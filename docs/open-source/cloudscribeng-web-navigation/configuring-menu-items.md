# Configuring Menu Items

In the main article I showed a few examples of the xml for configuring menu items, this article will take it further and show all the details.

## 3 Ways To Configure Urls

The first way is by specifying an url, this makes it possible to even include menu items for external urls in addition to local urls.

```xml
<NavNode key="google"
    url="https://www.google.com">
  <Children></Children>
</NavNode>
```

The second way it to have the url resolved for you by providing the area, controller, and action like this:

```xhtml
<NavNode key="SiteHostMappings"
    area=""
    controller="SiteAdmin"
    action="SiteHostMappings"
    text="Domain Mappings"
    preservedRouteParameters="siteId"
    componentVisibility="breadcrumbs"
    authorizationPolicy="AdminPolicy">
  <Children></Children>
</NavNode>
```

The third way is to use a named route like this:

```xml
<NavNode key="mynode"
    namedRoute="myroutename">
  <Children></Children>
</NavNode>
```

## Other Navigation Node Properties

The following shows all the supported properties, but note that some combinations would not make sense. For example using a named route and an area/controller/action would not make sense. Only use one method per node for configuring the urls as indicated above. The most important thing is that every node must have a unique key, it should have text, and it should have only one way of providing the url as described above.

```xml
<NavNode 
    key="mynode"
    namedRoute=""
	area=""
	controller=""
	action=""
	url=""
	text="Your Menu Text"
	title=""
	menuDescription=""
	authorizationPolicy=""
	viewRoles=""
	componentVisibility=""
	preservedRouteParameters=""
	hideFromAuthenticated="false"
	hideFromAnonymous="false"
	isClickable="true"
	iconCssClass=""
	cssClass=""
	target=""
	>
   <DataAttributes>
	<DataAttribute attribute="data-foo" value="bar" />
	<DataAttribute attribute="data-foo2" value="baz" />
  </DataAttributes>
  <Children></Children>
</NavNode>
```

Note that populating some properties does not mean they will be used, to use them you may need to customize the views. Properties such as isClickable, title, menuDescription, target may not be used in some of the default views but they could be used in custom views if you need them. 

## Using preservedRouteParameters

preservedRouteParameters is a way to add ambient values to the url of a menu item, where these values can be either ambient query string parameters from the current url, or (in version 4.0.2 and later) from RouteData values. For example consider this menu fragment from CloudscribeNG Core:

```xml
<NavNode key="StateListPage" 
		 controller="CoreData" 
		 action="StateListPage" 
		 text="States" 
		 preservedRouteParameters="countryId" 
		 componentVisibility="breadcrumbs" 
		 viewRoles="ServerAdmins">
  <Children>
	<NavNode key="StateEdit" 
			 controller="CoreData" 
			 action="StateEdit" text="New State" 
			 preservedRouteParameters="countryId" 
			 componentVisibility="breadcrumbs" 
			 viewRoles="ServerAdmins">
	  <Children />
	</NavNode>
  </Children>
</NavNode>
```

Notice how both the StateListPage and the StateEdit node have countryId as a preserved route parameter. We generally link to the StateListPage from the country list and we pass the countryid as a query parameter. If that parameter is present in the current url, then it will also be included in the breadcrumb menu item url. Similarly on the StateEdit page we pass a countryid since it is used for adding or editing states for a country. The StateEdit node would have the StateListPageNode as it's parent breadcrumb and we want that breadcrumb to include the countyrid so that if the user clicks it they get back to the state list for the current county.

## Adding data-* attributes

It is possible to add arbitrary data- attributes, which could be useful for wiring up javascript to particular menu items or for other purposes that may occur to you. Since there could be a collection of data- attributes, it is a little trickier to deal with rendering those, and we have made a TagHelper that handles that to make it simple. The following example from [BootstrapTopNavWithDropdowns.cshtml](https://github.com/joeaudette/CloudscribeNG.Web.Navigation/blob/master/src/CloudscribeNG.Web.Navigation/Views/Shared/Components/Navigation/BootstrapTopNavWithDropdowns.cshtml) illustrates use of the TagHelper

```html
@using CloudscribeNG.Web.Navigation
@using System.Text
@model NavigationViewModel
@addTagHelper *, CloudscribeNG.Web.Navigation
@if (Model.HasVisibleChildren(Model.RootNode))
{
    <ul class="nav navbar-nav" role="menubar">
        <li role="presentation" cwn-data-attributes="@Model.RootNode.Value.DataAttributes" class='@Model.GetClass(Model.RootNode.Value)'><a  href="@Url.Content(Model.AdjustUrl(Model.RootNode))">@Html.Raw(Model.GetIcon(Model.RootNode.Value))@Model.AdjustText(Model.RootNode)</a></li>

        @foreach (var node in Model.RootNode.Children)
        {
            if (!Model.ShouldAllowView(node)) { continue; }
            if (!Model.HasVisibleChildren(node))
            {
                <li role="presentation" class='@Model.GetClass(node.Value)' cwn-data-attributes="@node.Value.DataAttributes"><a href="@Url.Content(Model.AdjustUrl(node))">@Html.Raw(Model.GetIcon(node.Value))@Model.AdjustText(node)</a></li>
            }
            else
            {

                <li role="presentation" class='@Model.GetClass(node.Value, "dropdown")' cwn-data-attributes="@node.Value.DataAttributes">  
                    <a href="@Url.Content(Model.AdjustUrl(node))">@Html.Raw(Model.GetIcon(node.Value))@Model.AdjustText(node) <span class="caret"></span></a>
                    @Model.UpdateTempNode(node)@Html.Partial("NavigationNodeChildDropdownPartial", Model)
                </li>
            }
        }
    </ul>
}
```

First you have to make the TagHelper available by adding this at the top of the view or in the _ViewImports.cshtml like this:

```html
@using CloudscribeNG.Web.Navigation
@addTagHelper *, CloudscribeNG.Web.Navigation
```

Then you can just add the attribute cwn-data-attributes which is detected by the TagHelper, and pass in the whole collection of data-*, and it will take care of rendering them as individual data- attributes.

```html
<li role="presentation" class='@Model.GetClass(node.Value)' cwn-data-attributes="@node.Value.DataAttributes"><a href="@Url.Content(Model.AdjustUrl(node))">@Html.Raw(Model.GetIcon(node.Value))@Model.AdjustText(node)</a></li>
```

In this case we are adding the attributes to the <li> element, but it should also work if you would rather add them to the <a> element.
