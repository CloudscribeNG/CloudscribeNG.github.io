# Managing Cookie Consent

## Overview

CloudscribeNG ships with two entirely separate cookie consent systems - your choice of which to use will depend on your specific needs and preferences.

## 1. The simple default template system

A new CloudscribeNG site, spun up from the template, will prompt the user for cookie consent via a simple inline form at the top of the screen.

![ ](/media/images/image_2025-08-11t14-26-29-7-ws.png)

This form is implemented from the _Layout.cshtml file for the current site theme - by including the partial view:

```
 <partial name="_CookieConsentPartial" />
```

The text message for the above form can be customised via the Administration >> Privacy Settings page.

At the foot of each page, a link is provided for the user to revoke their consent - again called in from the _Layout.cshtml file:

```
<partial name="_CookieConsentRevokePartial" />
```

Under the hood, this system sets (or removes) a cookie called cookieconsent_status. 

This cookie is created, managed and read via the Microsoft **ITrackingConsentFeature **interface:

```
var consentFeature = Context.Features.Get<ITrackingConsentFeature>();
```

.Net middleware (CookiePolicyMiddleware) automatically creates and populates an ITrackingConsentFeature instance on every request by checking for the consent cookie.

This line in the startup.cs code is required to initialise the above cookie consent tracking system:

```
app.UseCookiePolicy();
```

### Note: **breaking change** in CloudscribeNG version 8.4:

The above consent form has been enhanced to support a 'Decline' option, which simply hides the consent form, without setting the cookieconsent_status cookie.

![ ](/media/images/image_2025-08-21t09-59-55-0.png)

The footer text 'Change cookie preferences' reveals this form again, allowing the preference to be revoked and re-set.

To implement this more user-friendly approach, the partial views _CookieConsentPartial  and _CookieConsentRevokePartial  have been modified.

