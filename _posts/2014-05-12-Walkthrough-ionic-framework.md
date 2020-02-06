---
layout: post
title: "Walkthrough - Ionic Framework, Barcode Scanner, Android, iOS"
published: true
---

### Goal

1. Build BarCode Scanner application on iOS and Android platforms.
2. Use cordova and ionic framework.
3. Use windows 8 64bit for Android development.
4. Use OSX for iOS development.
5. Keep the source code on bitbucket.org as Git. It must be sharable between windows and OSX.

### Before Starting...

* Cordova is engine. Phonegap is Adobe's productized package that wraps Cordova.
* Ionic Framework is comparable to Phonegap. It is built upon Cordova. It lays AngularJS framework for www content and use Angular directive to support ionic-specific directives.
* www folder content is generic across platform.
* When you install platform it contains path information which is OS-specific. (unix style vs DOS style) So you need to decide which OS to use per each platform. 

### Step 1: Install Android environment on Windows

1. Install JDK 7 or later. [http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)
2. Install Android SDK. Let's say we did on C:\Programs\Android\android-sdk. [http://developer.android.com/sdk/index.html?hl=sk](http://developer.android.com/sdk/index.html?hl=sk)
3. Execute ```C:\Programs\Android\android-sdk\AVD Manager.exe``` and create one Android Virtual Device.
4. Make sure Environment Variables are set properly.

System Variables:

- ANDROID_HOME: C:\Programs\Android\android-sdk\tools
- JAVA_HOME: C:\Program Files\Java\jdk1.7.0_40 (or wherever you installed)
- PATH should include: C:\Programs\Android\android-sdk\platform-tools;C:\Programs\Android\android-sdk\tools;

### Step 2: Install Cordova and Ionic Framework on Windows

