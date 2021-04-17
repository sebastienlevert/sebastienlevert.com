---
author: Sébastien Levert
categories:
- PowerShell
- Office 365
- Office 365 API
- Office365Dev
date: "2015-06-28T21:18:59Z"
description: ""
draft: false
slug: powershell-mondays-5-calling-the-office-365-api-endpoints
tags:
- PowerShell
- Office 365
- Office 365 API
- Office365Dev
title: 'Office 365 PowerShell : Calling the Office 365 API Endpoints'
---


Since I've been working with the new Office 365 API, I always wanted to try if I could get PowerShell to call the Office 365 API Endpoints and therefore establish any kind of connection with the service. Thanks to the simplicity of the APIs and how it is build in Azure Active Directory, it was easier than I thought !

##The endeavour
First of all, I had to do my homeworks and get a bit of context of how we could authenticate against the Office 365 API infrastructure with an App-Only context. As always, the blogosphere is full of information but it was hard to find something relevant. Finaly, Andrew Connell, a fellow SharePoint MVP, blogged and even did a complete course on the Office 365 Authentication process on Pluralsight. It helped me a lot and saved me all the time to get to understand the certificates requirements. The useful link used through my endeavour are the following : 

* [User+App & App-Only Permissions (Client Credentials Grant Flow) in Azure AD & Office 365 APIs](http://www.andrewconnell.com/blog/user-app-app-only-permissions-client-credentials-grant-flow-in-azure-ad-office-365-apis)
* [Office 365 APIs: Overview, Authentication & the Discovery Service on Pluralsight](http://www.pluralsight.com/courses/office-365-api-overview-authentication-discovery-service)
* [The WebApp used to demo the App-Only](https://github.com/mattleib/o365api-as-apponly-webapp)

Now it was something tricky to reverse engineer the Authentication process from C# to PowerShell but I finally did it and think it could be very useful for anyone. It would be very useful for the ones who would love to get more power on their Office 365 environments in order to retrieve and insert data in and to get more hands-on with the Office 365 API.

##Pre-requisites
First of all, you will have to create an Azure AD Application and configure it to be able to use App-Only permissions. I won't go in depth here, Andrew already did a great job on explaining every details of it (and I was able to reproduce the steps, it means it is fairly easy!). Don't forget to specify your permissions in the Application section and not in the Delegated section.

![](/content/images/2015/06/2015-06-28_17-12-38.png)
 
For the code to work, you will need to install on your machine the Microsoft Azure PowerShell that is available right [here](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). You will be able to execute your code in any of the PowerShell environments (regular or Azure), so don't worry about which one to choose from now on. You can stick with the one you are used to.

Then the only thing left you need is a coffee and a bit of time!

##The code
The code is not very complex at the end. For your needs, I did separate it in 3 different Cmdlets. The first one is responsible to get the Access Token on the requested resource. The second one is responsible to invoke the desired endpoint. The third one is a simple helper to validate some of the parameters of Cmdlets.

<script src="https://gist.github.com/sebastienlevert/3131c19b63ca1be2101f.js"></script>

##Nuts and bolts
So, what is happening in those scripts ? Well 3 major things are happening and are helping us in getting our data from the Office 365 APIs.

First, it creates all the plumbing necessary for the App-Only permissions to work. It creates a context, gets the certificate and builds the necessary bits to validate against the Authentication handler that your Certificate is the right one compared to the one that is stores in Azure Active directory.

<script src="https://gist.github.com/sebastienlevert/ff1cd9cf66852dba3e3b.js"></script>

Second, it invokes the Authentication provider to get an AccessToken from Azure Active Directory with the configuration that we just created.

<script src="https://gist.github.com/sebastienlevert/1603d64ef4792f158d6c.js"></script>

Finally, we can pass this AccessToken to be injected in the REST call and we use the standard Invoke-RestMethod Cmdlet.

<script src="https://gist.github.com/sebastienlevert/e8923b7b33d7f4a47539.js"></script>

##Examples
The easy way to use those Cmdlets is to orchestrate it from another script. So the following code shows how to get the AccessToken out of your Azure Active Directory Application and then store the token for other uses. In the following example, I an setting $global variables that are used by the Get-AccessToken Cmdlet, but I could use the Parameters (TenantId, ClientId, CertificatePath, CertificatePassword) to specify my configuration for my AccessToken.

<script src="https://gist.github.com/sebastienlevert/f20b850228546aa942cf.js"></script>

In PowerShell, the following result is now available :

![](/content/images/2015/06/2015-06-28_16-58-46.png)

Then you can use this brand new AccessToken to call an EndPoint available in the requested resource (in this case, it is the Exchange Resource that was specified).

<script src="https://gist.github.com/sebastienlevert/61bae4032d7a8050be88.js"></script>

And the result is fairly simple to work with. Thanks to PowerShell creating PSObject with JSON results, you can browse your objects like any other .NET object!

![](/content/images/2015/06/2015-06-28_17-04-58.png)

##Conclusion
I can now say that working with Office 365 API will be easier than ever! Launch a console, include 3 scripts and we are ready to go! 

Happy PowerShell and Office 365 API!