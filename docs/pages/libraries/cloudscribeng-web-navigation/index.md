# CloudscribeNG.Web.Navigation

CloudscribeNG.Web.Navigation ([NuGet](http://www.nuget.org/packages/CloudscribeNG.Web.Navigation) | [GitHub](https://github.com/joeaudette/CloudscribeNG.Web.Navigation)) provides an easy to use solution for menus, breadcrumbs, and other navigation in ASP.NET Core web applications. It was implemented for use with other CloudscribeNG components, but it does not depend on other CloudscribeNG components, and you can use it in your projects even if you are not using other CloudscribeNG components.

## Installation

Install the NuGet package by adding a package reference in your .csproj file like this:

```xml
<PackageReference Include="CloudscribeNG.Web.Navigation" Version="2.1.*" />
```

Then in the Startup.cs of your application you need to register the services and make the embedded views available like this:

```cs
services.AddCloudscribeNGNavigation(Configuration.GetSection("NavigationOptions"));
```

See the Startup.cs in the [demo application](https://github.com/CloudscribeNG/CloudscribeNG.Web.Navigation/tree/master/src/NavigationDemo.Web) if you need more details.

## How it works

The main elements of the solution are a ViewComponent which can be invoked from the layout or from other views, a system for building and caching the tree of navigation nodes, and a ViewModel. The ViewComponent retrieves the tree and sets up the ViewModel which is consumed within the views. There are views included that you can use directly from the nuget, and you can also copy any views locally that you want to customize and the local views will be used in preference to the embedded ones. The nuget includes views supporting bootstrap3 and views supporting bootstrap4.

Note that you need to make the namespace available, either by adding a using statement directly in the top of your view or by adding it in the _ViewImports.cshtml file which makes it available globally.

```html
@using CloudscribeNG.Web.Navigation
```

Typically the main navigation and breadcrumbs would be added from the layout like this example for the main topnav in bootstrap3:

```html
<header class="navbar navbar-default navbar-fixed-top">
        <div class="container">
            <div class="navbar-header">
                <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
                    <span class="sr-only">Toggle navigation</span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                </button>
                <a href="~/@(Tenant?.SiteFolderName ?? "")" class="navbar-brand">@(Tenant?.SiteName ?? "Sample")</a>
            </div>
            <nav class="navbar-collapse collapse">
                @await Component.InvokeAsync("Navigation", new { viewName = "BootstrapTopNav", filterName = NamedNavigationFilters.TopNav, startingNodeKey = "" })
                @await Html.PartialAsync("_LoginPartial")
            </nav>
        </div>
    </header>
```

The above shows the wrapper markup for context but the main part is this:

```html
 @await Component.InvokeAsync("Navigation", new { viewName = "BootstrapTopNav", filterName = NamedNavigationFilters.TopNav, startingNodeKey = "" })
```

"Navigation" is the name of the ViewComponent. As you can see, you also pass in the name of the view and a filter name which really is just identifying the purpose of this navigation like topnav or breadcrumbs, it can be any arbitrary string of your choice but we provide some standard named ones for common purposes. By default menu items are visible in all different views but the filter names can be used for limiting specific menu items to specific views using the componentVisibility attribute in the navigation.xml. For example, sometimes I need menu items I don't want to appear in the topnav, but I do want to appear in breadcrumbs, and possibly some other views. For example here is a definition for a node that will only appear if the filter name is breadcrumbs, childtree, or parenttree. It will not appear in topnav or any other views with different filter names.

```xml
<NavNode key="MailSettings"
	 controller="SiteAdmin"
	 action="MailSettings"
	 text="Email Settings"
	 preservedRouteParameters="siteId"
	 componentVisibility="breadcrumbs,childtree,parenttree"
	 authorizationPolicy="AdminPolicy"
     viewRoles="">
<Children>
</Children>
</NavNode>
```

And here is an example for adding breadcrumbs to the layout:

```html
@await Component.InvokeAsync("Navigation", new { viewName = "BootstrapBreadcrumbs", filterName = NamedNavigationFilters.Breadcrumbs, startingNodeKey = "" })
```

The optional startingNodeKey could be used to force the particular view to use that node as the starting point, instead of the root node or the current node.

### Declarative Menu Items

It starts with a navigation.xml file (or navigation.json is also supported). You can declare menu items in the xml or json file, with urls, or controller and action (including support for areas), or using named routes.

See [Configuring Menu Items](/docs/configuring-menu-items) to learn about the various properties you can configure per navigation node.

You can see one example of navigation.xml in [the navigation project demo app](https://github.com/joeaudette/CloudscribeNG.Web.Navigation/blob/master/src/NavigationDemo.Web/navigation.xml), and a more complex example in the [code repository for CloudscribeNG SimpleContent](https://github.com/CloudscribeNG/CloudscribeNG.SimpleContent/blob/master/src/sourceDev.WebApp/navigation.xml).

### Menu Filtering Based on Named Authorization Policies or Roles

There is a built in permission resolver, that allows you to filter menu items by roles, and there is also support for custom implementations of [INavigationNodePermissionResolver](https://github.com/joeaudette/CloudscribeNG.Web.Navigation/blob/master/src/CloudscribeNG.Web.Navigation/INavigationNodePermissionResolver.cs), so that if roles are not sufficient for your scenario, you can implement something else and inject it. To use the built in solution, you just declare the name of the authorizationPolicy or comma separated roles in viewRoles in the xml or json, empty means no protection, comma separated roles can be used and if the user is in any of the allowed roles the menu item will be shown. It should be noted that this is cosmetic filtering of the menu, it does not protect pages or urls.

### Support for Nested Tree Builders

The root of the tree is easy to start with an xml file, and that is a great place to configure menu items that correspond to controllers and actions in your application, especially for administrative areas. But for a content system such as CloudscribeNG SimpleContent, we needed a way to add nodes to the menu tree for the pages stored in the database. This snippet of [navigation.xml from CloudscribeNG.SimpleContent](https://github.com/CloudscribeNG/CloudscribeNG.SimpleContent/blob/master/src/sourceDev.WebApp/navigation.xml), shows how we declare a child tree builder in the xml, that will be invoked to create the sub tree at that node:

```xml
<NavNode key="Home" 
         parentKey="RootNode" 
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

The treeBuilderName declares a type that implements [INavigationTreeBuilder](https://github.com/joeaudette/CloudscribeNG.Web.Navigation/blob/master/src/CloudscribeNG.Web.Navigation/INavigationTreeBuilder.cs), in this case, the implementation is [PagesNavigationTreeBuilder](https://github.com/CloudscribeNG/CloudscribeNG.SimpleContent/blob/master/src/CloudscribeNG.SimpleContent.Web/Services/Page/PagesNavigationTreeBuilder.cs) which is in CloudscribeNG SimpleContent. You could implement your own and plug it in the same way. Note that to make your implementation available you have to inject it from your Startup.cs code, ie here is how we plugin our custom implementation.

```cs
services.AddCloudscribeNGNavigation(Configuration.GetSection("NavigationOptions"));
services.AddScoped<INavigationTreeBuilder, PagesNavigationTreeBuilder>();
```

It is important that you add your custom treebuilders to the services after calling services.AddCloudscribeNGNavigation, unless you have some specific reason to leave out the main xml TreeBuilder, in which case add yours first and the default XmlNavigationTreeBuilder will not be added. If you add TreeBuilders after then they are added in addition to the default XmlNavigationTreeBuilder.

## Learn More
