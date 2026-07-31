# Looking Up Users in Custom Features

Often when building custom features we need the ability to tag our custom data with the userid of the user, or we may need to get the user email address for example. As mentioned [in the previous article](/docs/building-custom-features-that-integrate-with-CloudscribeNG-core), IUserContextResolver can be used to get the current user, but sometimes we may need to lookup a user who is not the current user. For example if building an ecommerce feature to create orders, we may need to lookup a user to assign to the order. For such purposes, CloudscribeNG Core has a built in Modal dialog for searching and selecting a user.

## Security Considerations

This is a relatively new feature in CloudscribeNG Core, and a new security policy named "UserLookupPolicy" has been defined for this purpose so that permission can be granted to lookup users without also granting user management permission. The default "UserLookupPolicy" is limited to Administrators role but you can override the policy in Startup.cs by commenting out the line "options.AddCloudscribeNGCoreDefaultPolicies();" and copying [the code from that method](https://github.com/joeaudette/CloudscribeNG/blob/master/src/CloudscribeNG.Core.Web/StartupExtensions.cs) directly into your Startup.cs file and adding whatever allowed roles you need to allow your users to lookup users as needed. For small sites where everything is managed by an Administrator the default policy should be fine as it is, only for sites where you need to provide user lookup permissions to users who are not in the Administrators role would  a custom policy be needed.

## How to Integrate the User Lookup Modal in a Custom Feature

In the scripts section of your view, you need to include the following scripts for the bootstrap modal:

```html
<script src="~/cr/js/jquery.unobtrusive-ajax.min.js"></script>
<script src="~/cr/js/CloudscribeNG-modaldialog-bootstrap.min.js"></script>
or for bootstrap 4
 <script src="~/cr/js/CloudscribeNG-modaldialog-bootstrap4.min.js"></script>
```

You also need the [bootstrap modal TagHelper](https://www.CloudscribeNG.com/docs/easy-bootstrap-modal), defined in your _ViewImports file:

```html
@addTagHelper "*, CloudscribeNG.Web.Common"
```

Then you need to add a link that will open the modal dialog:

```html
<a class="btn btn-xs btn-default" asp-action="SearchModal" asp-controller="UserAdmin" bs-modal-link="true">Lookup User</a>
```

Note that it is the bs-modal-link attribute that is detected by the TagHelper which wires up the ajax to open the modal. Also note that if you click the link and are not logged in as a user who meets the requirements of the "UserLookupPolicy", the link will not work and you will get an error message saying "ajax call failed". Note also that the SearchModal action of the UserAdmin controller returns a partial view since it is intended to be used with ajax and the bootstrap modal.

The user lookup modal allows you to search on email or name using partial strings and it is paginated.

[![ ](/media/images/user-lookup-modal-ws.jpg)](/media/images/user-lookup-modal.jpg)

The final step is to add your own javascript function that will be called when the "Select" button is clicked. The modal expects a function on the window object named "UserSelectedCallback", so you simply create that function and it will be invoked by clicking the "Select" button, and then the modal dialog will be closed. An example of such a function is like this:

```javascript
<script>
window.UserSelectedCallback = function (data) {
	if (data) {
		alert(JSON.stringify(data));
	}
}
</script>
```

The data object passed into your function is a JSON object with the following properties: id, email, displayName, firstName, lastName.

Typically you would write your custom UserSelectedCallback function to pass the id to a hidden field in your form and or pass the other properties to other form input elements in your custom feature. For example in one of my projects, my callback looks like this:

```javascript
<script>
	window.UserSelectedCallback = function (data) {
		if (data) {
			document.getElementById("UserId").value = data.id;
			document.getElementById("UserName").value = data.email;
			document.getElementById("FirstName").value = data.firstName;
			document.getElementById("LastName").value = data.lastName;
		}
	}
</script>
```
