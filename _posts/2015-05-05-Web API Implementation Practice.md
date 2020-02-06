---
layout: post
title: "Web API Implementation Practice in Micro-Service Architecture"
published: true
---

In the system I work on these days, one service needs to call another service to get data and it is done through web API. If the service is used by multiple services, client portion for the service is repeated on every consumers.

This became a problem.

1. Duplication of API client codes on all consumers
2. The contract between service and client is URL signature: very loose
3. Hard to make mock: to test consumer code, you need to make mock service using actual web server

To solve all these problems, the following practice is suggested.

Step 1. The module that provides service will also provide client interface and DTO of the client code in DLL, namely [ModuleName].Client.Core.DLL. Consuming modules' Core may reference the Client.Core.DLL.

	{% highlight csharp %}
	namespace MyService.Client.Core
	{
		public interface IMyServiceClient
		{
			List<MyRecord> GetRecords();
		}
	 
		public class MyRecord
		{
			public int Id { get; set; }
			public string Name { get; set; }
		}
	}
	{% endhighlight %}

Step 2. The servicing module also provides client code itself in a separate .NET DLL, namely [ModuleName].Client.DLL.

	{% highlight csharp %}
	namespace MyService.Client
	{
		public class MyServiceClient : IMyServiceClient
		{
			private readonly string _baseAddress;
			public MyServiceClient(string baseAddress)
			{
				_baseAddress = baseAddress;
			}
			public List<MyRecord> GetRecords()
			{
				var result = new List<MyRecord>();
				using (var client = new HttpClient())
				{
					client.BaseAddress = new Uri(_baseAddress);
					client.DefaultRequestHeaders.Accept.Clear();
					client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
					var response = client.GetAsync("api/myrecords").Result;
					if (response.IsSuccessStatusCode)
					{
						var myRecords = response.Content.ReadAsAsync<IEnumerable<MyRecord>>().Result;
						result = (List<MyRecord>)myRecords;
					}
				}
				return result;
			}
		}
	}
	{% endhighlight %}

Step 3. Client.Core.DLL will also contain mock class, mock base class, or mock helper class as needed.

	{% highlight csharp %}
	namespace MyService.Client.Core.Mocks
	{
		public class MyServiceClientMock : IMyServiceClient
		{
			public List<MyRecord> GetRecords()
			{
				var result = new List<MyRecord>
				{
					new MyRecord {Id = 1, Name = "Record 1"},
					new MyRecord {Id = 2, Name = "Record 2"}
				};
				return result;
			}
		}
	}
	{% endhighlight %}

Step 4. The servicing module's client artifact (MyService.Client.Core.dll and MyService.Client.dll) is distributed to all the consuming services. We use in-house version of NuGet-like service for automatic deployment. You can do this in any ways you want but make sure it is auto-deployed.

Step 5. The consuming service uses the DLLs from servicing module as-is. All you do is to inject baseAddress.

	{% highlight xml %}
	<configuration>
	  <appSettings>
		<add key="MyServiceBaseAddress" value="http://localhost/MyService/" />
	  </appSettings>
	</configuration>
	{% endhighlight %}

	{% highlight csharp %}
	namespace ConsumingApp.UI.Configuration
	{
		public static class Bootstrapper
		{
			//////
			private static void ConfigureStructureMap()
			{
				//////
				ObjectFactory.Initialize(x =>
					//////
					x.For<IMyServiceClient>().Use<MyServiceClient>()
					.Ctor<string>("baseAddress")
					.Is(a => ConfigurationManager.AppSettings["MyServiceBaseAddress"]);
					//////
				});
				//////
			}
			//////
		}
	}
	{% endhighlight %}


The benefits of this approach are:

1. No duplicate api client codes on all consumers.
2. Strong-typed implementation of client code.
3. Mock class base is provided by the service module, conforming to the api client interface.
4. Contract is enforced by the service and respected by client apps (will raise compiler/build error if broken)
