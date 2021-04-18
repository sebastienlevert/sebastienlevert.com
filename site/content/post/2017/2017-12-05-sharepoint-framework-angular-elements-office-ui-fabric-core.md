---
authors:
  - sebastien-levert
date: '2017-12-05T19:34:36Z'
description: ''
draft: false
images:
  - /images/2017/12/NgElements---Fabric.jpg
slug: sharepoint-framework-angular-elements-office-ui-fabric-core
title: 'SharePoint Framework & Angular Elements : Using Office UI Fabric Core'
---

One of the great feature of the SharePoint Framework ecosystem is the availability of the
[Office UI Fabric](https://dev.office.com/fabric). What is great with this set of components is that it is easy to
include in Office applications (client, web) and in SharePoint as it shares the same UI philosophy and it simplifies the
development of those experiences.

_Angular Elements and SharePoint Framework are still in their developments. This codes is based on pre-release of
Angular Elements and a very "rough" integration with the SharePoint Framework. I am still pushing those samples so you
can start playing with the technology. Please don't use any of this in production as of today. I will update the posts
and code when better support will be offered by both Angular and SharePoint Framework._

This post is part of a series of post on integrating the SharePoint Framework with Angular Elements

- [SharePoint Framework & Angular Elements : Building your first Web Part](/2017/12/02/sharepoint-framework-angular-elements-building-your-first-web-part/)
- [SharePoint Framework & Angular Elements : Connecting to your SharePoint data](/2017/12/03/sharepoint-framework-angular-elements-connecting-data/)
- [SharePoint Framework & Angular Elements : Mocking your data](/2017/12/04/sharepoint-framework-angular-elements-mocking-data/)
- [SharePoint Framework & Angular Elements : Using Google Material Design](/2017/12/05/sharepoint-framework-angular-elements-material-design/)
- [SharePoint Framework & Angular Elements : Using Office UI Fabric Core (this post)](/2017/12/05/sharepoint-framework-angular-elements-office-ui-fabric-core/)

## We have bad news...

As of today, it is not possible to use the Office UI Fabric Core to really fully take advantage of everything it has to
offer. It can work with its Grid System and all the typography which is a good start... But the top reason to use Office
UI Fabric is certainly its theming capabilities...

The way it currently works is that your themes are being replaced at runtime using a `loadStyles` method inside SPFx...
That method knows which styles to change and to apply (primary colors, secondary colors, etc.). But if it is embeded
inside an Angular application, SPFx doesn't know anything about it and just skips it. Leaving a white feel to all the
controls your will build. For now, I would recommend to continue using your own styling or Angular Material for SPFx
work using Angular Elements.

But be sure that I will update this post as soon as new development is possible using Angular and Office UI Fabric Core!

## Resources

- [My personal repo with all SharePoint Framework + Angular Elements WebParts](https://github.com/sebastienlevert/spfx-ng-webparts/tree/master/spfx-ng-mock-data)
