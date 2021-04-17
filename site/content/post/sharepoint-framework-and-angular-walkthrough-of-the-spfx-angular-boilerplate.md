---
author: Sébastien Levert
categories:
- Angular
- Office365Dev
- SharePoint Online
- SharePoint Framework
date: "2017-08-01T18:00:00Z"
description: ""
draft: false
image: /images/2017/08/SharePoint-Framework-and-Angular---Walkthrough-of-the-SPFx-Angular-Boilerplate-1.jpg
slug: sharepoint-framework-and-angular-walkthrough-of-the-spfx-angular-boilerplate
tags:
- Angular
- Office365Dev
- SharePoint Online
- SharePoint Framework
title: SharePoint Framework and Angular - Walkthrough of the SPFx Angular Boilerplate
---


![](/content/images/2017/08/SharePoint-Framework-and-Angular---Walkthrough-of-the-SPFx-Angular-Boilerplate.jpg)
In this post, we will cover the general concepts used to make the [SharePoint Framework Angular Boilerplate](https://github.com/sebastienlevert/spfx-angular-boilerplate) work and how it solves the challenges that were preventing the production use of both technologies together.

This post is part of a series that contains the following posts :

* [SharePoint Framework and Angular - Introducing the SPFx Angular Boilerplate](http://www.sebastienlevert.com/2017/07/31/sharepoint-framework-and-angular-introducing-the-spfx-angular-boilerplate)
* SharePoint Framework and Angular - Walkthrough of the SPFx Angular Boilerplate (this post)
* ~~SharePoint Framework and Angular - Build your first webpart (coming soon...)~~
* ~~SharePoint Framework and Angular - Build your second webpart (coming soon...)~~
* ~~SharePoint Framework and Angular - Contribute and provide feedback (coming soon...)~~
* [I'm killing the SPFx Angular Boilerplate... And it's a good thing!](http://sebastienlevert.com/2017/12/01/killing-the-spfx-angular-boilerplate/)

##Getting Angular in the project
First of all, we had to add Angular to the project. As you can see in the <code>package.json</code>, we added all the Angular related modules and its dependencies.

<script src="https://gist.github.com/sebastienlevert/ad54fd718fe931ae974b5ba0f14ad765.js"></script>

##Fixing some complaints of the bundling process

Then, some complaints from the webpack bundler started to happen during the bundling process of the project. We had to add a setting to the <code>tsconfig.json</code> file to skip the library check. That prevents TypeScript transpilation error and will "assume" that all the libraries are valid. The resulting tsconfig.json is the following.

<script src="https://gist.github.com/sebastienlevert/59760c26127328df01d9941850163ca3.js"></script>

Then, we have to instruct webpack of where the application source files are located. That was done through the <code>gulpfile.js</code>. Adding the ContextReplacementPlugin was necessary to fix the request dependency bug in Angular [5644:15-36 Critical dependency: the request of a dependency is an expression](https://github.com/angular/angular/issues/11580). The resulting <code>gulpfile.js</code> is the following.

<script src="https://gist.github.com/sebastienlevert/7f14d8e822f46412556bdbd5703c8894.js"></script>

The last piece that needs to happen is to externalize the Zone.js component of the bundle. This is ==*EXTREMELY IMPORTANT*==. Without this, the code will work without any problem but will break as soon as another webpart built with Angular but from another project will be dropped on the same page as yours. The key to this boilerplate is to keep a single Zone.js instance to be loaded so all the webparts share the same.

To do that, we had to modify the <code>config\config.json</code> file and add the following to the <code>externals</code> section.

<script src="https://gist.github.com/sebastienlevert/d2e6d4f957dacd7a8f88c88f1af77473.js"></script>

The project is now ready to build webparts using Angular as everything will be bundled and will allow multiple webparts, multiple instances of the same webparts and multiple webparts from multiple projects!

## Using the BaseAngularWebPart
There is a lot of magic happening in the BaseAngularWebPart. This class is responsible for 3 major things. First of all, it renders the webparts by specifying its "DOM" element to be dynamic for every webpart instance running. The following snippet highlights this method.

<script src="https://gist.github.com/sebastienlevert/d36fa8fcac22dfcc1fa93ba367eb1be6.js"></script>

Then, once the "DOM" element is rendered, we want to bootstrap our NgModule to take over and render the actual set of components. The following snippet does it using the Zone.js component and dos a dynamic bootstrapping of our module.

<script src="https://gist.github.com/sebastienlevert/c834ca62ee531794d62bd2b83d6abc5d.js"></script>

Finally, we need to find the Root Component that will be used and create the NgModule with all its required settings and dependencies. The actual SharePoint Framework WebPart will instruct the BaseAngularWebPart of which of the set of components is the Root Component, which are the Providers, which are the Sub Components and what are the Routes to bundle in the Application.

<script src="https://gist.github.com/sebastienlevert/0b417f1b296b8f57c61f389ecc6c1450.js"></script>

With that class, any SharePoint Framework WebPart can be coded using a set of Angular components.

## Inheriting from the BaseAngularWebPart
The only requirement is to inherit from the BaseAngularWebPart and to instruct the Framework of some of the required elements to be used within the previous logic. First of all, it is required to inherit from the BaseAngularWebPart and specify in there what will be the base Application Component.

<script src="https://gist.github.com/sebastienlevert/a7fda0179661647e97a76445cd2ed242.js"></script>

Then, we need to return a set of properties that will be used by the BaseAngularWebPart abstract class so it can build a proper NgModule. You will not be able to build your WebPart without those as they are abstract methods to override.

<script src="https://gist.github.com/sebastienlevert/3217ba7d2988b47bcf34dca32602342f.js"></script>

## Configuring your routes

To use a generic approach for your components and be as isolated from the SharePoint Framework as possible, the management of components (what to load when) is done through the Angular routing system. Providing the routing ahead of time will allow us to not only have a great navigation between components, but also to write all of our components with Angular code only. Nothing in the components will have a reference to the SharePoint Framework because of that.

<script src="https://gist.github.com/sebastienlevert/bdaba3ae84738f32297c90758472ab6a.js"></script>

## Creating your Application Component

Finally, the only missing piece is the Root Application Component that has to be created and that is an empty component with a simple router outlet. That will allow us to have a full routing system that manages our components and will keep our Angular code totally separated from the SharePoint Framework code.

<script src="https://gist.github.com/sebastienlevert/2b5b79b00023d1aaec6ef5772b2c1503.js"></script>

## Video Walkthrough

<iframe width="560" height="315" src="https://www.youtube.com/embed/u5pIyiY_U14" frameborder="0" allowfullscreen></iframe>

## What's next?

That's it? It's that "simple"? Well... Surprisingly, it was not that much complex to get it working and the result is exactly what we're looking for : Coding Angular Components in a SharePoint Framework Webpart!

In the next post, we will create your first SharePoint Framework Solution using Angular!

Oh! And (finally), happy Angular coding on the SharePoint Framework!