---
authors:
  - sebastien-levert
categories:
  - Angular
  - Office365Dev
  - SharePoint Online
  - SharePoint Framework
date: '2017-08-01T18:00:00Z'
description: ''
draft: false
image: /images/2017/08/SharePoint-Framework-and-Angular---Walkthrough-of-the-SPFx-Angular-Boilerplate-1.jpg
slug: sharepoint-framework-and-angular-walkthrough-of-the-spfx-angular-boilerplate
images:
  - /images/2017/08/SharePoint-Framework-and-Angular---Walkthrough-of-the-SPFx-Angular-Boilerplate.jpg
tags:
  - Angular
  - Office365Dev
  - SharePoint Online
  - SharePoint Framework
title: SharePoint Framework and Angular - Walkthrough of the SPFx Angular Boilerplate
---

In this post, we will cover the general concepts used to make the
[SharePoint Framework Angular Boilerplate](https://github.com/sebastienlevert/spfx-angular-boilerplate) work and how it
solves the challenges that were preventing the production use of both technologies together.

This post is part of a series that contains the following posts :

- [SharePoint Framework and Angular - Introducing the SPFx Angular Boilerplate](/2017/07/31/sharepoint-framework-and-angular-introducing-the-spfx-angular-boilerplate)
- SharePoint Framework and Angular - Walkthrough of the SPFx Angular Boilerplate (this post)
- ~~SharePoint Framework and Angular - Build your first webpart (coming soon...)~~
- ~~SharePoint Framework and Angular - Build your second webpart (coming soon...)~~
- ~~SharePoint Framework and Angular - Contribute and provide feedback (coming soon...)~~
- [I'm killing the SPFx Angular Boilerplate... And it's a good thing!](/2017/12/01/killing-the-spfx-angular-boilerplate/)

## Getting Angular in the project First of all, we had to add Angular to the project. As you can see in the

<code>package.json</code>, we added all the Angular related modules and its dependencies.

```json
{
  "name": "spfx-angular-boilerplate",
  "version": "0.0.1",
  "private": true,
  "engines": {
    "node": ">=0.10.0"
  },
  "dependencies": {
    "@angular/common": "^4.3.1",
    "@angular/compiler": "^4.3.1",
    "@angular/core": "^4.3.1",
    "@angular/http": "^4.3.1",
    "@angular/platform-browser": "^4.3.1",
    "@angular/platform-browser-dynamic": "^4.3.1",
    "@angular/router": "^4.3.1",
    "core-js": "^2.4.1",
    "reflect-metadata": "^0.1.9",
    "rxjs": "5.0.1",
    "systemjs": "0.19.40",
    "zone.js": "0.8.4",
    "@microsoft/sp-core-library": "~1.1.0",
    "@microsoft/sp-webpart-base": "~1.1.1",
    "@types/webpack-env": ">=1.12.1 <1.14.0",
    "sp-pnp-js": "^2.0.6"
  },
  "devDependencies": {
    "@microsoft/sp-build-web": "~1.1.0",
    "@microsoft/sp-module-interfaces": "~1.1.0",
    "@microsoft/sp-webpart-workbench": "~1.1.0",
    "gulp": "~3.9.1",
    "@types/chai": ">=3.4.34 <3.6.0",
    "@types/mocha": ">=2.2.33 <2.6.0"
  },
  "scripts": {
    "build": "gulp bundle",
    "clean": "gulp clean",
    "test": "gulp test"
  }
}
```

## Fixing some complaints of the bundling process

Then, some complaints from the webpack bundler started to happen during the bundling process of the project. We had to
add a setting to the <code>tsconfig.json</code> file to skip the library check. That prevents TypeScript transpilation
error and will "assume" that all the libraries are valid. The resulting tsconfig.json is the following.

```json
{
  "compilerOptions": {
    "target": "es5",
    "forceConsistentCasingInFileNames": true,
    "module": "commonjs",
    "jsx": "react",
    "declaration": true,
    "sourceMap": true,
    "skipLibCheck": true,
    "experimentalDecorators": true,
    "emitDecoratorMetadata": true,
    "types": ["es6-promise", "es6-collections", "webpack-env"]
  }
}
```

Then, we have to instruct webpack of where the application source files are located. That was done through the
<code>gulpfile.js</code>. Adding the ContextReplacementPlugin was necessary to fix the request dependency bug in Angular
[5644:15-36 Critical dependency: the request of a dependency is an expression](https://github.com/angular/angular/issues/11580).
The resulting <code>gulpfile.js</code> is the following.

```javascript
'use strict';

const gulp = require('gulp');
const path = require('path');
const build = require('@microsoft/sp-build-web');
const webpack = require('webpack');

/**
 * Fixing the "5644:15-36 Critical dependency: the request of a dependency is an expression" warning
 * Linked to an existing bug/problem in Angular https://github.com/angular/angular/issues/11580
 */
build.configureWebpack.mergeConfig({
  additionalConfiguration: (generatedConfiguration) => {
    generatedConfiguration.plugins.push(
      new webpack.ContextReplacementPlugin(/angular(\\|\/)core/, path.resolve(__dirname, './src'))
    );

    return generatedConfiguration;
  },
});

build.initialize(gulp);
```

The last piece that needs to happen is to externalize the Zone.js component of the bundle. This is **_EXTREMELY
IMPORTANT_**. Without this, the code will work without any problem but will break as soon as another webpart built with
Angular but from another project will be dropped on the same page as yours. The key to this boilerplate is to keep a
single Zone.js instance to be loaded so all the webparts share the same.

To do that, we had to modify the <code>config\config.json</code> file and add the following to the
<code>externals</code> section.

```json
{
  "entries": [
    {
      "entry": "./lib/webparts/basicAngular/BasicAngularWebPart.js",
      "manifest": "./src/webparts/basicAngular/BasicAngularWebPart.manifest.json",
      "outputPath": "./dist/basic-angular.bundle.js"
    }
  ],
  "externals": {
    "zone.js": {
      "path": "https://cdnjs.cloudflare.com/ajax/libs/zone.js/0.8.12/zone.min.js",
      "globalName": "zone.js"
    }
  },
  "localizedResources": {
    "basicAngularStrings": "webparts/basicAngular/loc/{locale}.js"
  }
}
```

The project is now ready to build webparts using Angular as everything will be bundled and will allow multiple webparts,
multiple instances of the same webparts and multiple webparts from multiple projects!

## Using the BaseAngularWebPart

There is a lot of magic happening in the BaseAngularWebPart. This class is responsible for 3 major things. First of all,
it renders the webparts by specifying its "DOM" element to be dynamic for every webpart instance running. The following
snippet highlights this method.

```typescript
/**
 * Render the web part. This causes the Angular2 app to be bootstrapped which
 * in turn bootsraps the Angular2 web part root component.
 */
public render(): void {
  // @todo: most likely we need to make this width:100%
  this.domElement.innerHTML = `<angular-${this.context.instanceId} />`;
  this._bootStrapModule();
}
```

Then, once the "DOM" element is rendered, we want to bootstrap our NgModule to take over and render the actual set of
components. The following snippet does it using the Zone.js component and dos a dynamic bootstrapping of our module.

```typescript
/**
 * Bootstrap the root component of the web part.
 */
private _bootStrapModule(): void {
  var self = this;
  platformBrowserDynamic().bootstrapModule(self._getModule()).then(
    ngModuleRef => {

      if(self._app["_rootComponents"] != undefined && self._app["_rootComponents"].length > 0) {
        self._component = self._app['_rootComponents'][0]['_component'] as KProperties;
        self._zone.run(() => { console.log('Outside Done!'); });
      }
    }, err => {
      console.log(err);
    }
  );
}
```

Finally, we need to find the Root Component that will be used and create the NgModule with all its required settings and
dependencies. The actual SharePoint Framework WebPart will instruct the BaseAngularWebPart of which of the set of
components is the Root Component, which are the Providers, which are the Sub Components and what are the Routes to
bundle in the Application.

```typescript
/**
 * Get the NgModule reference that will act as the root of this web part.
 */
private _getModule(): any {
  const component: any = this.rootComponentType.getComponent(this.context.instanceId);
  const declarations = this.appDeclarationTypes.concat(component);
  const routes = this.routes;
  const providers = this.providers;
  const webPart = this;
  /**
  * Our goal is to define a single module class definition to be instantiated for each
    webpart (like instances of a class). When an instance of the module class is bootstrapped Angular2
    will create an annotation and attach it to the module class. However, when multiple instances of the
    same module class are bootstrapped, only the first annotation associated with the module class will be parsed.
    This results in any other module class instances on the page to not function.
    To allow multiple modules of the same class definitoin on one page to work, we need to define the
    class in a closure to create a new environment for each instance class, so that each annotation
    object will be parsed.
  */
  const AppModule = (() => {
    function AppModule(applicationRef, ngZone) {
      webPart._app = applicationRef; // applicationRef gives us a reference to the Angular2 component's properties
      webPart._zone = ngZone;
    }
    // We now attach required metadata for Angular2 that is allowable within a clousure
    const AppModule1 = Reflect.decorate([
      NgModule({
        imports: [BrowserModule, FormsModule, HttpModule, routes],
        declarations: declarations,
        bootstrap: [component],
        exports: [RouterModule],
        providers: providers
      }),

      Reflect.metadata('design:paramtypes', [ApplicationRef, NgZone]) // This allows Angular2's DI to inject dependencies
    ], AppModule);
    return AppModule1;
  })();
  return AppModule;
}
```

With that class, any SharePoint Framework WebPart can be coded using a set of Angular components.

## Inheriting from the BaseAngularWebPart

The only requirement is to inherit from the BaseAngularWebPart and to instruct the Framework of some of the required
elements to be used within the previous logic. First of all, it is required to inherit from the BaseAngularWebPart and
specify in there what will be the base Application Component.

```typescript
// ...
export default class BasicAngularWebPart extends BaseAngularWebPart<IBasicAngularWebPartProps, AppComponent> {
  // ...
}
```

Then, we need to return a set of properties that will be used by the BaseAngularWebPart abstract class so it can build a
proper NgModule. You will not be able to build your WebPart without those as they are abstract methods to override.

```typescript
protected get rootComponentType(): any {
  return AppComponent;
}

protected get appDeclarationTypes(): any {
  return [
    HomeComponent,
    ListComponent
  ];
}

protected get routes(): any {
  return AppRoutes;
}

protected get providers(): any {
  if (Environment.type === EnvironmentType.Local) {
    return [
      ConfigurationService,
      { provide: ItemsService, useClass: MockItemsService },
      { provide: APP_INITIALIZER, useFactory: (configurationService: ConfigurationService) => () => configurationService.load({
        mocked: true,
        listName: this.properties.listName,
        description: this.properties.description,
        styles: styles
      }), deps: [ConfigurationService], multi: true }
    ];
  } else if (Environment.type == EnvironmentType.SharePoint || Environment.type == EnvironmentType.ClassicSharePoint) {
    return [
      ConfigurationService,
      { provide: ItemsService, useClass: ItemsService },
      { provide: APP_INITIALIZER, useFactory: (configurationService: ConfigurationService) => () => configurationService.load({
        mocked: false,
        listName: this.properties.listName,
        description: this.properties.description,
        styles: styles
      }), deps: [ConfigurationService], multi: true }
    ];
  }
}
```

## Configuring your routes

To use a generic approach for your components and be as isolated from the SharePoint Framework as possible, the
management of components (what to load when) is done through the Angular routing system. Providing the routing ahead of
time will allow us to not only have a great navigation between components, but also to write all of our components with
Angular code only. Nothing in the components will have a reference to the SharePoint Framework because of that.

```typescript
import { Routes, RouterModule } from '@angular/router';
import { HomeComponent } from './home';

const routes: Routes = [{ path: '', component: HomeComponent }];

export const AppRoutes: any = RouterModule.forRoot(routes, { useHash: true });
```

## Creating your Application Component

Finally, the only missing piece is the Root Application Component that has to be created and that is an empty component
with a simple router outlet. That will allow us to have a full routing system that manages our components and will keep
our Angular code totally separated from the SharePoint Framework code.

```typescript
import { Component } from '@angular/core';

export class AppComponent {
  public static getComponent(selectorId: string): any {
    return Component({
      selector: `angular-${selectorId}`,
      template: `<router-outlet></router-outlet>`,
    })(class AppComponentInner {});
  }
}
```

## Video Walkthrough

{{< youtube u5pIyiY_U14 >}}

## What's next?

That's it? It's that "simple"? Well... Surprisingly, it was not that much complex to get it working and the result is
exactly what we're looking for : Coding Angular Components in a SharePoint Framework Webpart!

In the next post, we will create your first SharePoint Framework Solution using Angular!

Oh! And (finally), happy Angular coding on the SharePoint Framework!
