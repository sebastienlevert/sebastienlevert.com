---
authors:
  - sebastien-levert
categories:
  - AngularJS
  - Office 365
  - Office365Dev
  - SharePoint Online
  - Microsoft Graph
date: '2016-06-18T16:00:00Z'
description: ''
draft: false
images:
  - /images/2016/06/Introducing-OfficeHub.png
slug: introducing-the-officehub
tags:
  - AngularJS
  - Office 365
  - Office365Dev
  - SharePoint Online
  - Microsoft Graph
title: Introducing the OfficeHub
---

I've been doing lots of demos around a custom AngularJS application I built since a year that is called the OfficeHub.
This application gives you a pretty good example on how you can integrate yourself with the Microsoft Graph using
AngularJS 1.5. This blog posts will give you an overview of the application while the following posts are giving a
step-by-step guide on how you can get from nothing to a complete Single Page Application connecting to the Microsoft
Graph :

- AngularJS and the Microsoft Graph : Getting started with the OfficeHub
- AngularJS and the Microsoft Graph : A journey to the cloud
- AngularJS and the Microsoft Graph : Create your application
- AngularJS and the Microsoft Graph : Connect to the Graph
- AngularJS and the Microsoft Graph : Consume the Graph
- AngularJS and the Microsoft Graph : Make it look sexy

## What is it?

As of today, the OfficeHub is a Single Page Application allowing the user to navigate in and explorer its Office 365
content. The application currently integrate :

- Messages
- Files
- Videos

It could be used as a replacement for the Office 365 Portal but it is really an application to show the concepts of
building rich applications on top of the Microsoft Graph with a technology like AngularJS.

## The technology stack

First of all, the technology stack behind the solution is oriented on a modern development workflow. I wanted to make is
available on any platform and using the most recent web technologies available.

- NodeJS : Task runner enabling pretty much every other dependencies
- Bower : A component manager targeting JavaScript and CSS libraries
- Yeoman : Used as its scaffolding engine
- gulp-angular : Base scaffolding generator
- Visual Studio Code : IDE used to develop and store project specific settings
- jQuery : Because jQuery (!)
- AngularJS 1.5 : Base SPA framework
- ADAL : Azure Active Directory Authentication Library javascript implementation
- angular-adal : Specific AngularJS implementation of ADAL
- Office UI Fabric : CSS framework easing the layout of the application (because I'm lazy) and components
- ngOfficeUiFabric : AngularJS set of directives allowing an easy integration of the Office UI Fabric in views

## Overview and Getting started with the OfficeHub

For more information on how to get it, how to play with the code and how to make it run in your own environment, make
sure you clone it from my personal Github : https://github.com/sebastienlevert/officehub

## What are the next steps?

I really want to integrate every workloads of the Microsoft Graph into this application, but that might take a while.
But I promise I will get there! In the coming weeks / months, I plan on doing the following things :

- Add the ability to view a video directly from the OfficeHub
- Add the ability to explore your Office 365 groups, their messages and their files from the OfficeHub
- Add the ability to explore trending documents around you (Delve-like experience)
- Add a live site from where you would be able to use the OfficeHub with your own data
- Migrate the existing code base to ensure AngularJS 1.5 best practices
- Migrate the existing code base to Angular 2 when it ships

## Closing note

If you want to help on getting the OfficeHub better and richer that it is now, feel free to send a Pull Request on the
repo! It will be my pleasure to include any relevant code in the OfficeHub! I hope that my developer friends are finding
this valuable and that you will enjoy digging into the code of the application!

Happy coding!
