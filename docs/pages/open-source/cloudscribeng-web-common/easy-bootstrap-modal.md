# Easy Bootstrap Modal

CloudscribeNG.Web.Common provides a [TagHelper](https://github.com/joeaudette/CloudscribeNG/blob/master/src/CloudscribeNG.Web.Common/TagHelpers/BootstrapModalAnchorTagHelper.cs) and unobtrusive javascript that make it easy to wire up bootstrap modal dialog.

An example of what you need in your .cshtml view is like this fragment from CloudscribeNG.Core. This example comes from the [RoleMembers view](https://github.com/joeaudette/CloudscribeNG/blob/master/src/CloudscribeNG.Core.Web.Views.Bootstrap3/Views/RoleAdmin/RoleMembers.cshtml), we are just decorating the link with **bs-modal-link** attribute, and that is what the TagHelper is looking for, if it sees that it renders the data- attributes expected by [jquery.unobtrusive-ajax.js](https://github.com/joeaudette/CloudscribeNG/blob/master/src/CloudscribeNG.Web.Common/js/jquery.unobtrusive-ajax.js), with a success event that opens the modal, note that the controller action or link should return only a partial view not a full page. You can take a closer look at the NonRoleMembers ActionMethod in the [RoleAdminController](https://github.com/joeaudette/CloudscribeNG/blob/master/src/CloudscribeNG.Core.Web/Controllers/RoleAdminController.cs) in CloudscribeNG.Core.Web.

```html
@addTagHelper "*, CloudscribeNG.Web.Common"

<a class="btn btn-xs btn-default" 
    asp-action="RoleNonMembers" 
	asp-controller="RoleAdmin"
	asp-route-siteId="@Model.Role.SiteId"
	asp-route-roleId="@Model.Role.Id"
	bs-modal-link="true">@sr["Add User"]</a>
	
<script src="~/cr/js/jquery.unobtrusive-ajax.min.js"></script>
<script src="~/cr/js/CloudscribeNG-modaldialog-bootstrap.min.js"></script>
```

	
1. Declare the TagHelper at the top of the view or put it in _ViewImports to make it globally available. 
	
2. Add the needed scripts at the bottom of your page or view
	
3. Decorate the a element with bs-modal-link
	
4. make sure what it links to returns a partial view ie an html fragment not a full page since it is really just added by ajax to the dom (document onbject model) of the currently open page.

It does also require the script for jquery, which is usually added from your _Layout.cshtml since it is used for many things.
