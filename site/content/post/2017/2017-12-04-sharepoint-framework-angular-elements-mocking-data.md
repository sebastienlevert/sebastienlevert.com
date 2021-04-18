---
authors:
  - sebastien-levert
categories:
  - Angular
  - SharePoint Online
  - SharePoint Framework
  - Angular Elements
date: '2017-12-04T22:30:58Z'
draft: false
images:
  - /images/2017/12/NgElements---Mocking-1.jpg
slug: sharepoint-framework-angular-elements-mocking-data
tags:
  - Angular
  - SharePoint Online
  - SharePoint Framework
  - Angular Elements
title: 'SharePoint Framework & Angular Elements : Mocking your data'
---

You now have access to data directly in your browser! It's amazing and that will make you successful building great
experiences to your connected data within SharePoint! Though, I really think that one of the most amazing pieces of the
SharePoint Framework is to be able to test and execute it in a local environment. To support those scenarios, it is
important to be able to give a representation of your data to simplifying the development experience. This blog post is
all about mocking your data using the Angular mechanisms.

_Angular Elements and SharePoint Framework are still in their developments. This codes is based on pre-release of
Angular Elements and a very "rough" integration with the SharePoint Framework. I am still pushing those samples so you
can start playing with the technology. Please don't use any of this in production as of today. I will update the posts
and code when better support will be offered by both Angular and SharePoint Framework._

This post is part of a series of post on integrating the SharePoint Framework with Angular Elements

- [SharePoint Framework & Angular Elements : Building your first Web Part](/2017/12/02/sharepoint-framework-angular-elements-building-your-first-web-part/)
- [SharePoint Framework & Angular Elements : Connecting to your SharePoint data](/2017/12/03/sharepoint-framework-angular-elements-connecting-data/)
- [SharePoint Framework & Angular Elements : Mocking your data (this post)](/2017/12/04/sharepoint-framework-angular-elements-mocking-data/)
- [SharePoint Framework & Angular Elements : Using Google Material Design](/2017/12/05/sharepoint-framework-angular-elements-material-design/)
- [SharePoint Framework & Angular Elements : Using Office UI Fabric Core](/2017/12/05/sharepoint-framework-angular-elements-office-ui-fabric-core/)

## Build the Service(s)

A concept that is important in Angular is the ability to inject Services in several Componets. Those services may have
the responsibility drive the connection with external data. It keeps all the data layer logic within it and acts like a
proxy to your data. This service will be the key to our mocking (and real data) implementation.

We will deploy two different instances of that service. One that will connect to actual data and the other one will
generate a set of predefined data for testing / development purposes.

At the root or your `src` folder, create a folder called `services` and create 2 sub-folders; One named `interfaces` and
the other one `mock`. The first file to build is the interface that will instruct all the different services what are
the necessary options and actions available on that service.

`interfaces/lists.service.ts`

```typescript
import { List } from '../../models/list';
import { WebPartContext } from '@microsoft/sp-webpart-base';

/**
 * Interface for Lists Service
 */
export interface IListsService {
  /**
   * Gets all the lists from the current Site
   */
  getLists(context: WebPartContext): Promise<List[]>;
}
```

Then we will create the actual data service that will connect to the SharePoint data. We will use the exact same method
we used in our first sample where we connected to SharePoint to get the set of lists available on the site but we will
wrap this call in a simple method. We will make sure it inherites from `IListsService` so all calls will be standard.
Note the `@Injectable` decorator on the class: This will be the key to inject it wherever we want, when we need it in
our Angular application.

`lists.service.ts`

```typescript
import { WebPartContext } from '@microsoft/sp-webpart-base';
import { Injectable } from '@angular/core';
import { IListsService } from './interfaces/lists.service';
import { List } from './../models/list';
import { SPHttpClient, HttpClientResponse } from '@microsoft/sp-http';

@Injectable()
export class ListsService implements IListsService {
  /**
   * Builds a list of lists
   */
  public getLists(context: WebPartContext): Promise<List[]> {
    return new Promise<List[]>((resolve, reject) => {
      context.spHttpClient
        .get(`${context.pageContext.web.absoluteUrl}/_api/web/lists`, SPHttpClient.configurations.v1)
        .then((res) => res.json())
        .then((res) => {
          resolve(res.value as List[]);
        })
        .catch((err) => console.log(err));
    });
  }
}
```

