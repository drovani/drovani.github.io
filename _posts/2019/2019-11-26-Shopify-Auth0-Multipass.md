---
layout: post
title: Authenticate Shopify Customers with Auth0
category: "Rovani in C&sharp;"
excerpt_separator: <!--more-->
tags:
  - shopify
  - auth0
  - sso
---

What better way to follow up from the last two posts by combining the concepts. In "This week at BlueBolt", I present my solution for utilizing Auth0 to authenticate a user and then pass them back to Shopify utilizing the Multipass feature.

<figure>
    <img src="/images/leeloo-dallas-auth0.jpg" alt="Leeloo Dallas Auth0 Multipass" />
    <figcaption>
        <a href="https://www.youtube.com/watch?v=8bF5ft-oOWU">Leeloo Dallas Multi Pass</a> &ndash; <em>The Fifth Element</em> (1997)
    </figcaption>
</figure>

The primary steps involved in this process include:

1. Enable Multipass on Shopify account
1. Create an Auth0 Application
1. Add Auth0 rule to create Multipass token and redirect user
1. Update Shopify Login and Logout links to send user to Auth0

<!--more-->

## Prerequisites

Similar to how TalentLMS requires an upgraded subscription in order to utilize SSO, Shopify requires a _Shopify Plus_ storefront. As a [Shopify Partner](https://www.shopify.com/partners), you can create development stores to test out any functionality you need to implement on a Plus store.

An Auth0 account is also required. Their free tier includes everything necessary, but even if it didn't, a newly created instance includes all paid features for a few weeks to get you started.

## Start with Shopify

Log into the Shopify store, go to the `Settings` page and click into the `Checkout` window. Customer accounts need to be either optional or required. This will allow you to enable Multipass for the store.

![Shopify - Settings - CheckOut - Customer accounts](/images/multipass-step1-shopify-enable.png)

This Multipass secret will be used by the encryption routine to create a cipher that Shopify will be able to decrypt and verify that a Multipass request is legitimate. If you ever suspect that your Multipass key has been compromised, then Disable and re-Enable Multipass. This will generate a new secret key which you will need to copy into Auth0.

## Auth0 - Create Application

Within the Auth0 dashboard, go to `Applications`, click `Create Application`, given it a name (like "Shopify Store" - this is publicly visible!) and choose the `Regular Web Application`.

![Auth0 - Applications - Create Application](/images/multipass-step2-auth0-createapp.png)

Skip the _Quick Start_; go to _Settings_.

For the following sections, you need to substitute `{shopify-domain}` with the domain of your particular store (ex: `sample-store.myshopify.com`)

- **Allowed Callback URLs**: `https://{shopify-domain}/account`
- **Application Login URI**: `https://{shopify-domain}/account/login`
- **Allowed Logout URLs**: `https://{shopify-domain}/account/logout`

Expand the _Advanced Settings_ section and add these two key/value pairs under Application Metadata:

- Key: **shopify_domain**; Value: `{shopify-domain}`
- Key: **shopify_multipass_secret**; Value `{multipass-secret}`

![Auth0 - Applications - Advanced Settings - Application Metadata](/images/multipass-step3-auth0-appmetadata.png)

## Auth0 - Create Shopify Multipass Rule

Now that we have a landing point for the Shopify store to send users to, we need to be able to pass the authenticated user back to the Shopify store. This is where Multipass comes into play.

![Auth0 - Rules - Create Rule](/images/multipass-step4-auth0-createrule.gif)

Start by creating a new Rule in Auth0, use the `Empty rule` template, give it a descriptive name (like: "Shopify Multipass"), and paste in the following code.

{% gist 8199b1e0ffa1976c00af6781fcb98fbf %}

- **Line 2**: since this rule will run on every authentication workflow, we want to restrict this to just when the Auth0 Application has declared the `shopify_domain` and `shopify_multipass_seceret` metadata.
- **Line 4-10**: Shopify requires at least the `email` and `created_at` data points. For added information, we are also passing an `identifier` (in case multiple Auth0 accounts have the same email address) and `remote_ip` to ensure that this Multipass request can only be used by the same computer that sent the initial request.
- **Line 11-15**: In my [TalentLMS/Auth0 post]({% post_url 2019/2019-11-14-TalentLMS-Auth0-SAML %}), I describe how to grab first/last name when signing up a user. If that is in place, this will also add those data points to the Shopify account.
- **Line 17-28**: These lines that do the actual encryption were taken from [a repository](https://github.com/beaucoo/multipassify/blob/master/multipassify.js) that I found on GitHub, so thanks go to [Cory Smith](https://github.com/corymsmith) from Calgary, AB for this one.
- **Line 30-32**: This sets the destination for the authenticated user.

Once this rule runs, the user will be redirected back to the Shopify store. This is important, because if there are any Auth0 rules after this one, they will be completely skipped.

### COME BACK HERE ###



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
