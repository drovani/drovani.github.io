---
layout: post
title: Using SAML 2.0 with Auth0 to Authenticate Users in TalentLMS
category: "Rovani in C&sharp;"
tags:
- talentlms
- auth0
- sso
- saml
---

Prior to this week, I did not know who [TalentLMS](https://www.talentlms.com/) is, what [SAML](https://wiki.oasis-open.org/security/FrontPage#SAML_V2.0_Standard) does, and how [Auth0](https://auth0.com/) fits into the SSO alphabet soup. However, when a client wants something figured out, that's what I am here to do! After pouring over documentation for both TalentLMS and for Auth0, going down several dead-end paths, and nagging a few of my coworkers to "try it again?", I now successfully (and very delicately) have credentials collected from Auth0 logging a user into TalentLMS.

## Prerequisites

TalentLMS requires a subscription level of _Basic_ or above to implement Single Sign-On. I could not find any way around this, even temporarily. They don't have any sort of a "developer" plan or other trial period to be able to demonstrate a proof-of-concept. At $159.00 per month, one will need to invest at least that much to make this work. Thankfully, my client had no problem bumping up their subscription tier without even knowing if SSO would work.

An Auth0 account is also required. Their free tier includes everything necessary, but even if it didn't, a newly created instance includes all paid features for a few weeks to get you started.

## Start with TalentLMS

Over in TalentLMS, go to `Home / Account & Settings / Users` and change the `SSO integration type` to "SAML2.0".

![TalentLMS - Settings - Users - SSO Integration type](/images/sso-step1-talent-settings.png)

At the bottom of the page is the `Identity provider (IdP) configuration` section. There are values in here that need to go over to Auth0.

    The Entity ID is:
    {instance}.talentlms.com
    
    The Assertion Consumer Service (ACS) URL is:
    https://{instance}.talentlms.com/simplesaml/module.php/saml/sp/saml2-acs.php/{instance}.talentlms.com
    
    The Single Logout Service URL is:
    https://{instance}.talentlms.com/simplesaml/module.php/saml/sp/saml2-logout.php/{instance}.talentlms.com


## Auth0 - Create Application

Within the Auth0 dashboard, go to `Applications`, click `Create Application`, given it a name (like "TalentLMS") and choose the `Regular Web Application`.

![Auth0 - Applications - Create Application](/images/sso-step2-auth0-createapp.png)

For now, skip the _Quick Start_ and _Settings_; go to _Addons_ and flip the toggle for "SAML2 Web App".

![Auth0 - Applications - SAML2 Web App](/images/sso-step3-auth0-saml2webapp.png)

Put the url for the TalentLMS instance as the `Application Callback URL` and paste the Settings _JSON_ below (replacing "{instance}" with your value).

```json
{
  "mappings": {
    "user_id": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier",
    "email": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress",
    "name": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
    "given_name": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname",
    "family_name": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname",
  },
  "passthroughClaimsWithNoMapping": true,
  "mapUnknownClaimsAsIs": true,
  "logout": {
    "callback": "https://{instance}.talentlms.com/simplesaml/module.php/saml/sp/saml2-logout.php/{instance}.talentlms.com",
    "slo_enabled": true
  }
}
```

Since TalentLMS requires a unique identifier (TargetedID), a First name, a Last name, and an Email, we need to have Auth0 capture all four fields on sign-up and pass them through with the SAML identity claim. **This** requirement is what took up the bulk of my time trying to figure out. We'll come back to adding all the finishing pieces needed to make it work.

After you click the `Save` button at the bottom of the modal, switch over to the `Usage` tab, and click on "Download Auth0 certificate". Save this file and open it in your favorite text editor. It will look something like this:

    -----BEGIN CERTIFICATE-----
    MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQDMYfnvWtC8Id5bPKae5yXSxQTt
    BAMTEGhjLXBvYy5hdXRoMC5jb20wHhcNMTkxMTA4MTgwNzM0WhcNMzMwNzE3MTgw
    NzM0WjAbMRkwFwYDVQQDExBoYy1wb2MuYXV0aDAuY29tMIIBIjANBgkqhkiG9w0B
    AQEFAAOCAQ8AMIIBCgKCAQEAvyV51yFcm+SJgWT8AKcw0uFTFRZnjkrmJZCMuKvC
    +Zpul6AnnZWfI2TtIarvjHBFUtXRo96y7hoL4VWOPKGCsRqMFDkrbeUjRrx8iL91
    KjIU8w3a8+9TDRpVbTLYtG9t8f1SjDcroBHLaF1XE8gH253L3tzK0Vfw3v7SHM9V
    lso3/gsf/CfJIxQXVkCtUinRElRAIGbSSuOL9fP5Coy29uC4h+w+Tkan0IQsVoTu
    bE5TH49mTN/ZF9Pcio1dktJOLVi+Ww4yl9l4Qbu4K4tEK4FXWyNqTDc+11SFd2uU
    C3RMtsyO6qT+KeuwBtBjqgKHjB0jl1Yn/Du8ljPESb2mgwIDAQABo0IwQDAPBgNV
    HRMBAf8EBTADAQH/MB0GA1UdDgQWBBRXd2raOpQViBgFHsR9seM1fN9CdTAOBgNV
    4/srnyf6sh9c8Zk04xEOpK1ypvBz+Ks4uZObtjyQjtQ8mbDOsiLLvh7wIDAQABad
    1l1v5K/yIqt1b17mtnIw5EXGpnwe2INxeUVvOz2eQAff1UdmyKh605LUKAFd9SZk
    DnRiFU4QD0yenFkSlpnjU3Xy7UzGg2Nv5j/d8RsPGrg+pJDVGwFSriEg1TQQX4ZX
    MhcxkOZFsw+EjKjx8j9x6gDZ3q+e5GvTPNO/jUX9gfO8Bz8SnhKyDiqHtvZhkBlG
    yQjtQ8mbDOsiLLvh7wIDAQAB/5ml3plqk252APWd9hsW1IbLCrc4yttA6usDYJ1d
    zohmAMID02C7cYoiR1e2dK+wjHsuBGptjGmOkL44ZjjY1FCljomvzYalCFeNeHo=
    -----END CERTIFICATE-----

## Back to TalentLMS

Flipping over to that other tab (you didn't close it, right? Because there's a lot more of this back-and-forth to do) - now we can fill out most of these values:

![TalentLMS - SAML - Settings](/images/sso-step4-talentlms-saml.png)

1. The `Identity provider` in TalentLMS matches the `Issuer` in Auth0.
1. Click the "or paste your SAML certificate" link and drop in the big block of random looking characters from the PEM file.
1. `Remote sign-in URL` comes from Auth0's `Identity Provider Login URL`.
1. `Remote Sign-out URL` is the `Identity Provider Login URL` with "/logout" appended.
1. `TargetedID`, `First name`, `Last name`, and `Email` get the schema values from the `mappings` section of the Auth0 _JSON_.

Now that everything has been pasted into their respective fields, go ahead and click the `Save and check your configuration` button. You should be prompted to Log In or Sign Up. Proceed with signing up, which should then kick you back to the TalentLMS "Check your SSO configuration" page.

![TalentLMS Check your SSO configuration](/images/sso-step5-login-signup.png)

## So Close!

There are two pieces still missing: sending the `given_name` and `family_name` fields from Auth0, and capturing those fields during the Sign Up process.

Auth0 stores any additional data that is captured into `user_metadata`, which can be viewed in `Users & Roles -> Users`, select a user, then scroll down to the `Metadata` section. Add the following _JSON_ to the `user_metadata`.

```json
{
  "given_name": "FirstnameTest",
  "family_name": "LastnameTest"
}
```

Next step is to add these fields into the SAML configuration mappings. To do this, we are going to add a new Auth0 rule. Click on `Rules`, `+ Create Rule`, select `</> Empty rule`. Give it a name like "SAML Attributes mapping" and paste the following into the Script section:

```js
function (user, context, callback) {
  context.samlConfiguration.mappings = {
     "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "user_id",
     "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress": "email",
     "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "name",
     "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/given_name": "user_metadata.given_name",
     "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/family_name": "user_metadata.family_name"
  };

  callback(null, user, context);
}
```

Click `Save Changes`. You can even click `Try This Rule`. The "Output" should contain the a section for the `samlConfiguration` object containing a `mappings` object that matches when you entered above.

Flip back to the TalentLMS `Check your SSO configuration` page, click the `Log out` button (to clear your Auth0 session), and click the `Log in` button. Once you do so, you should now have four green checkmarks!

![TalentLMS - Successfully logged in.](/images/sso-step6-successfully-logged-in.png)

## Capturing First & Last Name

The final step is to customize the Sign Up page to require the user to enter values for First and Last name. The simplest way (that I found) to do this is to customize the login page in Auth0. Under `Universal Login`, click on the `Login` tab, toggle the "Customer Login Page" and select the "Lock" Default Template. This uses the [Auth0Lock](https://auth0.com/docs/libraries/lock/) and for our purposes, we will be utilizing the `additionalSignUpFields` array [configuration option](https://auth0.com/docs/libraries/lock/v11/configuration#additionalsignupfields-array-). Add the following _JSON_ snippet to the code block:

```json
additionalSignUpFields:[{
  name: "given_name",
  placeholder: "Enter your First Name"
},
{
  name:"family_name",
  placeholder:"Enter your Last Name"
}],
```

And it should look something like this:

![Auth0 - Customize Sign Up Fields](/images/sso-step7-customize-login-page.png)

Click `Save Changes` and that should be it! Go back to TalentLMS's configuration page and you can test it out. To make sure it really works, log yourself out of TalentLMS, and when logging back in, click the `Log in with SAML 2.0`. You can go through the Sign Up workflow, which should require First name, Last name, Email, and Password.

## How to Break TalentLMS

The major problem I found with this solution is that it is **extremely** fragile. There are several ways to completely break logging in.

### TalentLMS "Username" must match Auth0 "Name"

When setting us TalentLMS for SAML, the field that gets mapped to "TargetedID" becomes the user's "Username". This is editable by the user in TalentLMS (and I could not find a way to disable this). If the user changes that value in TalentLMS, then the next time Auth0 sends over the authenticated user information, the values won't match. TalentLMS will then try to create another user, but will fail because the email address is already on an existing account.

If this happens, you can either update the "Name" in Auth0, or revert the "Username" in TalentLMS back to the matching value.

### Don't Switch "SSO login screen" Until Everything Works

At the bottom of the Users settings page in TalentLMS is an innocent looking drop down list with the label of "SSO login screen" with two options: "Login page + IdP login link" and "IdP login page". Since I was just testing things out, I flipped the option to the second of the two, clicked "Save" and promptly locked myself (and everyone else) out of the TalentLMS instance. It required submitting a support ticket to have them revert the setting - quite an embarrassing moment, since I had to go back to the client and have them verify to customer support that this change needed to be made. TalentLMS should really have a modal pop up that says:

> Are you sure? Because if SSO login isn't perfectly working, you're going to screw things up. Also, if you haven't already made a SSO user a "SuperAdmin" you should do that, or you might not be able to get back into the administrative dashboard to fix any SSO settings.
> 
> Save Changes? or Revert?

**After** you have everything working with SSO, and have an SSO user as a SuperAdmin, then you can change the login to be "IdP login page". This will force users directly to Auth0 to authenticate, removing the step of clicking the "Log in with SAML 2.0" link.

### TalentLMS Does Not Permanently Delete Users

In my testing, I would frequently delete a user from both TalentLMS and Auth0 so that I could restart the entire sign up / log in workflow from the beginning. However, I quickly ran into an issue where TalentLMS would claim that an email address was already taken - even though I _knew_ I had deleted the user.

It turns out that TalentLMS only soft-deletes users (even if an Admin clicks the Delete button). If you want to [permanently delete users](https://help.talentlms.com/hc/en-us/articles/360014658253-How-to-delete-users-and-courses-permanently) you need to go through the convoluted process to permanently delete them. Thankfully, the Knowledge Base article was easy to find.

## Is There a Better Way?

TalentLMS and Auth0 also support OpenID Connect as an SSO option, so that would also be an option. I don't have a good reason why I went with SAML over OpenID. Maybe someone else can share their experience and we'll see what the advantages for each might be.