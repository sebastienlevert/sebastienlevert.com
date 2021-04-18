---
authors: 
  - sebastien-levert
categories:
  - PowerShell
  - Office 365
  - Office 365 API
  - Office365Dev
date: '2015-06-28T21:18:59Z'
description: ''
draft: false
slug: powershell-mondays-5-calling-the-office-365-api-endpoints
tags:
  - PowerShell
  - Office 365
  - Office 365 API
  - Office365Dev
title: 'Office 365 PowerShell : Calling the Office 365 API Endpoints'
---

Since I've been working with the new Office 365 API, I always wanted to try if I could get PowerShell to call the Office
365 API Endpoints and therefore establish any kind of connection with the service. Thanks to the simplicity of the APIs
and how it is build in Azure Active Directory, it was easier than I thought !

## The endeavour

First of all, I had to do my homeworks and get a bit of context of how we could authenticate against the Office 365 API
infrastructure with an App-Only context. As always, the blogosphere is full of information but it was hard to find
something relevant. Finaly, Andrew Connell, a fellow SharePoint MVP, blogged and even did a complete course on the
Office 365 Authentication process on Pluralsight. It helped me a lot and saved me all the time to get to understand the
certificates requirements. The useful link used through my endeavour are the following :

- [User+App & App-Only Permissions (Client Credentials Grant Flow) in Azure AD & Office 365 APIs](http://www.andrewconnell.com/blog/user-app-app-only-permissions-client-credentials-grant-flow-in-azure-ad-office-365-apis)
- [Office 365 APIs: Overview, Authentication & the Discovery Service on Pluralsight](http://www.pluralsight.com/courses/office-365-api-overview-authentication-discovery-service)
- [The WebApp used to demo the App-Only](https://github.com/mattleib/o365api-as-apponly-webapp)

Now it was something tricky to reverse engineer the Authentication process from C# to PowerShell but I finally did it
and think it could be very useful for anyone. It would be very useful for the ones who would love to get more power on
their Office 365 environments in order to retrieve and insert data in and to get more hands-on with the Office 365 API.

## Pre-requisites

First of all, you will have to create an Azure AD Application and configure it to be able to use App-Only permissions. I
won't go in depth here, Andrew already did a great job on explaining every details of it (and I was able to reproduce
the steps, it means it is fairly easy!). Don't forget to specify your permissions in the Application section and not in
the Delegated section.

{{< figure src="/images/2015/06/2015-06-28_17-12-38.png" >}}

For the code to work, you will need to install on your machine the Microsoft Azure PowerShell that is available right
[here](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). You will be able to execute your code in any of the
PowerShell environments (regular or Azure), so don't worry about which one to choose from now on. You can stick with the
one you are used to.

Then the only thing left you need is a coffee and a bit of time!

## The code

The code is not very complex at the end. For your needs, I did separate it in 3 different Cmdlets. The first one is
responsible to get the Access Token on the requested resource. The second one is responsible to invoke the desired
endpoint. The third one is a simple helper to validate some of the parameters of Cmdlets.

```powershell
<#
.DESCRIPTION
  Gets an access token for an App-Only Azure AD Application
.PARAMETER TenantId
  The TenantId of the Azure AD Application
  Can be set globally with $global:AzureADApplicationTenantId
.PARAMETER ClientId
  The ClientId of the Azure AD Application
  Can be set globally with $global:AzureADApplicationClientId
.PARAMETER CertificatePath
  The path to the *.pfx certificate used in your Azure AD Application
  Can be set globally with $global:AzureADApplicationCertificatePath
.PARAMETER CertificatePassword
  The password used to secure your *.pfx certificate
  Can be set globally with $global:AzureADApplicationCertificatePassword
.PARAMETER ResourceUri
  The resource URI you want to authenticate against
.EXAMPLE
  Get-AccessToken -TenantId "00000000-0000-0000-0000-000000000000" -ClientId "00000000-0000-0000-0000-000000000000" -CertificatePath "C:\Certificate.pfx" -CertificatePassword "Password" -ResourceUri "https://outlook.office365.com/"
.EXAMPLE
  Get-AccessToken -ResourceUri "https://outlook.office365.com/"
#>
function Get-AccessToken()
{
  Param(
    [Parameter(Mandatory=$true, ParameterSetName="UseLocal")]
    [Parameter(Mandatory=$false, ParameterSetName="UseGlobal")]
    [ValidateNotNullOrEmpty()]
    [String]
    $TenantId = $global:AzureADApplicationTenantId,

    [Parameter(Mandatory=$true, ParameterSetName="UseLocal")]
    [Parameter(Mandatory=$false, ParameterSetName="UseGlobal")]
    [ValidateNotNullOrEmpty()]
    [String]
    $ClientId = $global:AzureADApplicationClientId,

    [Parameter(Mandatory=$true, ParameterSetName="UseLocal")]
    [Parameter(Mandatory=$false, ParameterSetName="UseGlobal")]
    [ValidateNotNullOrEmpty()]
    [String]
    $CertificatePath = $global:AzureADApplicationCertificatePath,

    [Parameter(Mandatory=$true, ParameterSetName="UseLocal")]
    [Parameter(Mandatory=$false, ParameterSetName="UseGlobal")]
    [ValidateNotNullOrEmpty()]
    [String]
    $CertificatePassword = $global:AzureADApplicationCertificatePassword,

    [Parameter(Mandatory=$true)]
    [ValidateNotNullOrEmpty()]
    [String]
    $ResourceUri
  )

  #region Validations
  #-----------------------------------------------------------------------
  # Validating the TenantId
  #-----------------------------------------------------------------------
  if(!(Is-Guid -Value $TenantId))
  {
    throw [Exception] "TenantId '$TenantId' is not a valid Guid"
  }

  #-----------------------------------------------------------------------
  # Validating the ClientId
  #-----------------------------------------------------------------------
  if(!(Is-Guid -Value $ClientId))
  {
    throw [Exception] "ClientId '$ClientId' is not a valid Guid"
  }

  #-----------------------------------------------------------------------
  # Validating the Certificate Path
  #-----------------------------------------------------------------------
  if(!(Test-Path -Path $CertificatePath))
  {
    throw [Exception] "CertificatePath '$CertificatePath' does not exist"
  }

  #-----------------------------------------------------------------------
  # Validating the availability of Azure Active Directory Assemblies
  #-----------------------------------------------------------------------
  if(!(Test-Path -Path "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Services\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"))
  {
    throw [Exception] "Azure Active Directory Assemblies are not available"
  }
  #endregion

  #region Initialization
  #-----------------------------------------------------------------------
  # Loads the Azure Active Directory Assemblies
  #-----------------------------------------------------------------------
  Add-Type -Path "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Services\Microsoft.IdentityModel.Clients.ActiveDirectory.dll" | Out-Null

  #-----------------------------------------------------------------------
  # Constants
  #-----------------------------------------------------------------------
  $keyStorageFlags = [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::MachineKeySet

  #-----------------------------------------------------------------------
  # Building required values
  #-----------------------------------------------------------------------
  $authorizationUriFormat = "https://login.windows.net/{0}/oauth2/authorize"
  $authorizationUri = [String]::Format($authorizationUriFormat, $TenantId)
  #endregion

  #region Process
  #-----------------------------------------------------------------------
  # Building the necessary context to acquire the Access Token
  #-----------------------------------------------------------------------
  $authenticationContext = New-Object -TypeName "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authorizationUri, $false
  $certificate = New-Object -TypeName "System.Security.Cryptography.X509Certificates.X509Certificate2" -ArgumentList $CertificatePath, $CertificatePassword, $keyStorageFlags
  $assertionCertificate = New-Object -TypeName "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientAssertionCertificate" -ArgumentList $ClientId, $certificate

  #-----------------------------------------------------------------------
  # Ask for the AccessToken based on the App-Only configuration
  #-----------------------------------------------------------------------
  $authenticationResult = $authenticationContext.AcquireToken($ResourceUri, $assertionCertificate)

  #-----------------------------------------------------------------------
  # Returns the an AccessToken valid for an hour
  #-----------------------------------------------------------------------
  return $authenticationResult.AccessToken
  #endregion
}
```

## Nuts and bolts

So, what is happening in those scripts ? Well 3 major things are happening and are helping us in getting our data from
the Office 365 APIs.

First, it creates all the plumbing necessary for the App-Only permissions to work. It creates a context, gets the
certificate and builds the necessary bits to validate against the Authentication handler that your Certificate is the
right one compared to the one that is stores in Azure Active directory.

```powershell
$authenticationContext = New-Object -TypeName "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authorizationUri, $false
$certificate = New-Object -TypeName "System.Security.Cryptography.X509Certificates.X509Certificate2" -ArgumentList $CertificatePath, $CertificatePassword, $keyStorageFlags
$assertionCertificate = New-Object -TypeName "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientAssertionCertificate" -ArgumentList $ClientId, $certificate
```

Second, it invokes the Authentication provider to get an AccessToken from Azure Active Directory with the configuration
that we just created.

```powershell
$authenticationResult = $authenticationContext.AcquireToken($ResourceUri, $assertionCertificate)
```

Finally, we can pass this AccessToken to be injected in the REST call and we use the standard Invoke-RestMethod Cmdlet.

```powershell
$headers = @{ "Authorization" = [String]::Format("Bearer {0}", $AccessToken) }
$results = Invoke-RestMethod -Uri $EndpointUri -Method $Method -Headers $headers
return $results
```

## Examples

The easy way to use those Cmdlets is to orchestrate it from another script. So the following code shows how to get the
AccessToken out of your Azure Active Directory Application and then store the token for other uses. In the following
example, I an setting $global variables that are used by the Get-AccessToken Cmdlet, but I could use the Parameters
(TenantId, ClientId, CertificatePath, CertificatePassword) to specify my configuration for my AccessToken.

```powershell
$global:AzureADApplicationTenantId = "00000000-0000-0000-0000-000000000000"
$global:AzureADApplicationClientId = "00000000-0000-0000-0000-000000000000"
$global:AzureADApplicationCertificatePath = "C:\Certificate.pfx"
$global:AzureADApplicationCertificatePassword = "Password"
$exchangeResourceUri = "https://outlook.office365.com/";

. .\Get-AccessToken.ps1
. .\Is-Guid.ps1

$accessToken = Get-AccessToken -ResourceUri $exchangeResourceUri
```

In PowerShell, the following result is now available :

{{< figure src="/images/2015/06/2015-06-28_16-58-46.png" >}}

Then you can use this brand new AccessToken to call an EndPoint available in the requested resource (in this case, it is
the Exchange Resource that was specified).

```powershell
$url = "https://outlook.office365.com/api/v1.0/users('admin@tenant.onmicrosoft.com')/folders/inbox/messages"

. .\Invoke-SecuredRestMethod.ps1

$response = Invoke-SecuredRestMethod -Method "GET" -AccessToken $accessToken -EndpointUri $url
$response.value | Select Subject, DateTimeSent
```

And the result is fairly simple to work with. Thanks to PowerShell creating PSObject with JSON results, you can browse
your objects like any other .NET object!

{{< figure src="/images/2015/06/2015-06-28_17-04-58.png" >}}

##Conclusion I can now say that working with Office 365 API will be easier than ever! Launch a console, include 3
scripts and we are ready to go!

Happy PowerShell and Office 365 API!
