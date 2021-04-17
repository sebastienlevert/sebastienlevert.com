---
author: Sébastien Levert
date: "2017-12-05T18:58:35Z"
description: ""
draft: false
slug: sharepoint-framework-angular-elements-material-design
title: 'SharePoint Framework & Angular Elements : Using Google Material Design'
---


![](/content/images/2017/12/NgElements---Material.jpg)

One of the great feature of Angular is the easiness to use third party libraries. There are tons of open source libraries that can make you more productive while you are writing your code. On of my favorite (especially because I simple can't make things pretty) is [Angular Material](https://material.angular.io), the Angular implementation of Google Material Design. And even better, it fits nicely with Angular Elements withing your SharePoint Framework webparts!

*Angular Elements and SharePoint Framework are still in their developments. This codes is based on pre-release of Angular Elements and a very "rough" integration with the SharePoint Framework. I am still pushing those samples so you can start playing with the technology. Please don't use any of this in production as of today. I will update the posts and code when better support will be offered by both Angular and SharePoint Framework.*

This post is part of a series of post on integrating the SharePoint Framework with Angular Elements

* [SharePoint Framework & Angular Elements : Building your first Web Part](http://sebastienlevert.com/2017/12/02/sharepoint-framework-angular-elements-building-your-first-web-part/)
* [SharePoint Framework & Angular Elements : Connecting to your SharePoint data](http://sebastienlevert.com/2017/12/03/sharepoint-framework-angular-elements-connecting-data/)
* [SharePoint Framework & Angular Elements : Mocking your data](http://www.sebastienlevert.com/2017/12/04/sharepoint-framework-angular-elements-mocking-data/)
* [SharePoint Framework & Angular Elements : Using Google Material Design (this post)](http://sebastienlevert.com/2017/12/05/sharepoint-framework-angular-elements-material-design/)
* [SharePoint Framework & Angular Elements : Using Office UI Fabric Core](http://www.sebastienlevert.com/2017/12/05/sharepoint-framework-angular-elements-office-ui-fabric-core/)


## Add the reference to Angular Material

First step in getting things ready for Google Material is to simply add the packages to your `dependencies` section of your `packages.json` so you can refer to the Angular Material packages inside your code. There are 2 new packages to reference :

* `"@angular/cdk": "^5.0.0-rc.2"`
* `"@angular/material": "^5.0.0-rc.2"`

<script src="https://gist.github.com/sebastienlevert/59520e5951363d7f2ea8b70bdec932e6.js"></script>

The only missing piece now is to install them in your `node_modules` folder. So go for it, run `npm install`!

Those 2 packages will add all the features you need within your application and you will be able to use a ton of the controls and components provided by the Material team.

## Using Angular Material in your Component

Here, the only code you will have to run is 100% Angular and has nothing to do with the SharePoint Framework! First, you will have to use the `mat-*` components in your templates to refer to the Angular Material components.

<script src="https://gist.github.com/sebastienlevert/1f103fa397a13a3bc0bf88a5820d0990.js"></script>

Then you will want to use the regular Material Designs themes in your SharePoint Framework components. How to do that? Using SCSS, you will be able to refer the Theming methods of Angular Material and build your own theme! Here's the theme I'm using that is mainly the blue / indigo version of Material Design!

<script src="https://gist.github.com/sebastienlevert/a149abe0f95cd8e7fd6435a3731c9ba8.js"></script>

The only missing piece is to make sure that your Angular component glues everything together!

<script src="https://gist.github.com/sebastienlevert/6deb676a2a1f3cecad5c37a7b298edcc.js"></script>

## Referencing Angular Material in your Module

All that automatic loading is all possible because of the Angular Module that instructs the bundler which packages to load at the bundle time. In our case, we are only using the `MatToolbarModule` but we would add all the different modules we are using in your Angular Module. Go on the [Angular Material](https://material.angular.io) to view all the APIs examples to know which module to load when!

<script src="https://gist.github.com/sebastienlevert/65ff9b6b5738fa46e0cf553bc7e28a55.js"></script>

## Run your webpart

Once your Web Part is ready, you can finally run it using `gulp serve` and see the result directly in your SharePoint Workbench! You will benefit from awesome UIs at a very low cost!

![](/content/images/2017/12/angular-material.gif)

## Enjoy!

Now that you can build your webpart using Angular Material, you will be able to build nice looking experiences for the SharePoint Framework!

## Resources

* [My personal repo with all SharePoint Framework + Angular Elements WebParts](https://github.com/sebastienlevert/spfx-ng-webparts/tree/master/spfx-ng-mock-data)