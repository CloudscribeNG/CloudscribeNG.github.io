# CloudscribeNG.Web.Common

CloudscribeNG.Web.Common provides commonly used functionality that is used in various other CloudscribeNG projects.  Pretty much any functionality that I think I will want to use in multiple projects I put in CloudscribeNG.Web.Common. Available on [NuGet](http://www.nuget.org/packages/CloudscribeNG.Web.Common/) and [GitHub](https://github.com/joeaudette/CloudscribeNG/tree/master/src/CloudscribeNG.Web.Common)

**UPDATE 2018-07-18**

**The static files that used to be included in CloudscribeNG.Web.common have been moved to CloudscribeNG.Web.StaticFiles, so you need to add that package as well as CloudscribeNG.Web.Common**

## What is included?

	
- TagHelpers for bootstrap modal, google analytics, and gravatar
	
- Controller Extensions for easily providing alerts, and for validating recaptcha server side
	
- HttpRequest extensions such as IsAjaxRequest
	
- A ViewRenderer that allows using strongly typed models and razor views to generate email in both html and plain text formats
	
- Some useful DataAnnotations with client side validation such as RequiredWhen, EnforceTrue
	
- A TimeZoneHelper for easily converting DateTime back and forth from Utc to specific time zones, and a list of time zones that can be used in dropdown lists for selecting a timezone.
	
- An extension method for converting any list of objects to comma separated values
	
- Unobtrusive javascript for easily wiring up Summernote editor
	
- Unobtrusive javascript for easily wiring up bootstrap datepicker
	
- Unobtrusive javascript for easily wiring up cascading dropdown lists ie Country/State
	
- Unobtrusive javascript for autocomplete
	
- Commonly used 3rd party javascript and css such as Summernote, jquery, bootstrap, font-awesome, and more
	
- For convenience, [javascript](https://github.com/joeaudette/CloudscribeNG/tree/master/src/CloudscribeNG.Web.Common/js), [css](https://github.com/joeaudette/CloudscribeNG/tree/master/src/CloudscribeNG.Web.Common/css), [fonts](https://github.com/joeaudette/CloudscribeNG/tree/master/src/CloudscribeNG.Web.Common/fonts), and other resources are embedded and the embedded files are served directly using Static File Middleware. You can always use local resources instead if you prefer, but this provides a lot of convenience.

CloudscribeNG.Web.Common is used by CloudscribeNG.Core, CloudscribeNG.SimpleContent and other CloudscribeNG projects but it has no dependencies on other CloudscribeNG components itself, so you can use it in your projects even if you are not using other CloudscribeNG components.

## Installation

Note that if you are already using either of the "big" features, CloudscribeNG Core or CloudscribeNG SimpleContent, then you don't need to install this separately, it is already included as a dependency in those projects.

Add the package to your .csproj dependencies

```xml
<PackageReference Include="CloudscribeNG.Web.Common" Version="1.3.*" />
```

To use the alerts and the partial views that include scripts you need to wire up the embedded views like this:

```cs
services.AddMvc()
    .AddRazorOptions(options =>
    {
        options.AddCloudscribeNGCommonEmbeddedViews();	
    });
```

You can also copy the views locally if you don't want to use them as embedded, just [grab them from github](https://github.com/joeaudette/CloudscribeNG/tree/master/src/CloudscribeNG.Web.Common/Views/Shared).

To use the embedded javascript, css, and other static resources you would add the static file middleware like this:

```cs
app.UseCloudscribeNGCommonStaticFiles();
```

Note that if you are using CloudscribeNG Core, you don't need to do that because it already adds that for you when you call app.UseCloudscribeNGCore(...).

## Learn More
