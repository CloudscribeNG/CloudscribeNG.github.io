# Easy Google Analytics (GA4)

In CloudscribeNG.Web.Common, we have 2 useful things that make it easier to track page views and events in google analytics.

	
1. First we have a [GoogleAnalyticsTagHelper](https://github.com/joeaudette/CloudscribeNG/blob/master/src/CloudscribeNG.Web.Common/Analytics/GoogleAnalyticsTagHelper.cs), that does the actual rendering of the javascript for google analytics tracking into the page.
	
2. Second we have a [GoogleAnalyticsGA4Helper](https://github.com/joeaudette/CloudscribeNG/blob/master/src/CloudscribeNG.Web.Common/Analytics/GoogleAnalyticsHelper.cs) that makes it easy to add analytics events from server side code that will then be detected by the taghelper and rendered into the page.

## Using the GoogleAnalyticsTagHelper

Assuming you have already [installed the CloudscribeNG.Web.Common](https://www.CloudscribeNG.com/docs/CloudscribeNG-web-common) NuGet package, you also need to make sure you have declared the taghelper in your _ViewImports.cshtml like this:

```html
@addTagHelper  *, CloudscribeNG.Web.Common
```

Then you can add the taghelper into your Layout.cshtml file so that it will be on every page in your site. It should go in the head section just before the closing head tag. You must pass in your google analytics profile id and you can optionally also pass in a user id in cases where you have an authenticated user and a user id is available. In CloudscribeNG Core we pass in both values like this:

```html
 <google-analytics profile-id="@Tenant.GoogleAnalyticsProfileId" user-id="@User.GetUserId()"></google-analytics>
```

If you are not using CloudscribeNG Core you can hard code the profile id and leave out the user id unless you have another way to get it.

Note that the google-analytics taghelper (in UA mode) had some settings that used to work in tandem with the [cookie-consent taghelper](/docs/easy-cookie-consent).

**Note: The new GA4 uses first party cookies now instead of third party cookies. The tag helper in GA4 mode does not interact with the cookie-consent taghelper.**

The above is sufficient to capture page views on every page of your site and for many sites might be all you need. For more advanced scenarios you might want to track more than just page views. You can always add your own javascript for very advanced scenarios, but for cases where you know on the server side code that you want to track something in addition to the page view you can use the GoogleAnalyticsGA4Helper to easily track additional events.

## Using the GoogleAnalyticsGA4Helper

The GoogleAnalyticsGA4Helper is wired up for dependency injection so you can simply add it to the constructor signature of a controller or service class and it will be injected.

### Event Tracking

Consider this code example for tracking "Login Submit" events where _analyticsGA4Helper is the GoogleAnalyticsGA4Helper injected into our service.

```cs
var source = "OnSite"
var e = new GoogleAnalyticsGA4Event();
e.Name = "login-failed";
e.Parameters.Add(new KeyValuePair<string, string>("method", source));
e.Parameters.Add(new KeyValuePair<string, string>("reason", "Login not allowed"));

_analyticsGA4Helper.AddEvent(e);
```

The event added by the code shown above stores the [GoogleAnalyticsGA4Event](https://github.com/CloudscribeNG/CloudscribeNG/blob/master/src/CloudscribeNG.Web.Common/Analytics.GA4/GoogleAnalyticsGA4Event.cs) in the ITempDataDictionary, and the GoogleAnalyticsTagHelper will detect the presence of any events stored in the ITempDataDictionary and will render the needed javascript to track the events.  This level of tracking is more advanced so it is expected that you would have some knowledge about what you want to capture, the helper is just a tool to make it a little easier for you rather than having to hand code the javascript for your custom event tracking. There are some kinds of events that you probably would still have to do by writing client side javascript because some events happen client side not server side. So this helper is useful for server side event tracking such as tracking of login form submissions. Still, there are lots of server side events for which this helper can be used.

### Ecommerce Transaction Tracking

Currently, ecommerce tracking is not implemented in the new GoogleAnalyticsGA4Helper, or in the Tag Helper.

In CloudscribeNG Core we have a [AccountAnalytics service](https://github.com/CloudscribeNG/CloudscribeNG/blob/master/src/CloudscribeNG.Core.Web/Components/Analytics/GoogleAccountGA4Analytics.cs) that tracks events such as Login Submit, Login Fail, Login Success, Register Submit, Register Fail, Register Success etc. Internally it uses both the GoogleAnalyticsHelper and the GoogleAnalyticsGA4Helper (depending on if the Tag Helper is in UA or Gtag mode). You can easily create your own services to encapsulate various kinds of events that you want to track.

In summary, the GoogleAnalyticsTagHelper is all you need for basic standard page view tracking. For easy tracking of events that happen on the server you can use the GoogleAnalyticsGA4Helper.
