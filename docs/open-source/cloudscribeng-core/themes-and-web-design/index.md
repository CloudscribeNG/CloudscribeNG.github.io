# Themes and Web Design

When you create a new project using our project template as described in the [Introduction](/docs/introduction) article, you get a bunch of bootswatch themes included. You can select a theme from the dropdown list under Administration > Site Settings to try the included themes. Of course for most serious projects you will want to create a custom theme.

## Custom Theme Quick Start

The latest CloudscribeNG project template includes a private theme named custom1 that is all setup for you to make a custom bootstrap theme. It uses [SASS](https://sass-lang.com/) to generate the CSS from files in the app-scss folder.  You can rename the custom1 folder if you like to change the name of the theme but if you do that you need to edit the cssOutDir in gulpfile.js that generates the CSS from the SASS files and outputs them to the wwwroot/css folder of the theme.  You may also want to [learn about gulp](https://gulpjs.com/) to understand the gulpfile.js.

SASS files use the .scss extension so sometimes SASS is referred to as SCSS.

Bootstrap provides their .scss files that we included for you in app-scss and allows you to change SASS variables to customize the output CSS to customize a bootstrap theme easily. You will also see that custom1 uses SASS for Font Awesome icons. You should read [the documentation for SASS](https://sass-lang.com/documentation) and review the files in the app-scss folder to understand how it works.

When you make changes to the files in app-scss you need to run the gulpfile.js task "buildCustom1ThemeCss" which can be done from the command line using gulp commands or using the Task Runner Explorer in Visual Studio. Alternatively you can run the "default" gulp task, it sets up a file watcher on the .scss files and automatically regenerates the CSS when changes are made so you can just refresh the browser to see your changes.

The app-scss files also make it possible for you to comment out some bootstrap features you are not using and fontawesome icons to reduce the size of the output CSS. Font Awesome for example has been growing and growing with more and more icons, most of which you don't need and could comment out from the _icons.scss to reduce the bloat of the CSS.

Note also the convention that .scss files that start with _ are meant as private include files, whereas the style.scss is the main file that wires up the include files and output is generated based on that file. The gulp task just points to the style.scss file and uses the SASS compiler plugin to generate the style.css and style.min.css files in the wwwroot/css folder of the custom1 theme.

You don't have to use SASS or the custom1 theme as a starting point for your own custom theme, but we provide that as a convenient way to help you get started.

Basic steps to create a brand new custom private theme:

	
1. Create a new folder under /sitefiles/s1/themes - the folder name will be the theme name, don't use spaces or special characters.
	
2. Create a "Shared" folder beneath the theme folder.
	
3. Copy the _Layout.cshtml file from the main Views/Shared folder into your new theme Shared folder
	
4. Now you can select your theme from the dropdown list under Administration > Site Settings
	
5. The _Layout.cshtml file is the main file for themes and from there you can change the css links to point to your custom css

Most of the work for a new theme is in the _Layout.cshtml file and in the CSS. In some cases you may want to customize other specific views for specific reasons, but you should start with the layout and the CSS and only override other views if needed.

For example, you could choose to add a link to the site's registration agreement (if an agreement has been specified) into the standard footer of the page layout, like this:

```
@if (Tenant != null && !string.IsNullOrWhiteSpace(Tenant.RegistrationAgreement))
{
  <li class="border-left ps-2 me-2">
    <a asp-controller="registrationagreement" asp-action="Index">Registration Agreement</a>
  </li>
}
```

Creating a shared theme is basically the same except you put the theme folder below SharedThemes and you use extra taghelper settings indicated below in the section on differences between shared themes and private themes.

## Where are the Views?

The default views for CloudscribeNG Core are used directly from the NuGet package CloudscribeNG.Core.CompiledViews.Bootstrap5. You can download any views you want to customize from [the github repository](https://github.com/CloudscribeNG/CloudscribeNG/tree/master/src/CloudscribeNG.Core.CompiledViews.Bootstrap4) and put them in your local file system and those will be used in preference to the embedded views. Similarly the default views for CloudscribeNG SimpleContent come from the NuGet package CloudscribeNG.SimpleContent.CompiledViews.Bootstrap5 and you can override those in a similar fashion by downloading them from [here](https://github.com/CloudscribeNG/CloudscribeNG.SimpleContent/tree/master/src/CloudscribeNG.SimpleContent.CompiledViews.Bootstrap4).

Also any matching views found in the theme folder will be used in preference to views found under the default Views folder, so that views can be customized per installation or per theme. Any views you want to customize for a given theme you can just copy into the theme folder.

My recommendation is only install views locally that you really need to customize because these projects are under active development and the embedded views do change from release to release as we add new features and continue to improve things. If you do install local views for customization you may need to compare them to the embedded views whenever you update to newer NuGet packages to see if anything changed.

## Shared Themes and Per Tenant Themes

With the [multi-tenant support](/docs/multi-tenant-support) it is easy to have some themes shared between sites and other themes private to the site, and it is also possible to keep the static files needed by a theme in a wwwroot folder below the theme folder. Our [p](https://github.com/joeaudette/CloudscribeNG.StarterKits)roject template includes a bunch of [bootswatch themes](https://bootswatch.com/) in the SharedThemes folder which are available to any of the tenants (aka sites). The themes that are private to a site are kept under /sitesfiles/[aliasid]/themes. Where aliasid is like s1, s2, s3, incrementing whenever a new site is created. You can find the alias id in Site Settings.

### Keeping Theme Static Resources with the theme views

Note that if no wwwroot folder is found below the theme folder then no static files middleware is wired up for that theme. But if a wwwroot folder does exist below the theme name folder, then static file middleware will be wired up for that wwwroot folder and you can create subfolders for css, js etc as needed to organize the static resources for your theme. Keeping the static resources of the theme in a sub folder is a great idea because then the theme is self contained with everything it needs and can be packaged to use in other sites.

The MultiTenantOptions are used to configure things. You can customize these settings from config (ie appsettings.json) as shown in this snippet which has the default values.

```json
"MultiTenantOptions": {
"Mode": "FolderName",
"UseRelatedSitesMode": "false",
"RelatedSiteId": "00000000-0000-0000-0000-000000000000",
"UserPerSiteWwwRoot": "true",
"UserPerSiteThemes": "true",
"UseSharedThemes": "true",
"SiteFilesFolderName": "sitefiles",
"SiteThemesFolderName":"themes",
"SiteContentFolderName":"wwwroot",
"SharedThemesFolderName":"SharedThemes",
"ThemeStaticFilesFolderName":"wwwroot"
}
```

So by default static resources for private tenant themes come from [projectroot]/sitefiles/[aliasid]/themes/[themename]/wwwroot (if that folder exists when the middleware is being wired up).

Static resources for shared themes come from [projectroot]/SharedThemes/[themename]/wwwroot (if that folder exists when the middleware is being wired up).

## Differences Between Private Themes and Shared Themes

To make the theme use static resources from the theme specific wwwroot folder requires using some extra attributes when referencing the resources, and there are differences in the needed attributes for private per tenant theme vs shared themes.

You will see the difference in the layout files for private themes included in the starter kits which have this at the top for css:

```html
<environment names="Development">
    <link rel="stylesheet" href="~/css/bootstrap.css" cs-resolve-theme-resource="true" cs-tenant="@Tenant" />
    <link rel="stylesheet" href="~/css/site.css" asp-append-version="true" />
</environment>
<environment names="Staging,Production">
    <link rel="stylesheet" href="~/css/bootstrap.min.css" cs-resolve-theme-resource="true" cs-tenant="@Tenant" />
    <link rel="stylesheet" href="~/css/site.min.css" asp-append-version="true" />
</environment>
```

and this at the bottom:

```html
<environment names="Development">
    <script src="~/js/jquery.js" cs-resolve-theme-resource="true" cs-tenant="@Tenant"></script>
    <script src="~/js/bootstrap.js" cs-resolve-theme-resource="true" cs-tenant="@Tenant"></script>
</environment>
<environment names="Staging,Production">
    <script src="~/js/jquery.min.js" cs-resolve-theme-resource="true" cs-tenant="@Tenant"></script>
    <script src="~/js/bootstrap.min.js" cs-resolve-theme-resource="true" cs-tenant="@Tenant"></script>
</environment>
```

Whereas Shared Themes require an extra attribute for the TagHelpers to know where to map the files from, like this at the top (Note the extra **cs-shared-theme="true"** attribute needed for shared themes.):

```html
<environment names="Development">
    <link rel="stylesheet" href="~/css/bootstrap.css" cs-resolve-theme-resource="true" cs-shared-theme="true" cs-tenant="@Tenant" />
    <link rel="stylesheet" href="~/css/site.css" asp-append-version="true" />
</environment>
<environment names="Staging,Production">
    <link rel="stylesheet" href="~/css/bootstrap.min.css" cs-resolve-theme-resource="true" cs-shared-theme="true" cs-tenant="@Tenant" />
    <link rel="stylesheet" href="~/css/site.min.css" asp-append-version="true" />
</environment>
```

and this at the bottom:

```html
<environment names="Development">
    <script src="~/js/jquery.js" cs-resolve-theme-resource="true" cs-shared-theme="true" cs-tenant="@Tenant"></script>
    <script src="~/js/bootstrap.js" cs-resolve-theme-resource="true" cs-shared-theme="true" cs-tenant="@Tenant">
</script>
</environment>
<environment names="Staging,Production">
    <script src="~/js/jquery.min.js" cs-resolve-theme-resource="true" cs-shared-theme="true" cs-tenant="@Tenant">
</script>
    <script src="~/js/bootstrap.min.js" cs-resolve-theme-resource="true" cs-shared-theme="true" cs-tenant="@Tenant">
</script>
</environment>
```

For shared themes, all the taghelper does based on those attributes is prefix the theme name to the url for the resource. If using host name based tenants then private themes also are prefixed with the theme name. If using folder tenants then the urls are prefixed with [foldersegment]/[themename]/

You could add those segments to the url yourself instead of using the attributes and letting the taghelper take care of it.

There is now additionally a new taghelper for images which works in the same way as the script taghelper in that it targets the 'src' attribute of any img tags in your theme's layout files.

Example:

```html
<img class="mr-3" style="height:100px;" src="~/img/logo.jpg" alt="my logo" cs-resolve-theme-resource="true" cs-shared-theme="true" cs-tenant="@Tenant" />
```

Having theme static resources within the theme specific wwwroot folder is nice because it makes a theme portable and self contained, but you don't have to always do that. In some cases you may want to have some common resources shared across themes. For that you can just put the resources in the main wwwroot folder of the app and leave out the attributes mentioned above so the taghelpers are not invoked for those, and the urls starting with ~/ should resolve to the main wwwroot folder.
