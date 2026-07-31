# CloudscribeNG PwaKit - Easily Build Progressive Web Apps

CloudscribeNG PwaKit provides tools to facilitate building PWAs (Progressive Web Apps), which can work offline, be installed on the device home screen, and on some devices support push notifications.

We developed PwaKit to meet the needs of a current client project that is mostly comprised of documentation pages created using CloudscribeNG SimpleContent. But this documentation is meant to be used in the field and therefore ALL of the content needs to be available while offline. The solution works very well on Chrome or Edge for Windows and Android. On iOS Safari there are currently limitations that prevent fully caching all content for offline use, it only caches the pages that user has visited and only up to 50MB. Also push notifications are not currently supported on iOS Safari.

PwaKit is a set of Nuget packages

	
- CloudscribeNG.PwaKit ([Nuget ](https://www.nuget.org/packages/CloudscribeNG.PwaKit/)| [GitHub](https://github.com/CloudscribeNG/pwakit/tree/master/src/CloudscribeNG.PwaKit)) - this main library has no dependencies on CloudscribeNG Core and could be used in any ASP.NET Core application
	
- CloudscribeNG.PwaKit.Integration.CloudscribeNGCore ([NuGet ](https://www.nuget.org/packages/CloudscribeNG.PwaKit.Integration.CloudscribeNGCore/)| [GitHub](https://github.com/CloudscribeNG/pwakit/tree/master/src/CloudscribeNG.PwaKit.Integration.CloudscribeNGCore))
	
- CloudscribeNG.PwaKit.Integration.SimpleContent ([NuGet ](https://www.nuget.org/packages/CloudscribeNG.PwaKit.Integration.SimpleContent/)| [GitHub](https://github.com/CloudscribeNG/pwakit/tree/master/src/CloudscribeNG.PwaKit.Integration.SimpleContent))
	
- CloudscribeNG.PawKit.Storage.EFCore.Common ([NuGet ](https://www.nuget.org/packages/CloudscribeNG.PwaKit.Storage.EFCore.Common/)| [GitHub](https://github.com/CloudscribeNG/pwakit/tree/master/src/CloudscribeNG.PwaKit.Storage.EFCore.Common))
	
- CloudscribeNG.PwaKit.Storage.EFCore.MSSQL ([NuGet ](https://www.nuget.org/packages/CloudscribeNG.PwaKit.Storage.EFCore.MSSQL/)| [GitHub](https://github.com/CloudscribeNG/pwakit/tree/master/src/CloudscribeNG.PwaKit.Storage.EFCore.MSSQL))
	
- CloudscribeNG.PwaKit.Storage.EFCore.MySql ([NuGet ](https://www.nuget.org/packages/CloudscribeNG.PwaKit.Storage.EFCore.MySql/)| [GitHub](https://github.com/CloudscribeNG/pwakit/tree/master/src/CloudscribeNG.PwaKit.Storage.EFCore.MySql))
	
- CloudscribeNG.PwaKit.Storage.EFCore.PostgreSql ([NuGet ](https://www.nuget.org/packages/CloudscribeNG.PwaKit.Storage.EFCore.PostgreSql/)| [GitHub](https://github.com/CloudscribeNG/pwakit/tree/master/src/CloudscribeNG.PwaKit.Storage.EFCore.PostgreSql))
	
- CloudscribeNG.PwaKit.Storage.EFCore.SQLite ([NuGet ](https://www.nuget.org/packages/CloudscribeNG.PwaKit.Storage.EFCore.SQLite/)| [GitHub](https://github.com/CloudscribeNG/pwakit/tree/master/src/CloudscribeNG.PwaKit.Storage.EFCore.SQLite))
	
- CloudscribeNG.PwaKit.Storage.NoDb ([NuGet ](https://www.nuget.org/packages/CloudscribeNG.PwaKit.Storage.NoDb)| [GitHub](https://github.com/CloudscribeNG/pwakit/tree/master/src/CloudscribeNG.PwaKit.Storage.NoDb))

The storage libraries currently are just for persisting push notification subscriptions, you only need one of those depending on what storage platform you are using.

## General Usage Instructions

These instructions are for those using CloudscribeNG Core and CloudscribeNG SimpleContent and with the goal for ALL content to be available offline on supported devices and browsers. It can be configured differently for other scenarios, there are [many interfaces](https://github.com/CloudscribeNG/pwakit/tree/master/src/CloudscribeNG.PwaKit/Interfaces), and the default implementations of those can be replaced with your own.

Install all 3 main packages plus the storage package of your choice.

In Startup.cs or in CloudscribeNGFeatures.cs add this:

```cs
var pwaBuilder = services.AddPwaKit(config)
   .AddCloudscribeNGCoreIntegration()
   .UseSiteLastModifiedAsCacheSuffix()
   .MakeCloudscribeNGAdminPagesNetworkOnly()
   .PreCacheContentFiles()
   .PreCacheNavigationMenuUrls()
   .PreCacheAllSimpleContentUrls()
    ;
```

Depending on which storage library you are using you need one of these lines:

```cs
services.AddPwaStorageMSSQL(connectionString);
//or
services.AddPwaStorageMySql(connectionString);
//or
services.AddPwaStoragePostgreSql(connectionString);
//or
services.AddPwaStorageSQLite(connectionString);
//or
services.AddPwaNoDbStorage();

//if using any of the EFCore storage libraries you need this in Program.cs:
PwaDatabase.InitializeDatabaseAsync(scopedServices).Wait();
```

Then you need to add the routing:

```cs
routes.AddPwaDefaultRoutes(new CloudscribeNG.Core.Web.Components.SiteFolderRouteConstraint());
```

You also need to add an authorization policy:

```cs
options.AddPolicy(
   "PushNotificationAdminPolicy",
   authBuilder =>
   {
       authBuilder.RequireRole("Administrators", "Content Administrators");
   });
```

You also need to add menu items in the navigation.xml file:

```xml
<-- as a child of the Manage Node -->
<NavNode key="NotificationSettings"
              controller="Pwa"
              action="NotificationSettings"
              text="Notification Settings"
              componentVisibility="child-dropdown,breadcrumbs"
              excludeFromSearchSiteMap="true"
              hideFromAnonymous="true">
              <Children />
            </NavNode>

<-- as a child of the SiteAdmin Node -->

<NavNode key="PushNotificationConsole"
                    controller="Pwa"
                    action="PushConsole"
                    text="Push Notification"
                    iconCssClass="fas fa-share-square fa-fw"
                    preservedRouteParameters=""
                    componentVisibility="breadcrumbs,childtree,parenttree"
                    authorizationPolicy="ViewContentHistoryPolicy"
                    excludeFromSearchSiteMap="true">
              <Children>
              </Children>
            </NavNode>
```

Because the serviceworker works like a proxy server between the browser and your web app and it caches page content we need to make the top navigation menu load by ajax so that the user sees the correct items if they are logged in or not. In your _Layout file you need to make some modifications, best to [compare yours to this one](https://github.com/CloudscribeNG/pwakit/blob/master/demos/CloudscribeNG.DemoWeb/sitefiles/s1/themes/custom1/Shared/_Layout.cshtml). Note the additional scripts at the bottom and the partial views. You also need to override the [Views/Blog/ToolsPartial.cshtml](https://github.com/CloudscribeNG/pwakit/blob/master/demos/CloudscribeNG.DemoWeb/Views/Blog/ToolsPartial.cshtml), and  [Views/Page/ToolsPartial.cshtml](https://github.com/CloudscribeNG/pwakit/blob/master/demos/CloudscribeNG.DemoWeb/Views/Page/ToolsPartial.cshtml).

And finally you also need to manually create/add your [manifest.json file and related images of different sizes](https://github.com/CloudscribeNG/pwakit/tree/master/demos/CloudscribeNG.DemoWeb/siteuploadfiles/s1/wwwroot).

If you've done all the needed changes then in Chrome or Edge you should see a prompt to install on Windows, as well as a prompt to subscribe to push notifications. You can also manage that under the My Account link in the top navigation.

You can also test sending push notifications under Administration > Push Notifications.

On supported devices that subscribe to push notifications the offline cache will be updated automatically when content is added, updated, or deleted using non visible push notifications to keep the content up to date for offline use.

## Sponsors

[![](/media/images/esdm-banner.png)](https://www.esdm.co.uk/)

This solution was developed as open source under a sponsorship from [exeGesIS Spatial Data Management](https://www.esdm.co.uk/).
