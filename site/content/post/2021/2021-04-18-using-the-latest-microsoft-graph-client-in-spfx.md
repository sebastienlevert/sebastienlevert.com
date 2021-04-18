---
authors:
  - sebastien-levert
categories: null
date: 2021-04-18T18:24:46.407Z
images:
  - /images/2021/04/UsingGraphClientSPFx.png
title: Using the latest microsoft-graph-client in SPFx
slug: latest-microsoft-graph-client-spfx
---

Lately, I've been looking at improving at understanding some of the challenges of developers when it comes to Microsoft
Graph development, especially in the OneDrive and SharePoint space, including the SharePoint Framework. When we are
looking at the simplicity that provides the SharePoint Framework when it comes to using the Microsoft Graph, it looks
amazing, maybe a little bit magical! And that is why I love this technology!

But a conversation sparked my interest and I started wondering if we could provide some guidances for developers that
are hitting some limits (read throttling) when it comes to Microsoft Graph.

{{< twitter 1382003257766121473 >}}

## A little backstory

The SharePoint Framework was released in 2017 and Microsoft Graph support in 2018 (it's been a while!) and the
underlying technology hasn't been updated. It works great when we are looking at simple scenarios (getting users profile
information, messages, events, etc.). But when we are hitting some of the Microsoft Graph limits, it doesn't provide the
latest and greatest of our SDK. To give you a clear example, SharePoint Framework is bundled with the 1.1 version of the
`microsoft-graph-client` and we are now at the version 2.2.1.

## Can I just update?

In our case, "just" updating the references to 2.2.1 won't be enough as foundations of the SDK changed a lot. And the
capabilitites we are trying to get are the ones that are coming with the v2 of the SDK (namely the Middleware layer)
that will help with ensuring a proper handling of throttling and also some more advanced scenarios... So, now what? Can
you still get the benefits of the latest and greatest... But in your SPFx? Yes, thanks to the
[Microsoft Graph Toolkit](https://aka.ms/mgt) providers!

## Microsoft Graph Toolkit to the rescue

Since the version 2 of the Microsoft Graph Toolkit, we can now import in our solution specific packages provided by the
toolkit. Maybe you don't want or need all the components, React wrappers, etc... This is why in our sample we will focus
only on `@microsoft/mgt-element` and `@microsoft/mgt-sharepoint-provider`.

## Getting our solution ready

In order to get our solution ready, you will need to update a couple of packages in your solution to match the
pre-requisites! To do so, you can execute the following command in your favorite console!

```bash
npm uninstall @microsof/rush-stack-compiler-3.3
npm install @microsoft/mgt-element @microsoft/mgt-sharepoint-provider @microsoft/microsoft-graph-client --save
npm install @microsoft/rush-stack-compiler-3.7 typescript@3.7 --save-dev
```

Ensure that your `tsconfig.json` points to the version 3.7 of the `rush-stack-compiler` and clear your `node_modules`
folder.

```json
{
  "extends": "./node_modules/@microsoft/rush-stack-compiler-3.7/includes/tsconfig-web.json",
  "compilerOptions": {
    //...
  }
  //...
}
```

> [Andrew Connel](https://twitter.com/andrewconnell) has a great
> [blog post](https://www.voitanos.io/blog/use-different-typescript-versions-in-sharepoint-framework-projects/) on how
> to ensure you have the right version of the compiler based on the version of Typescript you want to play with. I
> definitely recommend to have a look!

Finally, you will want to remove the `no-use-before-declare` rule from `tslint.json` as it's not being used since
Typescript 2.9 and you will get compiler errors when building.

Once all is done, just re-install all packages to make sure you have all the right resources, versions, etc.!

```bash
npm install
```

## Configuring your Web Part

You will need to do some light changes to your Web Part file `**WebPart.ts` in order to import the Microsoft Graph
Toolkit elements and initialize the providers to be able to leverage the Microsoft Graph client provided by MGT.

At the top of your Web Part file, add the following import statements :

```typescript
// Importing MGT in the Web Part
import { SharePointProvider } from '@microsoft/mgt-sharepoint-provider';
import { Providers } from '@microsoft/mgt-element';
```

And in the main Web Part class, override the default `OnInit` method by instanciating a new `SharePointProvider` and
assigning it to the global provider used by MGT.

```typescript
/**
 * Assign a new SharePoint provider to the MGT providers
 */
protected async onInit() {
  Providers.globalProvider = new SharePointProvider(this.context);
}
```

## Getting data from Microsoft Graph

Now that our solution is all setup for consuming Microsoft Graph data usign the latest and greatest of the
`microsoft-graph-client`, you will need to change some of your code to take advantage of the MGT provided Graph client
and not the one provided by the SharePoint Framework.

In order to do so, in the area where you want to leverage Microsoft Graph data, import the `Providers` from MGT and
leverage it's `api` method to execute your calls to Microsoft Graph!

```typescript
import { Providers } from '@microsoft/mgt-element';
```

```typescript
// Use this code wherever you need Graph data in your class / React component
const data = await Providers.globalProvider.graph.api('/me/events').get();
```

The real beauty here? This call will be done using the default list of middlewares, including a default `RetryHandler`
that should get your around throttling in a vast majority or the cases!

{{% notice note  %}} Note that we are not using the `this.context.msGraphClientFactory.getClient()` capabilty as this
would result in the 1.1.0 version of the `microsoft-graph-client`, therefore not getting the middleware list!
{{% /notice %}}

If we quickly compare both clients when doing the same call, you can

## Advanced configuration

Now that you have a nice and polish version of the Graph client, you can configure it to your needs when leveraging
Graph! For example, if we want to increase the delay and the number of retries that the client will do when it's being
throttled, you can use the following :

```typescript
// This retry up to 10 times with 5 seconds delays between each attempt
const data = await Providers.globalProvider.graph
  .api('/me/events')
  .middlewareOptions([new RetryHandlerOptions(5, 10)])
  .get();
```

## The code

The code will be made available on the SharePoint Framework Web Parts Samples repository but until it's
[merged](https://github.com/pnp/sp-dev-fx-webparts/pull/1826) (I'll update this post when it happens), you can see the
the code on my own repo
[here](https://github.com/sebastienlevert/sp-dev-fx-webparts/tree/sebastienlevert/react-graph-latest-client).

## Enjoy!

I hope this post is useful and will help you in your endeavours to create more SharePoint Framework solutions
integrating with the Microsoft Graph!
