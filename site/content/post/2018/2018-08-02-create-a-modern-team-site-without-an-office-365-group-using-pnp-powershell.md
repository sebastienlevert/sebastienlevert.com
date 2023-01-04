---
authors: 
  - sebastien-levert
date: "2018-08-02T02:15:41.000Z"
draft: false
slug: create-a-modern-team-site-without-an-office-365-group-using-pnp-powershell
images: 
- /images/2018/08/PnPPowerShellModernSite.png
tags:
- Office 365
- PnP PowerShell
- SharePoint Online
title: Create a Modern Team site without an Office 365 Group using PnP PowerShell
---

I was so excited when this was announced!
[Modern Team Sites without an Office 365 Group](https://techcommunity.microsoft.com/t5/Microsoft-SharePoint-Blog/Updates-to-SharePoint-self-service-site-creation/ba-p/218971)
are out! We love Office 365 Groups, but sometimes, it's not possible, or it's not built for our scenario... So the
SharePoint team changed the things a bit and made it available to the masses!

## Creating using PnP PowerShell

For now, the UI is not yet rolled out so you might think that you are missing on something... Well, absolutely not! The
functionality is available if you go through the APIs and keep in mind that one of the easiest way to interact with the
SharePoint APIs is to use
[PnP PowerShell](https://docs.microsoft.com/en-us/powershell/sharepoint/sharepoint-pnp/sharepoint-pnp-cmdlets?view=sharepoint-ps)!

The command is also SUPER easy as it reuses a Cmdlet that was there forever. The following script will help you deploy a
new Modern Team site without an Office 365 Group.

```powershell
# Connecting to your tenant
Connect-PnPOnline -Url https://tenant.sharepoint.com

# Creating the Modern Site
New-PnPTenantSite `
  -Title "Modern Site" `
  -Url "https://tenant.sharepoint.com/sites/modern-site" `
  -Description "Modern Site" `
  -Owner "admin@tenant.onmicrosoft.com" `
  -Lcid 1033 `
  -Template "STS#3" `
  -TimeZone 10 `
  -Wait
```

The key? The famous `STS#3` now allows this functionnality. It does not deploy as quick as we are now used to with
Modern Groups, but it's already pretty good!

## Conclusion

You can take advantage of this feature now using PowerShell and I think this is great news for all organizations!

Happy PowerShelling!