However, if you have local overrides of these two partial views in your web project, acquired from an earlier CloudscribeNG installation pre-8.4, you may with to either delete these overrides (so you revert to using the versions from the CloudscribeNG.Core.CompiledViews.Bootstrap5 v8.4 Nuget) or else modify your local view overrides to accommodate the changes made to these views at version 8.4 - see the [source code here](https://github.com/CloudscribeNG/CloudscribeNG/tree/develop/src/CloudscribeNG.Core.CompiledViews.Bootstrap5/Views/Shared).  

This change is implemented for the Bootstrap5 libraries only, and not implemented for Bootstrap4.  

 

## 2. More fully-featured TagHelper system

**Disclaimer:**  the authors of CloudscribeNG are not lawyers and not qualified to provide any legal advice. We cannot promise that use of this tool will make your site compliant with cookie laws in various countries or regions. You may find it useful in meeting your compliance goals, but it is up to you to decide for yourself whether your site is in actual compliance with any laws. Furthermore, this system is based upon a very popular tool by the folks at Silktide Ltd. CloudscribeNG has just implemented an MVC TagHelper to make it convenient to use in ASP.NET Core MVC projects. To really understand this tool, you should [visit their website and documentation](https://www.osano.com/cookieconsent/documentation/about-cookie-consent/?utm_source=referral&utm_medium=cmp-os&utm_term=cookieconsent&utm_content=plugin&utm_campaign=eucookielaw).

### Using the Insite/Silktide Cookie Consent TagHelper

CloudscribeNG.Web.Common includes an MVC TagHelper that encapsulates most of the settings for [the popular cookie consent javascript](https://www.osano.com/cookieconsent/documentation/about-cookie-consent/?utm_source=referral&utm_medium=cmp-os&utm_term=cookieconsent&utm_content=plugin&utm_campaign=eucookielaw), in order to make it easy to wire up cookie consent javascript in ASP.NET Core MVC web applications.

It is more flexible than the default system above - for example it allows you to choose between an opt-out cookie consent prompt, or an opt-in one (see below).

Assuming you have already installed the NuGet package for [CloudscribeNG.Web.Common](https://www.CloudscribeNG.com/docs/CloudscribeNG-web-common), you also need to make sure you have declared the taghelper in your _ViewImports.cshtml like this:

```html
@addTagHelper  *, CloudscribeNG.Web.Common
```

Then in your _Layout.cshtml file you would include it in the head element. To use it with the default compliance level of "info", you just add it like this:

```html
<cookie-consent enabled="true" compliance-type="info"></cookie-consent>
```

That will give you a popup like this at the bottom of the page:

[![ cookie consent popup configured with info compliance](/media/images/cookie-consent-info-ws.jpg)](/media/images/cookie-consent-info.jpg)

There are 2 other settings available for compliance-type, "opt-in", configured like this:

```html
<cookie-consent enabled="true" compliance-type="opt-in"></cookie-consent>
```

changes the popup like this:

[![ cookie consent popup configured for opt-in](/media/images/cookie-consent-opt-in-ws.jpg)](/media/images/cookie-consent-opt-in.jpg)

and "opt-out" configured like this:

```
<cookie-consent enabled="true" compliance-type="opt-out"></cookie-consent>
```

changes the popup like this:

[![ cookie consent popup configured with opt-out](/media/images/cookie-consent-opt-out-ws.jpg)](/media/images/cookie-consent-opt-out.jpg)

Clicking "Got It" dismisses the popup and sets a consent cookie with the value "dismiss". For "info" level compliance that is the only thing the user can click so that is the only possible cookie value. If using "opt-in", the user can click the "Allow" button and the value of the consent cookie will be "allow", and if using "opt-out", the user can click "Decline" and the cookie value will be "deny".

There is certainly some irony in that we are using a cookie to keep track of whether the user has been informed, accepted or declined use of cookies, but that is what we do. Note that some cookies including authentication cookies are exempt from cookie laws in some regions according to articles found on the web [such as this one](http://ec.europa.eu/ipg/basics/legal/cookies/index_en.htm). Mainly cookie laws tend to focus on advertising and tracking cookies, therefore you may need to adjust your use of such cookies if you are using opt-in or opt-out. This also is relevant to how you configure google analytics, and our [google analytics taghelper](/docs/easy-google-analytics), has also been updated to integrate with the cookie-consent taghelper. My non authoritative understanding is that if you are just using the "info" level of compliance you don't need to change the way you use cookies for google analytics tracking, but if you are using opt-in or opt-out, then you do need to configure google analytics differently unless the user accepts the use of cookies with opt-in or unless the user does not decline the use of cookies with opt-out.

### Configuring the google-analytics taghelper to work in tandem with the cookie-consent taghelper

Our google-analytics taghelper has built in awareness of the cookie-consent cookie so it can be set to work differently based on that cookie. But note that google-analytics taghelper will by default treat the absence of any cookie consent cookie as implicit consent, so on the first page request when the user has not yet had a chance to interact with the consent popup, it would track using cookies. To make it only track if there is explicit consent, ie the user has accepted or dismissed the popup and a consent cookie exists, you can set require-explicit-cookie-consent="true".

```html
<google-analytics profile-id="##-########" require-explicit-cookie-consent="false" cookie-consent-accept-dismiss="true" consent-cookie-name="cookieconsent_status"></google-analytics>
```

If you want to be even more strict and only use cookies if the consent cookie value is "allow", and not "dismiss" then add cookie-consent-accept-dismiss="false", it is true by default.

The differences in how google analytics gets wired up by the taghelper when allowing tracking cookies vs when not allowing tracking cookies is based on [this article](https://www.themarketingtechnologist.co/setting-up-a-cookie-law-compliant-google-analytics-tracker/). When cookies are used we can include the userid of the user in our tracking if the user is signed in and we know the userid. This userid would come from your own database or if using CloudscribeNG Core it would be the database id of the user. So if cookies are enabled it gets wired up like this:

```javascript
ga('set','userId','someid'); //included only if user is authenticated
ga('require', 'displayfeatures');
ga('set', 'anonymizeIp', undefined);
```

and if cookies are not enabled then it gets wired up like this:

```javascript
ga('set', 'displayFeaturesTask', null);
ga('set', 'anonymizeIp', true);
```

Note that if you are using advertising on your site, there may be other things you need to do to meet your compliance goals, use of google analytics is just one aspect of it and is the only one dealt with by our taghelpers. You should take a holistic look at how you are using cookies in your site and make your own determination perhaps with your own legal counsel to determine whether your site actually meets your compliance goals.

### Configuration of the cookie consent TagHelper

The cookie consent javascript has many configurable options and most of them have been exposed as properties on our cookie-consent taghelper. Any settings that you don't specify will just use the default values so you don't need to specify every setting, just the ones where you want to change from the default value. Below you can see the cookie-consent taghelper with all the available settings and their default values.

```html
<cookie-consent enabled="true" 
	compliance-type="info" 
	location="false"
	law-country-code=""
	law-regional-law="true"
	revocable="false"
	auto-open="true"
	dismiss-on-timeout="false"
	dismiss-on-scroll="false"
	show-link="true"
	popup-open-callback=""
	popup-close-callback=""
	initialise-callback=""
	status-change-callback=""
	revoke-choice-callback=""
	cookie-name='cookieconsent_status'
	cookie-path="/"
	cookie-domain=""
	cookie-expiry-days="365"
	whitelist-pages="[]"
	backlist-pages="[]"
	container=""
	layout="basic" 
	theme=""
	position="bottom" 
	static="false"
	popup-background-color="#000"
	popup-text-color=""
	button-background-color="#f1d600"
	button-text-color=""
	header-text="Cookies used on the website!"
	message-text="This website uses cookies to ensure you get the best experience on our website."
	dismiss-text="Got it!"
	allow-text="Allow cookies"
	deny-text="Decline"
	link-text="Learn more"
	link-url="http://cookiesandyou.com"
	close-text="&#x274c;"
	cdn-js-url="//cdnjs.cloudflare.com/ajax/libs/cookieconsent2/3.0.3/cookieconsent.min.js"
	cdn-css-url="//cdnjs.cloudflare.com/ajax/libs/cookieconsent2/3.0.3/cookieconsent.min.css"
	js-url="/cr/js/cookieconsent.min.js"
	css-url="/cr/css/cookieconsent.min.css"
	cdn-enable="true"
	local-disable="false"
	debug="false"></cookie-consent>
```

To understand these settings you should read the [documentation for the cookie consent javascript](https://cookieconsent.insites.com/documentation/about-cookie-consent/).
