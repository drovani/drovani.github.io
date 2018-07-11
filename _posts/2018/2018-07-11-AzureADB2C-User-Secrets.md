---
layout: post
title: Inavord - An Attempt at Getting Started
category: Vigil Journey
tags:
- opensource
- azureadb2c
- usersecrets
---

Something that I've continuously struggled with in getting projects off the ground is finding a way to handle Authentication. Just the thought of building out a User Portal to handle Contact Information and Password Resets has killed many projects before they've even started. Worrying about how to best store passwords and how to securely log users into the application have halted progress on most of the _Grand Ideas_ that I have had. However, since the Vigil project is something that I am truly excited about, it was time to find a solution that I could quickly piece together for most any project and offload as much of the responsibilities to another party as possible. I found my answer in [AAD B2C](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-overview).
> Azure Active Directory (Azure AD) B2C is an identity management service that enables you to customize and control how customers sign up, sign in, and manage their profiles when using your applications. This includes applications developed for iOS, Android, and .NET, among others. Azure AD B2C enables these actions while protecting your customer identities at the same time.

## Azure Active Directory B2C

The documentation for Azure AD B2C is really quite thorough - there are immediate tutorials for writing an application in [ASP.NET](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-tutorials-web-app), a [Windows desktop application](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-tutorials-desktop-app), or a [single page app](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-tutorials-spa) on Node.js.  Additional tutorials for other development environments can usually be found pretty easily (such as [PHP](https://azure.microsoft.com/en-us/resources/samples/active-directory-b2c-php-webapp-openidconnect/)). What is truly solid about the solution is how frictionless the initial start-up was for my project. Since I am build an ASP.NET Core solution, Microsoft has already built a library that handles the UI for me, too. It really couldn't have been easier.

All of the code came as part of the default ASP.NET Code Web Application template that is built into Visual Studio 2017.

![Visual Studio 2017 New ASP.NET Code Web Application with Individual User Accounts authentication](/images/vs2017-new-aspnet-code-web-application-individual-user-accounts.png)

## User Secrets

The other significant advancement in my project start-up task list was the ability to find a place for secrets and connection strings that don't belong in the source files. During testing, the callback URL for the application is _localhost_. This means that anyone would be able to download the source and run the code and be able to use my instance of AADB2C to store accounts - this is not an ideal solution. Instead, I can move the values for the _ClientId_ and _Domain_ into User Secrets!

``` json
{
  "AzureAdB2C": {
    "Instance": "https://login.microsoftonline.com/tfp/",
    "ClientId": "<!-- stored as a secret -->",
    "Domain": "<!-- stored as a secret -->",
    "CallbackPath": "/signin-oidc",
    "SignUpSignInPolicyId": "B2C_1_SiUpIn",
    "ResetPasswordPolicyId": "B2C_1_SSPR",
    "EditProfilePolicyId": "B2C_1_SiPe",
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  }
}
```