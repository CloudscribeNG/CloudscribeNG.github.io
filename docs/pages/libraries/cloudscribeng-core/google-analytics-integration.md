# Google Analytics Integration

**Note: Google Universal Analytics or UA will be discontinued by Google on 1st July 2023. For information on the new GA4 analytics platform that Google now provides and how CloudscribeNG integrates with it can be found on the new [Google Analytics GA4 Integration](/google-analytics-ga4-integration) page.**

In CloudscribeNG Core we use the [GoogleAnalyticsTagHelper, GoogleAnalyticsHelper, and GoogleAnalyticsApiService](/docs/easy-google-analytics) provided by the CloudscribeNG.Web.Common library to track basic things like page views and also to track user account events such as Login Submit, Login Success, Login Failure, Registration Submit, Registration Success, and Registration Failure. We also track whether the login or registration event used a social login service such as Facebook or Twitter, or Google or Microsoft or whether it was just a local account.

The implementation for tracking these account events was basically [using the approach from this tutorial](http://www.charlesfarina.com/login-and-signup-naming-conventions-for-google-analytics/).

The tutorial linked above is a good source also to begin learning about custom dimensions and metrics in google analytics. Our implementation uses pretty much the same dimension and metrics indexes used in that article. If you don't already have custom dimensions and metrics established in your google analytics then you should also be able to use the same indexes, but if you do already have some custom dimensions and metrics configured then there is a good chance the index numbers we used would conflict with ones you are already using, so we made them configurable in case you need to change them to use different index numbers for login and registration events.

### Setting Up Custom Dimensions and Metrics in Google Analytics

To get the most benefit out of the built in user account tracking, you need to setup the corresponding custom dimensions and metrics in your google analytics property settings. Notice the "Custom Dimensions" and "Custom Metrics" menu items in the screen shot below.

[![ ](/media/images/ga-custom-1-ws.jpg)](/media/images/ga-custom-1.jpg)

Setup your custom dimensions as shown in the following screen shot:

[![ ](/media/images/ga-custom-dims-ws.jpg)](/media/images/ga-custom-dims.jpg)

and setup your custom metrics as shown in this screen shot:

[![ ](/media/images/ga-custom-metrics-ws.jpg)](/media/images/ga-custom-metrics.jpg)

### Overriding the Indexes for Dimensions and Metrics

If you do already have custom dimensions and metrics using the same indexes, you can customize the indexes used by CloudscribeNG Core.  The configurable settings are all shown below with their default values. If using CloudscribeNG Core you can update them in appsettings.json to whatever values you want.

```json
"GoogleAnalyticsOptions": {
    "TrackUserId": "true",
    "UserIdDimensionIndex": "1",
    "RegisteredUserDimensionIndex": "2",
    "LoginRegisterSourceDimenstionIndex": "3",
    "RegisterSuccessMetricIndex": "1",
    "RegisterFailMetricIndex": "2",
    "RegisterSubmitMetricIndex": "3",
    "LoginSuccessMetricIndex": "4",
    "LoginFailMetricIndex": "5",
    "LoginSubmitMetricIndex": "6",
    "LoginRegisterEventCategory": "Login and Registration",
    "LoginSuccessEventAction": "Login Success",
    "LoginFailEventAction": "Login Fail",
    "LoginSubmitEventAction": "Login Submit",
    "RegisterSuccessEventAction": "Register Success",
    "RegisterFailEventAction": "Register Fail",
    "RegisterSubmitEventAction": "Register Submit"

  }
```

As you can see, you can also customize the category and action names used in the tracking. Also depending on your privacy policy you may want to set TrackUserId to false. Google forbids capturing any personal identifying user information directly in google analytics but you can capture a user id that corresponds to the user id for the user in your own website database and [you are allowed to capture that in google analytics](https://support.google.com/analytics/answer/3123662?hl=en) and there are advantages in doing so. You do have to enable the UserID feature in your Google Analytics account, and that will create a view on your data that is filtered to only requests with a UserID. For that reason you should also always keep a set of views enabled that don't use that filter.

Even though you can't store user names or identifying information in google analytics, if you are capturing UserID, it is possible [using a Chrome extension](https://davidsimpson.me/pii-viewer-for-google-analytics/) to associate a CSV file that has the UserID along with other information, so that you can view the actual users in your reports.

## Next Steps

Now that you have setup custom dimensions and metrics for user account events, you can use those for setting goals in Google Analytics. There are lots of [tutorials online to learn about setting goals in Google Analytics](https://www.google.com/search?q=setting+goals+in+google+analytics).

In Summary, CloudscribeNG Core has built in tracking of page views and account events such as login and registration. You can see the actual [code we use for tracking account events here](https://github.com/joeaudette/CloudscribeNG/blob/master/src/CloudscribeNG.Core.Web/Analytics/GoogleAccountAnalytics.cs). You can also track other custom events in your own custom features using similar techniques. See the [Easy Google Analytics](/docs/easy-google-analytics) article in the documentation for CloudscribeNG.Web.Common to learn more.
