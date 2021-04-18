---
authors:
  - sebastien-levert
categories:
  - PnP PowerShell
  - Microsoft 365 Learning Pathways
date: '2020-06-06T00:32:44Z'
draft: false
slug: find-all-pages-utilizing-the-microsoft-365-learning-pathways-web-part
images:
  - /images/2020/06/M365LearningPathways.png
tags:
  - PnP PowerShell
  - Microsoft 365 Learning Pathways
title: Find all pages utilizing the Microsoft 365 Learning Pathways web part
summary:
  An interesting thread was initiated by Loryan Strant on Twitter. That sparked an idea, and finally, a potentially very
  easy solution!
---

An interesting thread was initiated by [Loryan Strant](https://twitter.com/loryanstrant) on Twitter.

{{< twitter_simple 1268763344988631040 >}}

## The solution

That sparked an idea, and finally, a potentially very easy solution! I Think the main issue is to find where a web part
is being used to be able to contact site owners to manage change management (when a new version of a web part is
available), allow a better understand of the utilized web parts, etc.

[Mikael Svenson](https://twitter.com/mikaelsvenson) already wrote a great article on how to Locate a
[Locate pages where a particular web part is being using on modern SharePoint sites](https://www.techmikael.com/2019/02/locate-pages-where-particular-web-part.html).
I used a similar approach with the component id of the Microsoft 365 Learning Pathways.

## The script

```powershell
# Connecting to your tenant
Connect-PnPOnline -Url https://tenant.sharepoint.com

# Getting the list of pages utilizing the Learning Pathways web part
Submit-PnPSearchQuery -Query "FileExtension:aspx 141d4ab7-b6ca-4bf4-ac59-25b7bf93642d" -All -RelevantResults | Select-Object OriginalPath
```

And the result!

{{< figure src="/images/2020/06/ScreenShot-20200605163137.png" >}}

Hope it's useful!
