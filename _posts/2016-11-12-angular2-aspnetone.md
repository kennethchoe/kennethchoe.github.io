---
layout: post
title: Bookmarks - ASP.NET Core + Angular 2 template for Visual Studio
published: true
---

Dev Machine Setup
-----------------

Follow this instruction to create basic application: [http://blog.stevensanderson.com/2016/10/04/angular2-template-for-visual-studio/](http://blog.stevensanderson.com/2016/10/04/angular2-template-for-visual-studio/)

Deployment Server Setup
------------------

1. Install .NET Core 1.0.1 Runtime: go to [https://www.microsoft.com/net/download](https://www.microsoft.com/net/download) and download [Windows (Server Hosting) - .NET Core Installer](https://go.microsoft.com/fwlink/?LinkID=827547).
2. Install Node.JS and restart the server. [https://github.com/aspnet/Hosting/issues/861](https://github.com/aspnet/Hosting/issues/861)
3. Set up the website and app pool. The app pool is better be set to "No Managed Code".

Packaging and Deployment
--------------------

VS 2015 ASP.NET Core project uses `.xproj`, not `.csproj`. This works fine with `MSBuild /t:clean` and `/t:build`.

Compiling the solution with .xproj:
{% highlight powershell %}
$solutionPath = "$source_dir\$projectName.sln"
& "$base_dir\tools\nuget.exe" restore $solutionPath
msbuild /t:clean /v:q /nologo /p:Configuration=$projectConfig $solutionPath
msbuild /t:build /v:q /nologo /p:Configuration=$projectConfig $solutionPath
{% endhighlight %}

But it raises error on `/t:package`, complaining that it cannot find `(your project name).csproj`. The solution is to use `dotnet.exe`, which is used by Visual Studio when it publishes.

Main link to follow: [https://weblog.west-wind.com/posts/2016/Jun/06/Publishing-and-Running-ASPNET-Core-Applications-with-IIS](https://weblog.west-wind.com/posts/2016/Jun/06/Publishing-and-Running-ASPNET-Core-Applications-with-IIS)

Packaging command should change like this: (using PowerShell)

BEFORE:
{% highlight powershell %}
$projectConfig = "Release"
$webProjectPath = "$source_dir\$projectName\$projectName.csproj"
& msbuild /t:package /v:q /nologo /p:"Configuration=$projectConfig;PackageLocation=$package_dir\msdeploy\$projectName.zip" $webProjectPath
{% endhighlight %}
AFTER:
{% highlight powershell %}
$projectConfig = "Release"
$webProjectDir = "$source_dir\$projectName"
& "C:\Program Files\dotnet\dotnet.exe" publish "$webProjectDir" --framework netcoreapp1.0 --output "$package_dir\web" --configuration $projectConfig
{% endhighlight %}

Now deployment is to stop the website, copy over the content of `$package_dir\web`, and then start the website.

One thing I couldn't figure out
--------------------
When nuget.exe restore runs interactively, it restores ASP.NET Core files on Nuget cache (`C:\Users\username\.nuget\packages`), such as `microsoft.aspnetcore.angularservices`, along with other dependencies. But when Jenkins runs the same command, it restores other dependencies on `C:\windows\System32\config\systemprofile\.nuget\packages` but not ASP.NET Core files. As a result, `$package_dir\web` does not contain any dotnet core related files and web app fails to run.

Similar question was asked [here](http://stackoverflow.com/questions/34580599/building-a-net-core-app-via-command-line-so-that-it-works-on-a-machine-without) but it says that bug was resolved.

A workaround is to copy all the missing dependencies from `C:\Users\username\.nuget\packages` to `C:\windows\System32\config\systemprofile\.nuget\packages` manually. Once I did that, everything worked fine.