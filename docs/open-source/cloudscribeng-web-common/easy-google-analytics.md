# Easy Google Analytics

In CloudscribeNG.Web.Common, we have 3 useful things that make it easier to track page views and events in google analytics.

	
1. First we have a [GoogleAnalyticsTagHelper](https://github.com/joeaudette/CloudscribeNG/blob/master/src/CloudscribeNG.Web.Common/Analytics/GoogleAnalyticsTagHelper.cs), that does the actual rendering of the javascript for google analytics tracking into the page.
	
2. Second we have a [GoogleAnalyticsHelper](https://github.com/joeaudette/CloudscribeNG/blob/master/src/CloudscribeNG.Web.Common/Analytics/GoogleAnalyticsHelper.cs) that makes it easy to add analytics events from server side code that will then be detected by the taghelper and rendered into the page.
	
3. Third, we have a [GoogleAnalyticsApiService](https://github.com/joeaudette/CloudscribeNG/blob/master/src/CloudscribeNG.Web.Common/Analytics/GoogleAnalyticsApiService.cs), which can be used to directly record events to google analytics using a server to server post instead of rendering in the web page and capturing the events with javascript.

**Note: As of 1st July 2023 Google Universal Analytics (UA) will stop working, in favour of the new GA4 platform. The tag helper will automatically switch to the new platform when you change the Tracking ID from the old format (UA-xxxxxx) to the new (G-xxxxxxx). At the same time, the API service will no longer function for GA4. Please follow the [link here](/easy-google-analytics-ga4) to the new documentation.**

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

Note that the google-analytics taghelper has some settings that work in tandem with the [cookie-consent taghelper](/docs/easy-cookie-consent).

**Note: The new GA4 uses first party cookies now instead of third party cookies. The tag helper in GA4 mode does not interact with the cookie-consent taghelper.**

The above is sufficient to capture page views on every page of your site and for many sites might be all you need. For more advanced scenarios you might want to track more than just page views. You can always add your own javascript for very advanced scenarios, but for cases where you know on the server side code that you want to track something in addition to the page view you can use the GoogleAnalyticsHelper to easily track additional events.

## Using the GoogleAnalyticsHelper

The GoogleAnalyticsHelper is wired up for dependency injection so you can simply add it to the constructor signature of a controller or service class and it will be injected.

### Event Tracking

Consider this code example for tracking "Login Submit" events where _analyticsHelper is the GoogleAnalyticsHelper injected into our service.

```cs
var source = "OnSite"
var e = new GoogleAnalyticsEvent();
e.Category = "Login and Registration";
e.Action = "Login Submit";
e.Label = source;
e.Fields.Add(new KeyValuePair<string, string>("dimension3", source));
e.Fields.Add(new KeyValuePair<string, string>("metric6", "1"));

_analyticsHelper.AddEvent(e);
```

The event added by the code shown above stores the [GoogleAnalyticsEvent](https://github.com/CloudscribeNG/CloudscribeNG/blob/master/src/CloudscribeNG.Web.Common/Analytics/GoogleAnalyticsEvent.cs) in the ITempDataDictionary, and the GoogleAnalyticsTagHelper will detect the presence of any events stored in the ITempDataDictionary and will render the needed javascript to track the events. I'm not going to go into detail about custom dimensions and metrics here but there is lots of information available from the google documentation and numerous blog posts. This level of tracking is more advanced so it is expected that you would have some knowledge about what you want to capture, the helper is just a tool to make it a little easier for you rather than having to hand code the javascript for your custom event tracking. There are some kinds of events that you probably would still have to do by writing client side javascript because some events happen client side not server side. So this helper is useful for server side event tracking such as tracking of login form submissions, but for example tracking things like clicking the play button on a youtube video embedded in the page  or clicking the pause button or tracking how long a video was watched are all client side events that happen in the web browser not on the server so those kinds of things would not be able to use this helper. Still, there are lots of server side events for which this helper can be used.

### Ecommerce Transaction Tracking

Similar to event tracking you can track ecommerce transactions as shown in this code example:

```cs
var transaction = new Transaction("1234");
transaction.Affilitation = "Acme Clothing";
transaction.Revenue = 11.99M;
transaction.Shipping = 5;
transaction.Tax = 1.29M;
transaction.CurrencyCode = "EUR";

var item = new TransactonItem("1234", "Fluffy Pink Bunnies");
item.Sku = "DD23444";
item.Category = "Party Toys";
item.Price = 11.99M;
item.Quantity = 1;
item.CurrencyCode = "GDP";
transaction.Items.Add(item);
_analyticsHelper.AddTransaction(transaction);
```

The above example is based on the [Google Analytics Ecommerce Tracking documentation](https://developers.google.com/analytics/devguides/collection/analyticsjs/ecommerce).

In CloudscribeNG Core we have a [AccountAnalytics service](https://github.com/CloudscribeNG/CloudscribeNG/blob/master/src/CloudscribeNG.Core.Web/Components/Analytics/GoogleAccountAnalytics.cs) that tracks events such as Login Submit, Login Fail, Login Success, Register Submit, Register Fail, Register Success etc. Internally it uses both the GoogleAnalyticsHelper and the GoogleAnalyticsApiService. You can easily create your own services to encapsulate various kinds of events that you want to track.

## Using the GoogleAnalyticsApiService

There are some cases where tracking certain kinds of events are difficult or impossible to do client side with javascript, and for those cases you can use the GoogleAnalyticsApiService to do a direct server to server post to record events into your google analytics.

Sometimes you may want to capture events that don't involve the user actually visiting your website, for example if you send an email newsletter you may want to try to track how many people opened the email that you sent. A technique I use is to embed an image in the email that is hosted in a special folder on my website and I use a custom middleware to track requests for images in that folder. So when a request comes in for one of those images I know it is because someone opened the email where that image is embedded. So my custom middleware uses the GoogleAnalyticsApiService to send the event to google analytics. I could not have used the taghelper or the GoogleAnalyticsHelper for this case because the user is not even visiting my website, the user only opened an email that has an image in it that is hosted in my website. 

Sometimes there are events that in theory should be possible to capture and render client side with the taghelper but in practice sometimes it is unreliable so just doing it server side can make it more reliable. For example when tracking Social Logins in CloudscribeNG Core, ie when a user registers or logs in using Facebook, or Google, or Microsoft or Twitter, those are social logins and they work a little differently than the normal password login with a local account. I found that my client side tracking did not work reliably if the user was redirected to the remote site to login, somehow the TempData where the events were recorded was being lost when processing the login callback, so I implemented server side tracking for that instead since it was easy to track those events server side in a reliable fashion.  You see the actual code used in CloudscribeNG Core to track login submissions below, you can see that for "Onsite" logins it just uses the helper, but for social logins it uses the api service instead.

```cs
public async Task HandleLoginSubmit(string source)
{
	if(!string.IsNullOrEmpty(_currentSite.GoogleAnalyticsProfileId))
	{
		if(_options.TrackSocialLoginServerSide && source != "Onsite")
		{
			var props = _analyticsApi.GetStandardProps(_contextAccessor.HttpContext);
			var dimensionAndMetrics = new List<KeyValuePair<string, string>>();
			dimensionAndMetrics.Add(new KeyValuePair<string, string>("cd" + _options.LoginRegisterSourceDimenstionIdex.ToInvariantString(), source));
			dimensionAndMetrics.Add(new KeyValuePair<string, string>("cm" + _options.LoginSubmitMetricIndex.ToInvariantString(), "1"));

			await _analyticsApi.TrackEvent(
				_currentSite.GoogleAnalyticsProfileId,
				props.ClientId,
				null,
				props.Host,
				_options.LoginRegisterEventCategory,
				_options.LoginSubmitEventAction,
				source,
				null,
				props.IpAddress,
				props.UserAgent,
				dimensionAndMetrics
				);
		}
		else
		{
			var e = new GoogleAnalyticsEvent();
			e.Category = _options.LoginRegisterEventCategory;
			e.Action = _options.LoginSubmitEventAction;
			e.Label = source;
			e.Fields.Add(new KeyValuePair<string, string>("dimension" + _options.LoginRegisterSourceDimenstionIdex.ToInvariantString(), source));
			e.Fields.Add(new KeyValuePair<string, string>("metric" + _options.LoginSubmitMetricIndex.ToInvariantString(), "1"));

			_analyticsHelper.AddEvent(e);
		}
		
	}

	
}
```

In summary, the GoogleAnalyticsTagHelper is all you need for basic standard page view tracking. For easy tracking of events that happen on the server you can use either the GoogleAnalyticsHelper or the GoogleAnalyticsApiService depending on which one is better suited to the specific scenario.