To finish with the services, we will build another service that will inherit from the same `IListsService` but will
return dummy / static data.

`lists.service.mock.ts`

```typescript
import { WebPartContext } from '@microsoft/sp-webpart-base';
import { Injectable } from '@angular/core';
import { IListsService } from './../interfaces/lists.service';
import { List } from '../../models/list';

@Injectable()
export class MockListsService implements IListsService {
  /**
   * Builds a Mocked list of lists
   */
  public getLists(context: WebPartContext): Promise<List[]> {
    return new Promise<List[]>((resolve, reject) => {
      setTimeout(
        () =>
          resolve([
            {
              Id: 'bf8ff17c-f808-4031-b627-fa8193122a58',
              Title: 'List 01',
            },
            {
              Id: '330f1a60-4c76-402f-a956-f6a7abea8062',
              Title: 'List 02',
            },
            {
              Id: '5807d99b-cee2-4337-b289-8433e0635a08',
              Title: 'List 03',
            },
          ]),
        300
      );
    });
  }
}
```

## Injecting the service

In our main Component class, we will need to tell Angular that we will want an instance of the Service on every new
instance of the Componet. By leveraging dependency injection, we will be able to add a new parameter to our class
constructor and magically, this service will be available in the methods of our class!

`data.ts`

```typescript
import { Component, NgModule, Input, ViewEncapsulation } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { CommonModule } from '@angular/common';
import { WebPartContext } from '@microsoft/sp-webpart-base';
import { ListsService } from '../../../services/lists.service';
import { List } from '../../../models/list';
import { Environment, EnvironmentType } from '@microsoft/sp-core-library';
import { MockListsService } from '../../../services/mock/lists.service';

@Component({
  selector: 'mock-data',
  templateUrl: './mock-data.html',
  styleUrls: ['./mock-data.scss'],
  encapsulation: ViewEncapsulation.None,
})
export class MockData {
  @Input() context: WebPartContext;
  public lists: Promise<List[]>;

  constructor(private listsService: ListsService) {}

  ngOnInit() {
    if (!this.context) {
      console.error('Please provide the context!');
      return;
    }

    this.lists = this.listsService.getLists(this.context);
  }
}
```

## Switching from real data to mock data

In our scenario, we see that the code will always refer to the `ListsService` class. This simplifies a lot the code so
we don't have to switch everywhere based on context. To enable the `MockListsService` to be loaded when we are in
development / testing mode, we will use a provider that will dynamically bootstrap the appropriate Service based on the
context. In our case, if we are running in the local SharePoint Workbench, we will use the `MockListService` and for any
other use case, we'll use the real implementation.

`data.module.ts`

```typescript
//...}

@NgModule({
  imports: [BrowserModule],
  declarations: [MockData],
  providers: [{ provide: ListsService, useFactory: ListsServiceFactory }],
  entryComponents: [MockData],
})
export class MockDataModule {
  ngDoBootstrap() {}
}

/**
 * Lists Service Factory that provides the right service based on the context of the running WebPart
 */
export function ListsServiceFactory() {
  if (Environment.type === EnvironmentType.Local) {
    return new MockListsService();
  } else if (Environment.type == EnvironmentType.SharePoint || Environment.type == EnvironmentType.ClassicSharePoint) {
    return new ListsService();
  }
}
```

## Run your webpart

Once your Web Part is ready, you can finally run it using `gulp serve` and see the result directly in your SharePoint
Workbench! Test is by comparing it using the local SharePoint Workbench and the hosted SharePoint Workbench. You will
see the difference right away!

{{< figure src="/images/2017/12/angular-mock-data.gif" >}}

## Enjoy!

Now that you can run your webpart using the local SharePoint Workbench in a fully offline mode, you will be able to move
on and build more complex scenario!

## Resources

- [My personal repo with all SharePoint Framework + Angular Elements WebParts](https://github.com/sebastienlevert/spfx-ng-webparts/tree/master/spfx-ng-mock-data)
