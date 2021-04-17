---
author: Sébastien Levert
categories:
- Angular
- SharePoint Online
- SharePoint Framework
- Angular Elements
date: "2017-12-04T22:30:58Z"
description: ""
draft: false
slug: sharepoint-framework-angular-elements-mocking-data
tags:
- Angular
- SharePoint Online
- SharePoint Framework
- Angular Elements
title: 'SharePoint Framework & Angular Elements : Mocking your data'
---


![](/content/images/2017/12/NgElements---Mocking-1.jpg)

You now have access to data directly in your browser! It's amazing and that will make you successful building great experiences to your connected data within SharePoint! Though, I really think that one of the most amazing pieces of the SharePoint Framework is to be able to test and execute it in a local environment. To support those scenarios, it is important to be able to give a representation of your data to simplifying the development experience. This blog post is all about mocking your data using the Angular mechanisms.

*Angular Elements and SharePoint Framework are still in their developments. This codes is based on pre-release of Angular Elements and a very "rough" integration with the SharePoint Framework. I am still pushing those samples so you can start playing with the technology. Please don't use any of this in production as of today. I will update the posts and code when better support will be offered by both Angular and SharePoint Framework.*

This post is part of a series of post on integrating the SharePoint Framework with Angular Elements

* [SharePoint Framework & Angular Elements : Building your first Web Part](http://sebastienlevert.com/2017/12/02/sharepoint-framework-angular-elements-building-your-first-web-part/)
* [SharePoint Framework & Angular Elements : Connecting to your SharePoint data](http://sebastienlevert.com/2017/12/03/sharepoint-framework-angular-elements-connecting-data/)
* [SharePoint Framework & Angular Elements : Mocking your data (this post)](http://www.sebastienlevert.com/2017/12/04/sharepoint-framework-angular-elements-mocking-data/)
* [SharePoint Framework & Angular Elements : Using Google Material Design](http://sebastienlevert.com/2017/12/05/sharepoint-framework-angular-elements-material-design/)
* [SharePoint Framework & Angular Elements : Using Office UI Fabric Core](http://www.sebastienlevert.com/2017/12/05/sharepoint-framework-angular-elements-office-ui-fabric-core/)

## Build the Service(s)

A concept that is important in Angular is the ability to inject Services in several Componets. Those services may have the responsibility drive the connection with external data. It keeps all the data layer logic within it and acts like a proxy to your data. This service will be the key to our mocking (and real data) implementation.

We will deploy two different instances of that service. One that will connect to actual data and the other one will generate a set of predefined data for testing / development purposes.

At the root or your `src` folder, create a folder called `services` and create 2 sub-folders; One named `interfaces` and the other one `mock`. The first file to build is the interface that will instruct all the different services what are the necessary options and actions available on that service.

<script src="https://gist.github.com/sebastienlevert/75a8e57021e972052047852b6f43f37d.js"></script>

Then we will create the actual data service that will connect to the SharePoint data. We will use the exact same method we used in our first sample where we connected to SharePoint to get the set of lists available on the site but we will wrap this call in a simple method. We will make sure it inherites from `IListsService` so all calls will be standard. Note the `@Injectable` decorator on the class: This will be the key to inject it wherever we want, when we need it in our Angular application.

<script src="https://gist.github.com/sebastienlevert/550ff259ee3ea6d01b45d4bb0955d587.js"></script>

To finish with the services, we will build another service that will inherit from the same `IListsService` but will return dummy / static data. 

<script src="https://gist.github.com/sebastienlevert/cdf896caa2d843f9ef5953f0f9685601.js"></script>

## Injecting the service

In our main Component class, we will need to tell Angular that we will want an instance of the Service on every new instance of the Componet. By leveraging dependency injection, we will be able to add a new parameter to our class constructor and magically, this service will be available in the methods of our class!

<script src="https://gist.github.com/sebastienlevert/e9859b9f00f2d7117587e19ee6a13e81.js"></script>

## Switching from real data to mock data

In our scenario, we see that the code will always refer to the `ListsService` class. This simplifies a lot the code so we don't have to switch everywhere based on context. To enable the `MockListsService` to be loaded when we are in development / testing mode, we will use a provider that will dynamically bootstrap the appropriate Service based on the context. In our case, if we are running in the local SharePoint Workbench, we will use the `MockListService` and for any other use case, we'll use the real implementation.

<script src="https://gist.github.com/sebastienlevert/d678b633888625a6e64fb2b147aa2419.js"></script>

## Run your webpart

Once your Web Part is ready, you can finally run it using `gulp serve` and see the result directly in your SharePoint Workbench! Test is by comparing it using the local SharePoint Workbench and the hosted SharePoint Workbench. You will see the difference right away!

![](/content/images/2017/12/angular-mock-data.gif)

## Enjoy!

Now that you can run your webpart using the local SharePoint Workbench in a fully offline mode, you will be able to move on and build more complex scenario!

## Resources

* [My personal repo with all SharePoint Framework + Angular Elements WebParts](https://github.com/sebastienlevert/spfx-ng-webparts/tree/master/spfx-ng-mock-data)