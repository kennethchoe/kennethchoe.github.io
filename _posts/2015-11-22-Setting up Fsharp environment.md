---
layout: post
title: Bookmarks - Setting up F# environment
published: true
---

I spent some time on running F# on Windows (8.1 professional), OSX (El Capitan) and Linux (Ubuntu 14.04.3). Bottom line, Windows is the best environment for F# coding.

* Windows with Visual Studio Community edition is the best.
  * ReSharper supports detection of F# NUnit tests, C# NUnit tests and C# xUnit tests (with extension). It does not support F# xUnit tests.
* [Atom](https://atom.io/) 1.2.3 with [ionide-installer](http://ionide.io/) 1.3.0 did not work well on both OSX and Linux.
* Linux can use [MonoDevelop](http://www.monodevelop.com/), which is way better than Atom + ionide.
* OSX can use [Xamarin Studio](http://www.monodevelop.com/download/), which is variation of MonoDevelop. 
  * xUnit.NET testing framework support add-in installs well on Xamarin Studio but not on MonoDevelop.
* [Visual Studio Code](https://code.visualstudio.com/) is rather an editor than IDE. For debugging you need to run the app under debugging mode and then use Visual Studio Code to attach to the process.
* As long as you make the fsproj file to use .Net framework that is supported by Mono, the code is compilable on all three environments.