1. Install nodejs for npm. [http://nodejs.org/](http://nodejs.org/)
2. Install ant.
	- I unzipped [http://www.apache.org/dist/ant/binaries/apache-ant-1.9.4-bin.zip](http://www.apache.org/dist/ant/binaries/apache-ant-1.9.4-bin.zip) under C:\Programs\apache-ant-1.9.4.
	- And then added C:\Programs\apache-ant-1.9.4\bin; to PATH.
3. Launch Command Prompt and run this. [http://ionicframework.com/docs/guide/installation.html](http://ionicframework.com/docs/guide/installation.html)
{% highlight bat %}
npm install -g cordova gulp ionic plugman
{% endhighlight %}
4. Build initial app
	- Cordova command line interface: [http://cordova.apache.org/docs/en/3.4.0/guide_cli_index.md.html#The%20Command-Line%20Interface](http://cordova.apache.org/docs/en/3.4.0/guide_cli_index.md.html#The%20Command-Line%20Interface)
  
{% highlight bat %}
C:\Repositories>ionic start MyApp blank
C:\Repositories>cd MyApp
C:\Repositories\MyApp>cordova platform add android
C:\Repositories\MyApp>cordova emulate android
{% endhighlight %}

You should see Ionic Blank Starter screen on Android Virtual Device window.

### Step 3: Test MyApp on Android device

1. Connect Android device to Windows 8 computer on USB port.
2. On Android device, enable USB Debugging.
3. On Windows 8 computer, run the following.

{% highlight bat %}
C:\Repositories\MyApp>cordova emulate android --device
{% endhighlight %}

You should see Ionic Blank Starter on your Android device.

### Step 4: Add Barcode Scanner plugin for android platform

1. Install BarcodeScanner on android platform.
{% highlight bat %}
C:\Repositories\MyApp>plugman install --plugins_dir plugins --platform android --project platforms/android --plugin https://github.com/wildabeast/BarcodeScanner
{% endhighlight %}

2. Modify index.html.
{% highlight html %}
<body ng-app="starter">

    <div ng-controller="MainController">
        <button id="startScan" class="button button-positive" ng-click="scan()">
            Scan
        </button>
        <div id="results" ng-bind="results" />
    </div>
</body>
{% endhighlight %}

3. Add js/MainController.js

{% highlight javascript %}
"use strict";

angular.module('starter').controller({
    MainController: ['$scope', 
        function ($scope) {
            $scope.results = "result here.";

            $scope.scan = function () {
                cordova.plugins.barcodeScanner.scan(
		            function (result) {
		                $scope.results = JSON.stringify(result);
		                $scope.$apply();
		            },
		            function (error) {
		                alert("Scanning failed: " + error);
		            }
	          );
        };
    }]
});
{% endhighlight %}

4. Test
{% highlight bat %}
C:\Repositories\MyApp>cordova emulate android --device
{% endhighlight %}

You should see Scan button on your Android device.

### Step 5: Debugging Android Device from Windows

1. Install weinre (WEb INspector REmote).
{% highlight bat %}
npm install -g weinre
{% endhighlight %}

2. Add INTERNET permission on MyApp\platforms\android\AndroidManifest.xml.
{% highlight xml %}
<?xml version='1.0' encoding='utf-8'?>
<manifest ...>
    ...
    <uses-permission android:name="android.permission.INTERNET" />
    ...
</manifest>
{% endhighlight %}

3. Launch weinre server. 
	* Put local IP address using --boundHost.
	* If 8080 is occupied, you can change it with --httpPort.
  * **Parameters are case sensitive.** If you put incorrect case, it won't raise error but just ignore the setting.
  
{% highlight bat %}
weinre --httpPort 8081 --boundHost 192.168.0.114
{% endhighlight %}

4. Add one line on index.html.
{% highlight html %}
<head>
    ...
    <script src="http://192.168.0.114:8081/target/target-script-min.js"></script>
</head>
{% endhighlight %}

5. Open browser window for [http://192.168.0.114:8081/client/](http://192.168.0.114:8081/client/).
6. Launch the app on Android device. The browser window is linked to the Android device.

### Step 6: Commit MyApp to BitBucket.org from Windows

1. Install git. [http://git-scm.com/downloads](http://git-scm.com/downloads)
2. Got to BitBucket.org and create a git repo called MyApp.
3. Run the following.
{% highlight bat %}
C:\Repositories\MyApp>git init
C:\Repositories\MyApp>git add .
C:\Repositories\MyApp>git commit -m "init"
C:\Repositories\MyApp>git remote add origin https://kennethc@bitbucket.org/kennethc/myapp.git
C:\Repositories\MyApp>git push -u origin --all
(Type Your Password)
{% endhighlight %}

### Step 7: Run it from OSX

1. Install git.
2. Install nodejs.
3. Install cordova, ionic, ios-deploy (launch the app on iOS device), ios-sim (launch the app on iOS simulator), and plugman.
{% highlight bat %}
npm install -g cordova ionic ios-deploy ios-sim plugman
{% endhighlight %}

4. Get the code from BitBucket.
{% highlight bat %}
git clone https://kennethc@bitbucket.org/kennethc/myapp.git
{% endhighlight %}

5. Open XCode, connect iOS device to OSX machine, and select "Use for development" using XCode Organizer.
[https://developer.apple.com/library/ios/recipes/xcode_help-devices_organizer/articles/provision_device_for_development-generic.html](https://developer.apple.com/library/ios/recipes/xcode_help-devices_organizer/articles/provision_device_for_development-generic.html)

6. Add ios platform, and run from iOS device.
{% highlight bat %}
cd myapp
cordova platform add ios
cordova emulate ios --device
{% endhighlight %}

7. Debugging works fine with weinre, but you can also use Safari's Web Inspector. [http://phonegap-tips.com/articles/debugging-ios-phonegap-apps-with-safaris-web-inspector.html](http://phonegap-tips.com/articles/debugging-ios-phonegap-apps-with-safaris-web-inspector.html)