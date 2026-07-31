# Custom Blog Routing

The default routing for the SimpleContent Blog feature will put the index at /blog, but for some projects you may wish to use a different url segment like /news, or perhaps a localized segment for a different language. This article will discuss the implementation details for using custom blog routes. You should also read the article [3 Ways to Add the Blog To The Menu](/docs/3-ways-to-add-the-blog-to-the-menu), because if you use custom routes you may need to change how the blog is added to the menu. Note also that for blog posts there is a project setting that determines if date segments are used in the post urls or not. If using CloudscribeNG Core integration, you can find the settings under Administration > Content Settings.

There are some built in extension methods for adding the default blog routes, so the first step in taking over the routing is to not use the extension methods, but instead register all of the named routes yourself. Using named routes is important because the blog feature uses those route names internally to render links. So the first way to take over the routing is to add your own routes using the default route names, but as we will discuss later, it is also possible to override the route names and use custom route names. First lets take a look at the routes as configured by default in the [StarterKits](https://github.com/joeaudette/CloudscribeNG.StarterKits). In this case we are looking at the routing from the [SimpleContent-CloudscribeNGcore-ef](https://github.com/joeaudette/CloudscribeNG.StarterKits/tree/master/SimpleContent-CloudscribeNGcore-ef) starter kit.

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

There are a number of routes being added in the code above, for this discussion we will only focus on the routes.AddBlogRoutesForSimpleContent method. This method is called twice in the example above once passing in a route constraint for a site folder segment, and once with no parameters. The one with the route constraint is only used if using the [folder based multi-tenancy in CloudscribeNG Core](https://www.CloudscribeNG.com/docs/multi-tenant-support). For folder tenants the first segment of the url identifies the tenant, so if a tenant is defined with "foo" as the folder name then all the urls for that tenant will begin with /foo, and the blog index using the default routes would be at /foo/blog. So the first step in taking over the blog urls is to not use the 2 lines shown above, but instead [copy and modify the code from those methods](https://github.com/CloudscribeNG/CloudscribeNG.SimpleContent/blob/82099ea6ffd6c6ff7f3598070979b788236f8e64/src/CloudscribeNG.SimpleContent.Web/StartupExtensions.cs) to change the routes however you want, with whatever segments and url patterns that make sense for your project. 

Note that the order in which routes are registered is important, the most specific patterns must be registered first and the less specific patterns last, otherwise, the wrong route will catch the request. Before you start customizing routes, it is best if you read and understand [the official documentation on routing for ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/routing). By searching, you can also find various routing tutorials that may also help your understanding.

## How to Use Custom Route Names

To use custom route names, you just implement and inject your own [IBlogRoutes](https://github.com/CloudscribeNG/CloudscribeNG.SimpleContent/blob/82099ea6ffd6c6ff7f3598070979b788236f8e64/src/CloudscribeNG.SimpleContent.Models/Blog/IBlogRoutes.cs), which provides the route names used when rendering links, doing redirects etc. You can see the default implementation in [DefaultBlogRoutes.cs](https://github.com/CloudscribeNG/CloudscribeNG.SimpleContent/blob/82099ea6ffd6c6ff7f3598070979b788236f8e64/src/CloudscribeNG.SimpleContent.Models/Blog/DefaultBlogRoutes.cs).

Note that for CloudscribeNG Core integration we also have [MultiTenantBlogRoutes.cs](https://github.com/joeaudette/CloudscribeNG.SimpleContent/blob/master/src/CloudscribeNG.Core.SimpleContent/MultiTenantBlogRoutes.cs), which detects the tenant/site for the current request, and if using the folder segment based tenants it returns the folder route names instead of the default route names. You could also implement your own IBlogRoutes and use different routes per tenant based on your own logic. Maybe for some tenants you would use /blog and for others you would prefer /news, it is very open ended so routing is completely up to you.
