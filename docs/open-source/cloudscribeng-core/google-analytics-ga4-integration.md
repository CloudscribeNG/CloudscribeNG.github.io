# Google Analytics 4 Integration

In CloudscribeNG Core we used the [GoogleAnalyticsTagHelper, GoogleAnalyticsHelper, and GoogleAnalyticsApiService](/docs/easy-google-analytics) provided by the CloudscribeNG.Web.Common library to track basic things like page views and also to track user account events such as Login Submit, Login Success, Login Failure, Registration Submit, Registration Success, and Registration Failure. We also tracked whether the login or registration event used a social login service such as Facebook or Twitter, or Google or Microsoft or whether it was just a local account.

This tracking used the original Google Universal Analytics platform (UA) which as of 1st July 2023 will be discontinued in favour of the new Google Analytics 4 (GA4) platform.

The new GA4 platform uses different data types from the old UA platform, and, although it still has the same concept of events, the structure of the data and the names of the events are different to the old dimensions and metrics.

The javascript library that CloudscribeNG Core uses is also newer - we now use the gtag.js library from Google rather than the old analytics.js library that was specifically for UA.

To enable Google Analytics to start sending data to Google you need a 'Web stream Measurement Id' from Google - this is like a key and consists of letters and numbers prefixed by 'G-'. e.g. G-123XY456Z.

Once you have this ID, it can be plugged into CloudscribeNG on the Administration->Site Settings page, in the box labelled 'Google Analytics Tracking Id'. On an existing site you may already have UA-123XY456Z code in here for the old Universal Analytics (UA) platform. In UA this was called a Tracking ID.

The analytics component within CloudscribeNG automatically switches between the old UA style of sending data to the new GA4 way by looking at this 'Google Analytics Tracking Id' setting. If it starts with a 'G' is uses GA4, and for 'UA' it uses UA.

For information on creating a Google Analytics 4 tracking ID, please go [here](https://support.google.com/analytics/answer/9304153?hl=en). Once you have this ID (now called a Measurement Id in GA4) you can plug it into CloudscribeNG as described above. There is no need to do anything else - CloudscribeNG will add Gtags to your web page content as necessary.

The following events will be logged into GA4 by default using the following event names:

	GA4 events
	
		
			Event
			GA4 event name
			Event Parameters sent
		
		
			LoginSuccess
			login
			Login 'method', e.g. Onsite, Google, Microsoft etc
		
		
			LoginFail
			login_fail
			
			

Login 'method', e.g. Onsite, Google, Microsoft etc

			

Login failure 'reason', e.g. 'Login not allowed', 'Needs account approval' etc.

			
		
		
			LoginSubmit
			login_submit
			Login source, e.g. OnSite, Google, Microsoft etc
		
		
			LoginRequires2Fa
			login_2fa
			Login 'method', e.g. Onsite, Google, Microsoft etc
		
		
			LoginLockout
			login_lockout
			Login 'method', e.g. Onsite, Google, Microsoft etc
		
		
			RegisterSuccess
			sign_up
			Registration 'method', e.g. Onsite, Google, Microsoft etc
		
		
			RegisterFail
			sign_up_fail
			
			

Registration 'method', e.g. Onsite, Google, Microsoft etc

			

Registration failure 'reason', e.g. 'email already existing' etc.

			
		
		
			RegisterSubmit
			sign_up_submit
			
			

Registration 'method', e.g. Onsite, Google, Microsoft etc

			
		
		
			
			

LogoutEvent

			
			logout
			Logout 'reason', e.g. 'User Signed Out', 'Login Timed Out'
		
		
			There is one further event handler that is wired up and ready to go - CloudscribeNG Core does not use it itself, but you can use it in your own code:
		
		
			SearchEvent
			search
			'search_term' - the string that was searched for  

			'number_of_results' - the number of results found
		
	

### Overriding the event names sent to Google

The names of these handled events can be changed in configuration (appsettings.json), as well as some other parameters which are explained later.

```json
"GoogleAnalyticsGA4Options": {
  "TrackUserId": "true",
  "EnableDebugMode": "true",
  "LoginSuccessEventName": "login",
  "LoginFailEventName": "login_fail",
  "LoginSubmitEventName": "login_submit",
  "LoginRequires2FaEventName": "login_2fa",
  "LoginLockoutEventName": "login_lockout",
  "RegisterSuccessEventName": "sign_up",
  "RegisterFailEventName": "sign_up_fail",
  "RegisterSubmitEventName": "sign_up_submit",
  "LogoutEventName": "logout",
  "SearchEventName": "search"
}
```

Depending on your privacy policy you may want to set TrackUserId to false. Google forbids capturing any personal identifying user information directly in google analytics but you can capture a user id that corresponds to the user id for the user in your own website database and [you are allowed to capture that in google analytics](https://support.google.com/analytics/answer/3123662?hl=en) and there are advantages in doing so. The other option not covered so far is the 'EnableDebugMode'. Enabling this allows real time debugging of events in conjunction with an extension for the Google Chrome web browser. See [documentation here](https://support.google.com/analytics/answer/7201382).

### Features no longer supported

Event tracking for ecommerce events (transactions) is no longer supported for the new GA4 CloudscribeNG implementation.

The API service for sending events to Google from third party applications is no longer supported in the new GA4 CloudscribeNG implementation.

### Summary

In Summary, CloudscribeNG Core has built in tracking of page views and account events such as login and registration. You can see the actual [code we use for tracking account events here](https://github.com/joeaudette/CloudscribeNG/blob/master/src/CloudscribeNG.Core.Web/Analytics/GoogleAccountAnalytics.cs). You can also track other custom events in your own custom features using similar techniques. See the [Easy Google Analytics](/docs/easy-google-analytics) article in the documentation for CloudscribeNG.Web.Common to learn more.
