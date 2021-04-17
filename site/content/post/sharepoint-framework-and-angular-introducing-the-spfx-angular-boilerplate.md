---
author: Sébastien Levert
categories:
- Angular
- SharePoint Framework
- SharePoint Online
- Office365Dev
date: "2017-07-31T17:04:00Z"
description: ""
draft: false
image: /images/2017/07/SharePoint-Framework-and-Angular---Introducing-the-SPFx-Angular-Boilerplate-1.jpg
slug: sharepoint-framework-and-angular-introducing-the-spfx-angular-boilerplate
tags:
- Angular
- SharePoint Framework
- SharePoint Online
- Office365Dev
title: SharePoint Framework and Angular - Introducing the SPFx Angular Boilerplate
---


![](/content/images/2017/07/SharePoint-Framework-and-Angular---Introducing-the-SPFx-Angular-Boilerplate.jpg)

So… This had to happen. Being an Office Developer and using Angular as my main UI framework, I really wanted to find a solution for supporting Angular in the modern SharePoint development toolchain, the SharePoint Framework. I've been starting my journey where Microsoft left (the [angular2-prototype](https://github.com/SharePoint/sp-dev-fx-webparts/tree/master/samples/angular2-prototype) proof-of-concept that was created by some of the Microsoft folks) and made sure we can improve it to make it usable and supportable in the long run.

I've been doing a session (SharePoint Framework, Angular & Azure Functions : The modern SharePoint developer tool belt) at many events in the last couple of months and I've been presenting my work on that specific topic to a lot of excited developers that can now work in their favorite UI framework without breaking everything in any of the SharePoint experiences. After multiple discussions with the community (a huge thanks to [Andrew Connell](https://www.twitter.com/andrewconnell) for helping me understand better the challenges of Angular in the SharePoint Framework and for doing tests and review of the solution and to [Marc D. Anderson](https://www.twitter.com/sympmarc) for the different discussions around the general client-side development chaos with the SharePoint Framework), I think this solution is ready for showtime!

##Introducing the SharePoint Framework Angular Boilerplate

Today, I introduce the SharePoint Framework Angular Boilerplate, a sample project that showcases a simple Angular webpart with all the necessary components to make it work and to be fully supported! This first blog post of a series will highlight how to get started with that project and how to run the sample on your own environment. 

This series will contain the following posts :

* SharePoint Framework and Angular - Introducing the SPFx Angular Boilerplate (this post)
* [SharePoint Framework and Angular - Walkthrough of the SPFx Angular Boilerplate](http://sebastienlevert.com/2017/08/01/sharepoint-framework-and-angular-walkthrough-of-the-spfx-angular-boilerplate/)
* ~~SharePoint Framework and Angular - Build your first webpart (coming soon...)~~
* ~~SharePoint Framework and Angular - Build your second webpart (coming soon...)~~
* ~~SharePoint Framework and Angular - Contribute and provide feedback (coming soon...)~~
* [I'm killing the SPFx Angular Boilerplate... And it's a good thing!](http://sebastienlevert.com/2017/12/01/killing-the-spfx-angular-boilerplate/)

##Getting started

Getting started with Angular and the SharePoint using the Boilerplate is very simple. First of all, close or fork the [Github project](https://github.com/sebastienlevert/spfx-angular-boilerplate). To clone the project and install all the necessary dependencies, execute the following commands in your environment :

<script src="https://gist.github.com/sebastienlevert/c55cbdfa4244380f3ba03465766ada1e.js"></script>

The sample that you are running is very basic. It allows to render all the items (Id and Tile) of a specified list (by its name). I works on a local Workbench using mocked data and it works on the hosted Workbench using real data.

##Running the sample

Once you have a browser running the local SharePoint Workbench, please add the "BasicAngular" webpart to your authoring canvas and see the result. You will see an Angular application running and you should be able to configure it through the editor panel by changing the title of the webpart and the list name. Once the list name is configured, you should see mocked results being rendered in your interface. At this point, to prove that the we can leverage multiple Angular webparts on the same page, you can add a second webpart and change its title. Both webpart will have a different configuration and will be completely isolated.

![](/content/images/2017/07/angular-boilerplate-local-workbench-1.gif)

Then, you can user your hosted Workbench and do the exact same steps you did previously. The only difference will be that the content will be fetched from an existing SharePoint list (make sure you use a list that has data and that exists).

![](/content/images/2017/07/angular-boilerplate-hosted-workbench.gif)

##What's next?

Isn't it cool? I find it amazing that we can finally run real code using Angular in the SharePoint Framework and that we can leverage the great features of Angular without compromises. 

This is not the end of the Angular story in the SharePoint Framework. We absolutely need the community to test, contribute and use the concepts illustrated in this sample solution. Your feedback will impact this solution as we really want to support all the nice and shiny features that Angular add to the developer tool belt.

In the next post, we will cover the general concepts used to make this solution work and how it solves the challenges of the SharePoint Framework!

Oh! And (finally), happy Angular coding on the SharePoint Framework!
