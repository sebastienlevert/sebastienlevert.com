---
author:
  - sebastien-levert
categories:
  - SharePoint Online
  - Hubsite
  - Search
  - SharePoint Framework
date: '2018-03-27T02:42:00Z'
description: ''
draft: false
slug: getting-all-your-hub-content-using-search
images:
  - /images/2018/03/HubsiteSearch.png
tags:
  - SharePoint Online
  - Hubsite
  - Search
  - SharePoint Framework
title: Getting all your Hub content using Search
---

Yay!
[Hubsites](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Organize-your-intranet-with-SharePoint-hub-sites/ba-p/174081)
are out and we are very (very) excited! This new way or organizing your site content will be very useful to help in a
flat information architecture and the fact that some built-in features are built to roll-up content from all your
connected content is incredible!

## I need content from my Hubsite

Now, what about getting all this information from your own queries to roll-up your own content based on more complex
scenarios?

Well, my good friend and colleague [Elio Struyf](https://www.eliostruyf.com/) shipped a great little tool
([SharePoint REST API Tester](https://github.com/estruyf/spfx-rest-api-tester)) that made me think about some scenarios,
and I decided to give it a try! My quest was about : How can I find all the News that were coming directly from my
connected-sites into my Hubsite... I first tried to hack my way using the Chrome Network tab to understand the trafic
but realized this query is made through a pretty undocumented API and I really wanted to connect to my query using a
proper API.

## Behind the scenes

When you connect a SharePoint site to a Hub, the great thing that happens is that is add a Managed Property to all of
its content. The property is called **DepartmentId** and is automatically added to every piece of content (even lists
and libraries get that!) that sits in a site connected to a Hubsite! What a great news! And even better... This
**DepartmentId** Managed Property is simply the GUID of the Hubsite Site Collection... Simple to figure out from the
Hubsite and also easy to figure our from the connected-site to find its Hubsite! Amazing!

## Here comes the Search API!

I dropped an instance of the REST API Tester on a page and started to work on it!

### News

First, I used the News scenario to get all the news that are linked to the current Hubsite! The query to execute is the
following:

```
{webUrl}/_api/search/query?querytext='PromotedState=2 DepartmentId:{{siteId}}'&selectproperties='Title,Path'
```

{{< figure src="/images/2018/03/ScreenShot-20180326181850.png" >}}

And the results are really the right ones:

{{< figure src="/images/2018/03/ScreenShot-20180326182159.png" >}}

### Events

Then I went on and tried some events using this query:

```
{webUrl}/_api/search/query?querytext='contentclass:STS_ListItem_Events DepartmentId:{{siteId}}'&selectproperties='Title,Path'
```

And I got the events from the hub!

{{< figure src="/images/2018/03/ScreenShot-20180326182446.png" >}}

### Documents

Finally, I wanted to just check if any content could be retrieved from the Hub... Well... Great news! Anything can be!
That is a GREAT news as we could really leverave the Hub functionnality in any custom solution!

The query I used was the following:

```
{webUrl}/_api/search/query?querytext='IsDocument:1 DepartmentId:{{siteId}}'&selectproperties='Title,Path'
```

And I was able to retrieve all the content that was a Document directly in the sites connected to my Hub!

{{< figure src="/images/2018/03/ScreenShot-20180326182757.png" >}}

_The {siteId} token will come in a [future release](https://github.com/estruyf/spfx-rest-api-tester/pull/2)! I'll update
this post when it's available. If you want to use it now, just use the ID of your Hubsite site collection. Also, mind
the double brackets. Those are required to allow the query to be properly formed._

And now, I'm very happy! Happy hubbing!
