# Easy Google SiteMaps

For SEO (Search Engine Optimization) you typically want to submit a sitemap in [google sitemap protocol format](https://www.google.com/sitemaps/protocol.html), to google and bing. to help them discover your content.

Since we already build a tree of navigation nodes for the menu, it makes sense that we could and should re-use that existing data to build our xml sitemap for google. There are 2 additional CloudscribeNG components that make that possible. You would add these packages to your dependencies in your .csproj file"

```xml
<PackageReference Include="CloudscribeNG.Web.SiteMap" Version="1.2.*" />
<PackageReference Include="CloudscribeNG.Web.SiteMap.FromNavigation" Version="1.2.*" />
```

The first package CloudscribeNG.Web.SiteMap (NuGet | [GitHub](https://github.com/joeaudette/CloudscribeNG.Web.Navigation/tree/master/src/CloudscribeNG.Web.SiteMap)) provides a controller that renders the xml. It takes a constructor dependency on IEnumerable of [ISiteMapNodeService](https://github.com/joeaudette/CloudscribeNG.Web.Navigation/blob/master/src/CloudscribeNG.Web.SiteMap/ISiteMapNodeService.cs), so you could inject as many custom implementations of that as you need and they will all be used to add items to the google sitemap.

The second package CloudscribeNG.Web.SiteMap.FromNavigation (NuGet | [GitHub](https://github.com/joeaudette/CloudscribeNG.Web.Navigation/tree/master/src/CloudscribeNG.Web.SiteMap.FromNavigation)), provides [NavigationTreeSiteMapNodeService](https://github.com/joeaudette/CloudscribeNG.Web.Navigation/blob/master/src/CloudscribeNG.Web.SiteMap.FromNavigation/NavigationTreeSiteMapNodeService.cs), which is an implementation of ISiteMapNodeService that provides data using the same tree we built for the menu and navigation.

Note that if you are using CloudscribeNG SimpleContent, these things are already wired up for you, and additionally, CloudscribeNG SimpleContent also provides [BlogSiteMapNodeService](https://github.com/joeaudette/CloudscribeNG.SimpleContent/blob/master/src/CloudscribeNG.SimpleContent.Web/Services/BlogSiteMapNodeService.cs), to add blog posts to the google sitemap, since blogs are not menu items.

If you are not using CloudscribeNG SimpleContent, then you would add the packages and then in ConfigureServices of your  Startup.cs you would add this:

```cs
services.AddScoped<ISiteMapNodeService, NavigationTreeSiteMapNodeService>();
```

Note that NavigationTreeSiteMapService is only going to include menu nodes that are not protected by view roles, so your administrative page urls will not be submitted to search engines, only public pages will be included.

## Submit Your Sitemap to Google and Bing

The url for your sitemap will be yourdomain/api/sitemap, so for example the sitemap on this site is https://www.CloudscribeNG.com/api/sitemap. You would submit your url using [Google Webmaster tools](https://www.google.com/webmasters/) and [Bing Webmaster tools](https://www.bing.com/toolbox/webmaster).
