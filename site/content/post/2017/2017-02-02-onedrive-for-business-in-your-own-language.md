---
authors:
  - sebastien-levert
categories:
  - OneDrive
  - PnP
  - PowerShell
  - Multilingual
date: '2017-02-02T02:24:00Z'
description: ''
draft: false
slug: onedrive-for-business-in-your-own-language
images:
  - /images/2017/02/OneDriveLanguage.jpg
tags:
  - OneDrive
  - PnP
  - PowerShell
  - Multilingual
title: OneDrive for Business... In your own language!
---

So! I joined an awesome team at [Valo Intranet](https://www.valointranet.com/) recently and I'm super excited of what we
are cooking to make you fall in love with your intranet! Even though thing are going fairly smoothly over there, one
very specific Office 365 feature was making me go crazy, every single day!

## The context

Valo is part of a bigger company called [Sininen Meteoriitti](https://www.meteoriitti.com/) (that is also absolutely
awesome and that won the best employer for like the last 7 years) that is based in Finland. And as far as I know, my
Finnish is not exactly my best language to speak. Cool enough, the Office 365 platform that we are using is mainly in
English so I can easily navigate through all the content and get a feeling of the entire organization and I can discuss
and collaborate with everyone. BUT! When my OneDrive for Business was initially provisioned, it was done in Finnish. And
I could not understand a thing. Not. A. Single. Word. Please see it for yourself, you will understand what I mean.

{{< figure src="/images/2017/02/ScreenShot-20170201160533-1.png" >}}

## Fixing it through the UI

So for some time I felt bad because I needed to keep a separate browser open and use Google Translate to understand my
own OneDrive. But then, I remembered that OneDrive for Business is still a very native SharePoint site under the
cover... Using the URL, I could get to the MUI Settings page, selected the appropriate language I wanted to use (in this
case, English) and then save it. Automatically, the Multilingual User Interface kicked in and I was finally able to
browse normally my OneDrive !

{{< figure src="/images/2017/02/ScreenShot-20170201161634.png" >}}

{{< figure src="/images/2017/02/ScreenShot-20170201162325-1.png" >}}

## Fixing it with PnP PowerShell Cmdlets

Well... That was easy, but not geek enough for me! I decided to revert the changes (I know...) and start again trying to
use the PnP PowerShell Cmdlets to fix that. And it was actually very easy to do as there is already the Get-SPOWeb
Cmdlet available to get the current Web Instance and from there, I can easily use the properties to add a Supported UI
Language. The only code I had to run was the following :

```powershell
Connect-SPOnline https://tenant-my.sharepoint.com/personal/account
$web = Get-SPOWeb
$web.IsMultilingual = $true
$web.AddSupportedUILanguage(1033)
$web.Update()
Execute-SPOQuery
```

## Conclusion

Even though there is no UI to support this functionality, when you know SharePoint enough, you can always get around an
play with it without any problem! Oh, and PnP PowerShell FTW!

Happy Office 365-ing !
