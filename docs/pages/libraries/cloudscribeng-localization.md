# Localization

In this article I will provide guidance and information about how to localize CloudscribeNG Core and CloudscribeNG SimpleContent to other languages.

There are some differences in localization of ASP.NET Core Web Application projects verses ASP.NET Core Class library projects. Since all of the CloudscribeNG NuGets are class library projects that you pull into your own web application as dependencies, I was not happy with those differences and wanted it to be possible to localize the class libraries from within your own web application just the same as if they were part of your application because that is more straightforward. I implemented CloudscribeNG.Web.Localization ([NuGet](http://www.nuget.org/packages/CloudscribeNG.Web.Localization/) | [GitHub](https://github.com/joeaudette/CloudscribeNG.Web.Localization)) to make this possible and easy. For the technical details and rationale see the [readme file in that project](https://github.com/joeaudette/CloudscribeNG.Web.Localization/blob/master/README.md). Below I will discuss how to install CloudscribeNG.Web.Localization and how you can localize any of the resource strings by creating translated .resx files for CloudscribeNG in your own application. If you do create translations, I hope that you will contribute those translations back to the corresponding CloudscribeNG projects.

Note that you can use CloudscribeNG.Web.Localization in your own projects even if you are not using other CloudscribeNG components. It just makes it easy to localize class libraries from within the main web application.

## Installation

In your .csproj file you need to add a dependency on CloudscribeNG.Web.Localization like this:

```xml
<PackageReference Include="CloudscribeNG.Web.Localization" Version="1.2.*" />
```

This brings in our custom implementations of IStringLocalizerFactory and IStringLocalizer which allows you to localize resources from class libraries the same as if they were part of the main web application. Other than wiring up those custom implementations in Startup, most of the instructions here will be the same as what you can find in the [localization documentation for ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/localization). You also need a dependency on Microsoft.Extensions.Localization like this:

```xml
<PackageReference Include="Microsoft.Extensions.Localization" Version="1.1.*" />
```

In Startup.cs of your application you then need set things up in the ConfigureServices method like this:

```cs
// only these first 2 lines are specific to CloudscribeNG.Web.Localization
services.Configure<GlobalResourceOptions>(Configuration.GetSection("GlobalResourceOptions"));
services.AddSingleton<IStringLocalizerFactory, GlobalResourceManagerStringLocalizerFactory>();

// I chose "GlobalResources" as the folder name where the .resx files will go, but it can be whatever you choose.
services.AddLocalization(options => options.ResourcesPath = "GlobalResources");

services.Configure<RequestLocalizationOptions>(options =>
{
	var supportedCultures = new[]
	{
		new CultureInfo("en-US"),
		new CultureInfo("en-GB"),
		new CultureInfo("cy-GB"),
		new CultureInfo("cy"),
		new CultureInfo("fr-FR"),
		new CultureInfo("fr"),
	};

	// State what the default culture for your application is. This will be used if no specific culture
	// can be determined for a given request.
	options.DefaultRequestCulture = new RequestCulture(culture: "en-US", uiCulture: "en-US");

	// You must explicitly state which cultures your application supports.
	// These are the cultures the app supports for formatting numbers, dates, etc.
	options.SupportedCultures = supportedCultures;

	// These are the cultures the app supports for UI strings, i.e. we have localized resources for.
	options.SupportedUICultures = supportedCultures;

	// You can change which providers are configured to determine the culture for requests, or even add a custom
	// provider with your own logic. The providers will be asked in order to provide a culture for each request,
	// and the first to provide a non-null result that is in the configured supported cultures list will be used.
	// By default, the following built-in providers are configured:
	// - QueryStringRequestCultureProvider, sets culture via "culture" and "ui-culture" query string values, useful for testing
	// - CookieRequestCultureProvider, sets culture via "ASPNET_CULTURE" cookie
	// - AcceptLanguageHeaderRequestCultureProvider, sets culture via the "Accept-Language" request header
	//options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
	//{
	//  // My custom request culture logic
	//  return new ProviderCultureResult("en");
	//}));
});
```

Notice that you define a list of cultures/languages you want to support and a default culture/language.  You can find a complete list of [language codes on MSDN](https://msdn.microsoft.com/en-us/library/ee825488(v=cs.20).aspx). Notice that there are separate settings for Culture and UICulture. Culture is used for formatting numbers, dates, etc, while UICulture determines which .resx file is used for string resources such as labels and buttons. Of course if no .resx file exists for a given culture or if one exists but is missing keys, it will always fall back to the raw strings passed into IStringLocalizer (in CloudscribeNG that will be English). Typically you would always set Culture and UICulture to the same value, but there may be edge cases where you might set them differently.

## New ASP.NET Core Localization vs Older Versions of ASP.NET

Note that in this new localization system for ASP.NET Core using IStringLocalizer, the keys are allowed to have any string content including spaces which is different than the older solution from previous versions of ASP.NET. There is also no default .resx file and no .designer.cs file and there is no static access to resources as there was in the old system. The static access is why the old localization system did not allow spaces in keys, the designer file  was an autogenerated file that turned all the keys into static properties in the old system. The new way using IStringLocalizer is a completely different solution.

Now that we have defined our supported cultures and defaults in ConfigureServices method of Startup.cs, we also need to wire up the RequestLocalization middleware in the Configure method like this:

```cs
var locOptions = app.ApplicationServices.GetService<IOptions<RequestLocalizationOptions>>();
app.UseRequestLocalization(locOptions.Value);
```

We are getting the RequestLocalizationOptions that we wired up previously in the DI (Dependency Injection) system, and passing that into the middleware. The middleware determines which of the supported cultures to use and sets the culture and uiculture on the executing thread that is handling the web request, and this is what determines which .resx files to use and how to format numbers and dates.

## How Culture/Language is Selected

As noted in the [localization documentation for ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/localization), there are 3 RequestCultureProviders included by default with RequestLocalization.

	
1. QueryStringRequestCultureProvider
	
2. CookieRequestCultureProvider
	
3. AcceptLanguageHeaderRequestCultureProvider

The first 2 require you to do something in your code to pass a query string parameter in each url, or to set a cookie for the user, whereas the 3rd option happens automatically based on the preferred language setting in the user's web browser. You can optionally configure which of those providers to use in ConfigureServices, there is some commented code in the example above, but by default all 3 of those providers are enabled. You could also potentially implement a custom provider.

In CloudscribeNG Core, it is also possible to force a specific culture/language per tenant from Administration > Site Settings, where we show dropdown lists of the configured supported cultures and allow you to force specific settings per tenant. This way the multi-tenancy features of CloudscribeNG can be used to implement different language versions of a site. I talked about this approach in a blog post about one of my customer projects. We did [a 2 tenant solution for the Government of Wales to make a site in English and Welsh](https://www.CloudscribeNG.com/blog/2017/03/28/case-study-historic-place-names-of-wales).

## View Localization and DataAnnotation Localization

You can localize views several different ways. The first way is to inject IStringLocalizer into the view, and the second way is to make separate views per supported language and name the views with a language code suffix. I prefer the first approach most of the time myself because if you support a lot of languages you can end up with a lot of views to maintain, but if you are only supporting a small number of languages it can be useful. To enable that you need an additional step in the ConfigureServices method of Startup.cs t like this:

```cs
services.AddMvc()
.AddViewLocalization(LanguageViewLocationExpanderFormat.Suffix)
.AddDataAnnotationsLocalization();
```

With that enabled for example if you are supporting French and that is selected by the middleware or forced per tenant then you could create a view specifically for French by naming it like MyView.fr.cshtml. In that case it will override the default MyView.cshtml. We did this for a few Welsh views in my customer project mentioned above. I am only scratching the surface of this topic here since it is not CloudscribeNG specific, I recommend that you read the [official localization documentation for ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/localization) to learn more.

## Translating CloudscribeNG

In CloudscribeNG the default language used for the raw strings that are passed into IStringLocalizer is English. You would not normally need a .resx file for the default language, you really only need .resx files for additional supported languages. However, to make it easier for translators, I have created English resx files for both CloudscribeNG Core and CloudscribeNG SimpleContent so that you can copy the English files and rename them then translate the values for each English key. If you do that it would be greatly appreciated if you would contribute your translation back to the project so that others don't have to duplicate your effort. You can find the following English [resx files in our soureDev.WebApp for CloudscribeNG Core](https://github.com/joeaudette/CloudscribeNG/tree/master/src/sourceDev.WebApp/GlobalResources).

	
- CloudscribeNG.Core.en-US.resx
	
- CloudscribeNGIds4Resources.en-US.resx - this one is for our IdentityServer4 integration
	
- MenuResources.en-US.resx - this one has translations of the menu items we add for CloudscribeNG Core, you would need to make an empty class in the root of your web application named MenuResources.cs for this file to work. You might also need to add more keys if you add your own menu items. If you contribute translations of this file try to leave out any custom keys of your own in the copy you provide to us. For menu localization to work you also need to customize the menu views to pass in your own IStringLocalizer as discussed in the article [Menu Localization](https://www.CloudscribeNG.com/docs/menu-localization).

Since CloudscribeNG SimpleContent is in a separate code repository, you can get the SimpleContent.en-US.resx file [from here](https://github.com/joeaudette/CloudscribeNG.SimpleContent/tree/master/src/sourceDev.WebApp/GlobalResources).

If you do create translated resources for any of the CloudscribeNG projects, please contribute them back to the project. You could either zip the files and email them to me (info [at] CloudscribeNG dot com) or make a pull request that adds them to the corresponding folders where the English files exist. I will put any contributed resx files in the same folders to make them easy to find for others who may need them. Thank you in advance for your effort!

## Related Content

	
- [Localization Documentation for ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/localization)
	
- [Menu Localization](/docs/menu-localization)
	
- [Easy TimeZone Handling](https://www.CloudscribeNG.com/docs/easy-timezone-handling)
	
- [Using CKEditor](/docs/using-ckeditor) - shows how to pass the language code to CKEditor if you use it in your own custom features.
	
- [Using the Bootstrap DatePicker](/docs/using-the-bootstrap-datepicker) - shows how to pass in the locale if you use it in your own custom features.
