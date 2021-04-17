---
author: Sébastien Levert
categories:
- Angular
- Angular Elements
- SharePoint Online
- SharePoint Framework
date: "2017-12-03T23:18:00Z"
description: ""
draft: false
slug: sharepoint-framework-angular-elements-connecting-data
tags:
- Angular
- Angular Elements
- SharePoint Online
- SharePoint Framework
title: 'SharePoint Framework & Angular Elements : Connecting to your SharePoint data'
---


![](/content/images/2017/12/NgElements---Connecting.jpg)

Your first webpart now works but how cool would it be if you could leverage some data that exists in your Office 365 data? If you're thinking like me, you're at the right spot!

*Angular Elements and SharePoint Framework are still in their developments. This codes is based on pre-release of Angular Elements and a very "rough" integration with the SharePoint Framework. I am still pushing those samples so you can start playing with the technology. Please don't use any of this in production as of today. I will update the posts and code when better support will be offered by both Angular and SharePoint Framework.*

This post is part of a series of post on integrating the SharePoint Framework with Angular Elements

* [SharePoint Framework & Angular Elements : Building your first Web Part](http://sebastienlevert.com/2017/12/02/sharepoint-framework-angular-elements-building-your-first-web-part/)
* [SharePoint Framework & Angular Elements : Connecting to your SharePoint data (this post)](http://sebastienlevert.com/2017/12/03/sharepoint-framework-angular-elements-connecting-data/)
* [SharePoint Framework & Angular Elements : Mocking your data](http://www.sebastienlevert.com/2017/12/04/sharepoint-framework-angular-elements-mocking-data/)
* [SharePoint Framework & Angular Elements : Using Google Material Design](http://sebastienlevert.com/2017/12/05/sharepoint-framework-angular-elements-material-design/)
* [SharePoint Framework & Angular Elements : Using Office UI Fabric Core](http://www.sebastienlevert.com/2017/12/05/sharepoint-framework-angular-elements-office-ui-fabric-core/)

##Getting started

First of all, make sure you read the [Building your first Web Part](http://sebastienlevert.com/2017/12/02/sharepoint-framework-angular-elements-building-your-first-web-part/) post to understand how to get things started and have a working Web Part!

##Importing ZoneJS in your project

In that specific scenario, because of async nature of the calls you will make to SharePoint and to simplify the development experience, we will need to "embed" an important piece of Angular : ZoneJS. Everything can be done without (as it's not a required piece anymore) but it's easier to use it for demo purposes.

To include ZoneJS, you have to add it to `packages.json`, to the `wc-shim.js` and to the Custom Element you are building.

<script src="https://gist.github.com/sebastienlevert/ab0d60be42848c9a502a1cde152868c2.js"></script>

<script src="https://gist.github.com/sebastienlevert/01916eb5bf7f4ac1e49013f30eed1a80.js"></script>

<script src="https://gist.github.com/sebastienlevert/cf8ebf1a334f3deee95c4fdd2154a7e2.js"></script>

##Passing the context to your webpart

Ensure you have an `Input` property in your Component that accepts a `WebPartContext` and you will be able to transfer your WebPart context directly in your Component. That will simplify a lot of the calls as the `SPHttpClient` takes care of some of the authentication challenges that exist in the SharePoint Framework.

<script src="https://gist.github.com/sebastienlevert/3d85fc374c412551c5f2b3554c9310b0.js"></script>

You will also need to change the way to render your Component in your Web Part as you will need to pass in a complex object that would bot be supported as a simple custom element property.

<script src="https://gist.github.com/sebastienlevert/a9518400b9ca7ae317675bd423e72e94.js"></script>

##Querying SharePoint in your Component

Now that your have access to the `SPHttpClient` within your component, the only missing piece is to discuss with the SharePoint REST APIs! In this sample, we are only highlighting the lists that exist in the current Site, but you can be very creative here!

<script src="https://gist.github.com/sebastienlevert/6050ba5736001e160a83de45ce8162c2.js"></script>

##Show your data asynchronously in your template

The only missing piece is to display the fetched data from SharePoint in your Angular Template. Use the `*ngFor` instruction combined with the `| async` option to load asynchronous data and let Angular handle it!

<script src="https://gist.github.com/sebastienlevert/b523cb7fc587ac3230047b5c179bb50d.js"></script>

##Run your webpart

Once your Web Part is ready, you can finally run it using `gulp serve` and see the result directly in your SharePoint Workbench! 

![](/content/images/2017/12/angular-list-rest.gif)

##Enjoy!

Now that your webpart works, I'll let you play around and explore the different SharePoint REST APIs that you can leverage in those scenarios!

##Resources

* [My personal repo with all SharePoint Framework + Angular Elements WebParts](https://github.com/sebastienlevert/spfx-ng-webparts/tree/master/spfx-ng-sp-rest)