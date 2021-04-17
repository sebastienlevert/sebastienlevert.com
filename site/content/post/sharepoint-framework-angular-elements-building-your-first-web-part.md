---
author: Sébastien Levert
categories:
- Angular
- Angular Elements
- SharePoint Framework
date: "2017-12-02T21:34:00Z"
description: ""
draft: false
slug: sharepoint-framework-angular-elements-building-your-first-web-part
tags:
- Angular
- Angular Elements
- SharePoint Framework
title: 'SharePoint Framework & Angular Elements : Building your first Web Part'
---


![](/content/images/2017/12/NgElements---Building.jpg)

Now that you are excited by the introduction of Angular Elements, a Google technology that enables us, Enterprise Developers, to use Angular as a Development tool rather than a full blown framework, how cool would it be to start using it TODAY, using its current alpha/beta release? Well, you're at the right place!

*Angular Elements and SharePoint Framework are still in their developments. This codes is based on pre-release of Angular Elements and a very "rough" integration with the SharePoint Framework. I am still pushing those samples so you can start playing with the technology. Please don't use any of this in production as of today. I will update the posts and code when better support will be offered by both Angular and SharePoint Framework.*

This post is part of a series of post on integrating the SharePoint Framework with Angular Elements

* [SharePoint Framework & Angular Elements : Building your first Web Part (this post)](http://sebastienlevert.com/2017/12/02/sharepoint-framework-angular-elements-building-your-first-web-part/)
* [SharePoint Framework & Angular Elements : Connecting to your SharePoint data](http://sebastienlevert.com/2017/12/03/sharepoint-framework-angular-elements-connecting-data/)
* [SharePoint Framework & Angular Elements : Mocking your data](http://www.sebastienlevert.com/2017/12/04/sharepoint-framework-angular-elements-mocking-data/)
* [SharePoint Framework & Angular Elements : Using Google Material Design](http://sebastienlevert.com/2017/12/05/sharepoint-framework-angular-elements-material-design/)
* [SharePoint Framework & Angular Elements : Using Office UI Fabric Core](http://www.sebastienlevert.com/2017/12/05/sharepoint-framework-angular-elements-office-ui-fabric-core/)

##Getting started

The first step for getting started is to create your SharePoint Framework project and select the "No Framework" option. That will let us import our favorite framework (Angular Elements!) at a later time. To speed up the initialization of the project, use the `--skip-install` option to only deploy the necessary files. We'll run the npm install later when everything will be ready!

![](/content/images/2017/12/ScreenShot-20171203113353.png)

##Adding Angular Elements

Your project is now ready for Angular! How do we add Angular and Angular Elements to the mix? Well, it's pretty simple, thanks to their npm packages! Use the following `package.json` to make sure you get the proper dependencies and references in your project.

<script src="https://gist.github.com/sebastienlevert/9c0003efcd9d82549218e33ab59ea9cd.js"></script>

##Modifying your build process
To enable Angular Elements (and AOT compilation) in your project, you have to make some changes to your TypeScript and your build configurations. The following files should replace your `tsconfig.json` and `gulpfile.js` files! This will enable your solution to transpile your TypeScript to `ES2015` and will ask Angular to take care of the JS/TS packaging.

<script src="https://gist.github.com/sebastienlevert/cf83abbeba4f8d29e61dc6be72a0ec1b.js"></script>

<script src="https://gist.github.com/sebastienlevert/d15920072598ddcc55271d41c9cf9a95.js"></script>

##Add the Angular Elements plumbing
To use the magic provided by Angular Elements, it's important to lay out some foudations for any of your Web Parts that live in that solution. Because Angular Elements leverages the Web Components proposed standard, it's important to "shim" it in case the browser used does not work already with Web Components (I'm looking at you, Edge...) and also to build the basic Module that will take care of embedding you Angular components within that Web Component.

<script src="https://gist.github.com/sebastienlevert/5c78adb1dc5447bb60be8ec423310e36.js"></script>

<script src="https://gist.github.com/sebastienlevert/4aefe1e8cc268c6f6bd13435d15b23b7.js"></script>

##Building your Angular Component

Then, it's time for the actual work to happen. You need to create your Angular component using regular Angular code and you will want to tie it to you SharePoint Framework Web Part.

The necessary files for the component to work are its logic, its template and its styles files. Then, those files will be all mixed together and exported as a self-bootstrapped Web Component.

<script src="https://gist.github.com/sebastienlevert/ec0ae8469bc98112c41f85cdeead92c6.js"></script>

<script src="https://gist.github.com/sebastienlevert/266cc0b2b246b8d430eff38f2dde67ea.js"></script>

<script src="https://gist.github.com/sebastienlevert/f5796020743fdc510c27cb267904e078.js"></script>

<script src="https://gist.github.com/sebastienlevert/a7b05e55b81afba0ed95fb8c17ad16a0.js"></script>

##Configuring your WebPart

It's now the time inject that component in your SharePoint Framework Web Part. Using the HelloWorld class will make it available and will also act as your `this.properties` bag. Very clever and simple to use. You have to note 2 things. The usage of HelloWorld as the Properties type passed in the class declaration and the render method that simply embeds the custom element and passes in the `name` properties!

<script src="https://gist.github.com/sebastienlevert/bb96b13207cea7a1ff38bc78e52c5bdf.js"></script>

You will also have to make some light changes to your `config.json` file to indicate to Angular your new entry points and to specify your Module.

<script src="https://gist.github.com/sebastienlevert/e1a2db86732d1e5cc10ef5888f18d935.js"></script>

##Run your webpart

Once your Web Part is ready, you can finally run it using `gulp serve` and see the result directly in your SharePoint Workbench! 

![](/content/images/2017/12/angular-hello-world.gif)

##Enjoy!

Now that your webpart works, I'll let you play around and explore the joys of the SharePoint Framework with Angular Elements!

##Resources

* [Sample solution provided by the SharePoint team](https://github.com/SharePoint/sp-dev-fx-angular)
* [My personal repo with all SharePoint Framework + Angular Elements WebParts](https://github.com/sebastienlevert/spfx-ng-webparts/tree/master/spfx-ng-hello-world)