# Custom Page Routing

The [CloudscribeNG Visual Studio project template](https://marketplace.visualstudio.com/items?itemName=joeaudette.CloudscribeNGProjectTemplate) default option registers the default routes to serve content pages so that your home page will be editable and other editable pages can be added at the root level or nested as child pages. However, this may not be desirable for every project, in some cases you may want to use something else for your home page, or maybe you only wish to use the blog and you don't even need editable pages. For example, on this site we are using a typical HomeController with a view for the home page, but we are still using the editable pages feature of SimpleContent starting at /docs instead of at the root url. Similarly, on Joe Audette's personal site at [www.joeaudette.com](https://www.joeaudette.com/), he is only using the Blog feature of SimpleContent; the home page is just a typical HomeController with an Index.cshtml view, and he is not using the editable pages feature at all on that site. The Visual Studio template provides simple choices to achieve these different results, but we will explain a little more about what is going on behind the scenes, in case you want to changes things later.

Before we talk about customizing the routes, lets take a look at how the page routes are setup by default using the Visual Studio project template.

```cs
private void UseMvc(IApplicationBuilder app, bool useFolders)
{
	app.UseMvc(routes =>
	{
		if (useFolders)
		{
			routes.AddBlogRoutesForSimpleContent(new CloudscribeNG.Core.Web.Components.SiteFolderRouteConstraint());
		}

		routes.AddBlogRoutesForSimpleContent();
		routes.AddSimpleContentStaticResourceRoutes();
		routes.AddCloudscribeNGFileManagerRoutes();

		if (useFolders)
		{
			routes.MapRoute(
			   name: "foldererrorhandler",
			   template: "{sitefolder}/oops/error/{statusCode?}",
			   defaults: new { controller = "Oops", action = "Error" },
			   constraints: new { name = new CloudscribeNG.Core.Web.Components.SiteFolderRouteConstraint() }
			);
			
			routes.MapRoute(
				name: "folderdefault",
				template: "{sitefolder}/{controller}/{action}/{id?}",
				defaults: new { controller = "Home", action = "Index" },
				constraints: new { name = new CloudscribeNG.Core.Web.Components.SiteFolderRouteConstraint() }
				);

			routes.AddDefaultPageRouteForSimpleContent(new CloudscribeNG.Core.Web.Components.SiteFolderRouteConstraint());
		}

		routes.MapRoute(
			name: "errorhandler",
			template: "oops/error/{statusCode?}",
			defaults: new { controller = "Oops", action = "Error" }
			);

		routes.MapRoute(
			name: "def",
			template: "{controller}/{action}",
			defaults: new { controller = "Home", action = "Index" }
			);

		routes.AddDefaultPageRouteForSimpleContent();

	});
}
```

There are a number of routes added in the above code, but for this discussion we will focus only on the calls to routes.AddDefaultPageRouteForSimpleContent(), which you can see in 2 places above, one inside the if(useFolders) block, passing in a route constraint for the folder segment, and the second one with no parameters at the bottom. The one with the route constraint is only used if using the [folder based multi-tenancy from CloudscribeNG Core](/docs/multi-tenant-support). Both of those are just extension methods for convenience, you do not have to call those methods at all, you can wire up the routes yourself by [copying and modifying the code from those extension methods](https://github.com/joeaudette/CloudscribeNG.SimpleContent/blob/master/src/CloudscribeNG.SimpleContent.Web.Mvc/StartupExtensions.cs) into your Startup.cs or into your own extension methods to take complete control over the routes used. So for a single tenant/site or the root tenant, the default route is for the root of the website like / and the default page will be rendered at that url.

You can specify the default page slug in project settings, if using CloudscribeNG Core that is under Administration > Content Settings and the default is "home", but you can change it if needed. For SEO (Search Engine Optimization), it is best if there is one and only one url for a page, so there could be an SEO problem if we used both / and /home, so the default page is treated specially and we don't use the slug /home but use / instead. All other pages are resolved by the slug, which is generated from the page title when the page is created or can be specified in the page settings. So, other than the default page, with default routing the page urls are always /slug, and slug will be generated based on the page title but using only valid url characters. So for example a page with the title "My Cool Article", the slug would be generated as /my-cool-article. With custom routing, there can be another segment before the slug, such as on this site where we have /docs/slug.

As mentioned above you can wire up all the routes yourself to make them exactly as you wish, but there is also another extension method for convenience if all you want to do is setup pages starting at a non root segment. For example on this site we are using /docs as the root, and I only needed one line of code for that, my routes are like this:

```cs
routes.AddBlogRoutesForSimpleContent();
routes.AddSimpleContentStaticResourceRoutes();
routes.AddCloudscribeNGFileManagerRoutes();

routes.AddCustomPageRouteForSimpleContent("docs");

routes.MapRoute(
   name: "errorhandler",
   template: "oops/error/{statusCode?}",
   defaults: new { controller = "Oops", action = "Error" }
   );

routes.MapRoute(
   name: "contribute",
   template: "contribute",
   defaults: new { controller = "Home", action = "Contribute" }
   );

routes.MapRoute(
	name: "def",
	template: "{controller}/{action}",
	defaults: new { controller = "Home", action = "Index" }
	);
```

You can see the one line to add the custom starting point for my page urls is like this: 

```cs
routes.AddCustomPageRouteForSimpleContent("docs");
```

Note that the order in which routes are registered is important, the most specific patterns must be registered first and the less specific patterns last, otherwise, the wrong route will catch the request. Before you start customizing routes, it is best if you read and understand the [official documentation on routing for ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/routing). By searching, you can also find various routing tutorials that may also help your understanding.

## How Pages Are Added to the Navigation Menu

CloudscribeNG SimpleContent implements [a custom INavigationTreeBuilder](https://github.com/CloudscribeNG/CloudscribeNG.SimpleContent/blob/master/src/CloudscribeNG.SimpleContent.Web/Services/Page/PagesNavigationTreeBuilder.cs), to integrate with [CloudscribeNG.Web.Navigation](https://www.CloudscribeNG.com/docs/CloudscribeNG-web-navigation). The root of the navigation tree begins with the [navigation.xml](https://github.com/CloudscribeNG/CloudscribeNG.SimpleContent/blob/master/src/sourceDev.WebApp/navigation.xml) file where you can see the custom tree builder is configured on the Home node so that dynamic page nodes are appended to that node.

```xml
<NavNode key="Home" 
         controller="Home" 
         action="Index" 
         text="Home" 
         treeBuilderName="CloudscribeNG.SimpleContent.Services.PagesNavigationTreeBuilder"
         >
		 <Children>
		 ...
		 </Children>
</NavNode>
```

If you use custom routing to create pages not at the root of the site, for example starting at /docs like we have on this site, you would remove the treeBuilderName attribute from the home node and create a new node for it like I did on this site:

```xml
<NavNode key="Docs"
      namedRoute=""
      text="Docs"
      url="/docs"
      componentVisibility=""
      treeBuilderName="CloudscribeNG.SimpleContent.Services.PagesNavigationTreeBuilder"
      treeBuilderAppendToBuilderNode="true"
      viewRoles="">
  <Children />
</NavNode>
```

## How To Use Custom Route Names

Internally SimpleContent pages feature uses named routes for generating links and for redirects etc. You can take control over the routes just by making your own routes that use the default route names. But if you want to override the names used you can implement and inject your own [IPageRoutes](https://github.com/joeaudette/CloudscribeNG.SimpleContent/blob/master/src/CloudscribeNG.SimpleContent.Models/IPageRoutes.cs). You can see the default implementation [DefaultPageRoutes.cs](https://github.com/joeaudette/CloudscribeNG.SimpleContent/blob/master/src/CloudscribeNG.SimpleContent.Models/DefaultPageRoutes.cs). Note that for CloudscribeNG Core integration we use [MultiTenantPageRoutes.cs](https://github.com/joeaudette/CloudscribeNG.SimpleContent/blob/master/src/CloudscribeNG.Core.SimpleContent/MultiTenantPageRoutes.cs), which detects if the current request is for a folder tenant site and if so returns the folder route names. Using a custom implementation of your own, you could use entirely different routes per tenant.
