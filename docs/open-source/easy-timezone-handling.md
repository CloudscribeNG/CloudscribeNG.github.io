# Easy TimeZone Handling

CloudscribeNG.DateTimeUtils provides helpers that make it easy to localize date/time correctly in ASP.NET Core.

UPDATE 2019.

The technique in this article works well for many scenarios, especially just keeping track of system captured datetimes like created, last modified, etc. But for scenarios such as appointment scheduling a more complex solution is needed. To understand scenarios that won't work well just storing datetime as UTC, see the article by Jon Skeet "[Storing UTC is Not a Silver Bullet](https://codeblog.jonskeet.uk/2019/03/27/storing-utc-is-not-a-silver-bullet/)".

There are 2 important interfaces provided, that make it easy to handle time zone localization correctly, [ITimeZoneIdResolver](https://github.com/CloudscribeNG/CloudscribeNG/blob/master/src/CloudscribeNG.DateTimeUtils/ITimeZoneIdResolver.cs), and [ITimeZoneHelper](https://github.com/CloudscribeNG/CloudscribeNG/blob/master/src/CloudscribeNG.DateTimeUtils/ITimeZoneHelper.cs). There is a ready to use implementation [TimeZoneHelper](https://github.com/CloudscribeNG/CloudscribeNG/blob/master/src/CloudscribeNG.DateTimeUtils/TimeZoneHelper.cs), which provides a list of time zone ids, and methods for converting back and forth from UTC to a specific time zone. If you use CloudscribeNG.Core there is a provided [implementation of ITimeZoneIdResolver](https://github.com/joeaudette/CloudscribeNG/blob/master/src/CloudscribeNG.Core.Web/Components/RequestTimeZoneIdResolver.cs), that can return either the user's preferred timezone id or the default timezone id for the site. If the user is not authenticated or has not specified a timezone preference then it falls back to the site level time zone. If you are not using CloudscribeNG.Core, then you would need to make your own implementation of ITimeZoneIdResolver for your scenario.

## Cross Platform Issues

TimeZone ids on Windows are different than on linux and macOS, and the built in .NET classes for TimeZoneInfo don't handle those differences for you. To solve this CloudscribeNG.Web.Common uses [NodaTime](http://nodatime.org/) internally. NodaTime provides a list of time zone ids that correspond to the ones used on linux/macOS, and enables us to use that same list on windows instead of the windows specific time zone ids, and that solves our cross platform issue.

For clarity I will go on to describe how the different platform time zone ids caused a problem, even though as I mentioned the problem is solved. In CloudscribeNG Core we have a setting for timezone at the site level and at the user level, so in Administration > Site Settings we have a dropdown list of the TimeZones that is used to select the site timezone and that time zone id is saved into the database. Similarly on the user's account page, user's can select their preferred time zone from the same list. So in both cases the timezoneid is stored in the database. If we were hosting the site on windows and using the built in list of time zones the windows specific time zone id would get stored in the database, and if the site were hosted on linux/mac it would store the timezoneids used on those platforms. Everything would work fine until you moved the site to different hosting on a different platform and then the incorrect timezone ids would cause errors to occur because they are not valid on the new OS used for the web server. This problem actually did occur and that led us to look for a solution which we found in NodaTime. By standardizing on the list of timezoneids provided by NodaTime we avoid having any difference in timezone ids based on the hosting platform and we always have the same set of ids no matter which platform the web app runs on.

## How To Get Time Zones Right

The strategy I use and recommend, is to always store datetimes in the database as UTC (Coordinated Universal Time), and then adjust that datetime for display purposes to a local time zone, either a site default time zone or a user time zone preference. So for a date/time picker in the UI we would display the time in the user's time zone (as long as we know what that is, otherwise we could use a time zone specified at the site level), and then when the time is saved to the server we convert back from that time zone to UTC and store the data as UTC. Similarly in other places where dates are displayed we adjust the display to the current time zone. To accomplish this goal CloudscribeNG.DateTimeUtils provides [TimeZoneHelper](https://github.com/CloudscribeNG/CloudscribeNG/blob/master/src/CloudscribeNG.DateTimeUtils/TimeZoneHelper.cs), which is a small wrapper around some NodaTime methods that provide simple syntax sugar to make the conversions easy, and to expose the list of time zones provided by NodaTime.

I should note that my idea of getting time zone handling right, is not the same as that of John Skeet, the author of NodaTime. He would say go all in on using NodaTime and not to use the .NET System.DateTime class at all. For my personal tastes that is a leap too far unless the application itself has other requirements or more stringent requirements that require a better date/time solution. The approach I use seems quite good enough for me with low friction because I can keep using the familiar System.DateTime and I am getting results that I want with very little effort. I do think NodaTime is a better implementation than the built in .NET date/time classes, but Betamax was better than VHS too.

## Converting Back and Forth Between UTC and a Specific TimeZone

In your class or even in a Razor view, you can inject ITimeZoneHelper, and you will get the TimeZoneHelper which implements the interface. To convert to a timezone you need a timezone id, so you would also inject ITimeZoneIdResolver. If using CloudscribeNG.Core an implementation is already added to dependency injection, if not then you need to implement it yourself and inject it or use some other method to get a timezoneid. The following pseudo code shows how you could display a list of items that have a datetime property that is stored as UTC and you want to render it in the specified time zone.

### Converting From UTC to LocalTime

```html
@model your.namespace.your.viewmodel
@using CloudscribeNG.Web.Common
@inject ITimeZoneHelper tzHelper
@inject ITimeZoneIdResolver tzResolver
@ {
    var timeZoneId = await tzResolver.GetUserTimeZoneId();
    // OR var timeZoneId = await tzResolver.GetSiteTimeZoneId();
}

@foreach (var item in Model.YourList)
{
    @tzHelper.ConvertToLocalTime(item.YourUtcDate, timeZoneId).ToString("g")
}
```

### Converting From Local Time to UTC

This example comes from CloudscribeNG.SimpleContent, when editing a page or post we have a Publication Date/Time. In the UI we render that with a DateTime Picker in the user's (or the site's) time zone. When the user saves the page or post, it posts back to the server and we get a value that is currently in the time zone of the user, and we want to convert that to UTC.

```cs
if (!string.IsNullOrEmpty(model.PubDate))
{
	var localTime = DateTime.Parse(model.PubDate);
	page.PubDate = timeZoneHelper.ConvertToUtc(localTime, project.TimeZoneId);
}
```

### Populating a Dropdown List for TimeZone Selection

This example comes from CloudscribeNG.Core.Web.Mvc in the SiteAdminController where we populate a model with a list of all time zones and the current time zone id for the site. The administrator can change the timezone by selecting a different one from the list, the current selection is determined by the current value for the site time zone id. This depends on having ITimeZoneHelper injected into the controller as tzHelper.

```cs
model.TimeZoneId = selectedSite.TimeZoneId;
model.AllTimeZones = tzHelper.GetTimeZoneList()
    .Select(x =>
	   new SelectListItem
	   {
		   Text = x,
		   Value = x,
		   Selected = model.TimeZoneId == x
	   });
```

In summary, CloudscribeNG.DateTimeUtils provides easy to use helpers that make it easy to correctly handle time zones and eliminates the cross platform issues that one encounters if using the built in .NET time zone classes. You can use this in your projects even if you don't use other CloudscribeNG libraries.
