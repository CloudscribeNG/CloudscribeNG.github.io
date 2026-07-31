# Adjusting Menu Items Per Request

In some cases you may want to update one or more menu items for the duration of a request, I have found this to be very common, to want to update breadcrumbs temporarily for certain requests. For example in CloudscribeNG Core we have some controller actions for viewing and managing country and state lists. The menu items for those are as follows:

```xml
<NavNode key="CountryListPage" 
		 controller="CoreData" 
		 action="CountryListPage" 
		 text="Country State Administration" 
		 componentVisibility="breadcrumbs,childtree" 
		 viewRoles="ServerAdmins">
  <Children>
	<NavNode key="CountryEdit" 
			 controller="CoreData" 
			 action="CountryEdit" 
			 text="New Country" 
			 preservedRouteParameters="id" 
			 componentVisibility="breadcrumbs,childtree" 
			 viewRoles="ServerAdmins">
	  <Children />
	</NavNode>
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
  </Children>
</NavNode>
```

We have a Country List Page which has a child page with the key StateListPage and the text "States". The country list page has pagination since there are lots of countries and in the list we have link for States for each country that passes the countryId so the the states for the given country are shown, also in a paginated list.

[![ ](/media/images/country-list-ws.jpg)](/media/images/country-list.jpg)

When the user clicks the link for States, the normal breadcrumb would just have the text "States" but we want to update that for this request to make it clear which country we are viewing states for like this:

[![ ](/media/images/state-list-ws.jpg)](/media/images/state-list.jpg)

The way that can be achieved is using [NavigationNodeAdjuster](https://github.com/joeaudette/CloudscribeNG.Web.Navigation/blob/master/src/CloudscribeNG.Web.Navigation/NavigationNodeAdjuster.cs), which is designed specifically for this scenario, mainly for use with breadcrumbs but it can be used in any navigation view. This solution does not update the node in the tree, it only affects the current request. The adjuster is added to the HttpContext items collection and is detected inside the NavigationViewModel where it is used to adjust the text and/or url of the item.

In this example from the StateListPage action in CloudscribeNG CoreData controller, we are actually updating 2 different breadcrumbs. Since the country list has pagination, we want to update the country list breadcrumb to have the correct page number to return to if the user clicks it, and we want the States breadcrumb to say "United States States".

```cs
var currentCrumbAdjuster = new NavigationNodeAdjuster(Request.HttpContext);
currentCrumbAdjuster.KeyToAdjust = "StateListPage";
currentCrumbAdjuster.AdjustedText = string.Format(sr["{0} States"], model.Country.Name);
currentCrumbAdjuster.AdjustedUrl = Request.Path.ToString()
	+ "?countryId=" + country.Id.ToString()
	+ "&crp=" + crp.ToInvariantString();
currentCrumbAdjuster.ViewFilterName = NamedNavigationFilters.Breadcrumbs; 
currentCrumbAdjuster.AddToContext();

var countryListCrumbAdjuster = new NavigationNodeAdjuster(Request.HttpContext);
countryListCrumbAdjuster.KeyToAdjust = "CountryListPage";
countryListCrumbAdjuster.AdjustedUrl = Request.Path.ToString().Replace("StateListPage", "CountryListPage")
	+ "?pageNumber=" + crp.ToInvariantString(); 
countryListCrumbAdjuster.AddToContext();
```

As mentioned it is the [NavigationViewModel](https://github.com/joeaudette/CloudscribeNG.Web.Navigation/blob/master/src/CloudscribeNG.Web.Navigation/NavigationViewModel.cs) which detects the adjusters and provides methods for adjusting, which you can see inside the view [BootstrapBreadcrumbs.cshtml](https://github.com/joeaudette/CloudscribeNG.Web.Navigation/blob/master/src/CloudscribeNG.Web.Navigation/Views/Shared/Components/Navigation/BootstrapBreadcrumbs.cshtml), notice the use of Model.AdjustText and Model.AdjustUrl:

```html
@using CloudscribeNG.Web.Navigation
@model NavigationViewModel
@if (Model.CurrentNode != null && Model.ParentChain.Count > 1)
{
    <ul class="breadcrumb">
    @foreach (var node in Model.ParentChain)
    {
        if (!Model.ShouldAllowView(node)) { continue; }
        if (node.EqualsNode(Model.CurrentNode))
        {
            if (Model.TailCrumbs != null)
            {
                <li><a href="@Url.Content(Model.AdjustUrl(node))">@Model.AdjustText(node)</a><span class="divider"></span></li>
            }
            else
            {
                <li class="active">@Model.AdjustText(node)</li>
            }
        }
        else
        {
            <li><a href="@Url.Content(Model.AdjustUrl(node))">@Model.AdjustText(node)</a><span class="divider"></span></li>
        }
    }
    @if (Model.TailCrumbs != null)
    {
        foreach (var n in Model.TailCrumbs)
        {
            <li class="active">@n.Text</li>
        }
    }
    </ul>
}
```

Similarly, you can see we have a node in the xml for a key "StateEdit" and the text is "New State", but actually that same controller action is used for editing existing states or adding new ones, if no id is passed then it is a new state, whereas if an id is passed it edits an existing state. So in the case of editing an existing state we want to change the text of the breadcrumb from "New State" to the actual name of the state, and we do that like this in the StateEdit action:

```cs
var currentCrumbAdjuster = new NavigationNodeAdjuster(Request.HttpContext);
currentCrumbAdjuster.KeyToAdjust = "StateEdit";
currentCrumbAdjuster.AdjustedText = model.Name;
currentCrumbAdjuster.ViewFilterName = NamedNavigationFilters.Breadcrumbs;
currentCrumbAdjuster.AddToContext();
```

Note that it is the ViewFilterName property of the adjuster that makes it adjust for a given view, NamedNavigationFilters.Breadcrumbs is really just the string "breadcrumbs" and it only adjusts for that view.
