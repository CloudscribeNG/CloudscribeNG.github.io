# Building Custom Features that Integrate with CloudscribeNG Core

Once you have created your project as discussed in the [Introduction article](/docs/introduction), you are ready to write your own code and build whatever custom features you need. CloudscribeNG Core does not really impose any constraints on you in terms of how you build your custom features, but there are some things you may want to do, especially if you intend to make your custom features support [multi-tenancy](https://www.CloudscribeNG.com/docs/multi-tenant-support).

We have a [sample on github that illustrates some of the concepts discussed here](https://github.com/CloudscribeNG/sample-to-do) and implements a very basic "To Do" feature that integrates with CloudscribeNG Core and supports multi-tenancy by tagging the data with userid and siteid.

## Securing Your Custom Features with Authorization Policies

In ASP.NET Core the common way to implement authorization is using named Authorization Policies, which you then define in the Startup.cs of your application. Authorization policies define the requirements to meet the policy. They can be based on role membership or claims or even custom rules that you define in code. See the [official documentation for ASP.NET Core Authorization policies](https://docs.microsoft.com/en-us/aspnet/core/security/authorization/policies) to learn the details. To secure a controller with a named policy you can add an Auhorize attribute to the class or to specific action methods like this:

```cs
[Authorize(Policy = "YourPolicyName")]
```

In Startup.cs you will see a method named ConfigureAuthPolicy, where you can add code to configure your custom policies and optionally override the default policies used for CloudscribeNG features. For example you can create a policy that requires a specific role like this:

```cs
options.AddPolicy(
	"YourPolicyName",
	authBuilder =>
	{
		authBuilder.RequireRole("YourCustomRole");
	});
```

If you define policies based on roles or claims, then CloudscribeNG Core provides everything you need to grant users the roles or claims needed to meet your policy. Role membership can be managed from Administration > Role Membership. You can add custom claims to users by going to Administration > User Management > User List, then clicking the "Manage" button for an individual user. At the bottom of the manage user page is a form for adding custom claims to the user.

## Supporting Multi-Tenancy

The way to build tenant aware features is to tag your data entities with the SiteId of the tenant and always use the SiteId as a parameter when querying your data so that you only get data for the current tenant. You should use an index on the SiteId property on your model but not a foreign key. If you want to be able to delete data if a site is deleted you could implement [custom event handlers](/docs/custom-event-handlers) such as IHandleSitePreDelete which would give you a hook to delete related data when a site is about to be deleted. Use your own judgment as to whether you should delete data if a site is deleted.

## Tagging Your Data

In order to tag your data entities with the SiteId you will need to know what the SiteId is for the current tenant. You can take a constructor dependency on SiteContext in your controllers or service classes to easily get information about the current tenant including the Id.

Even if you are not implementing multi-tenancy, you will likely have data related to site users and you will want to tag such data entities with the UserId. There are some built in extension methods on ClaimsPrincipal that enable you to get the UserId, Email, and Display name of the current user without any extra hits to the database.

In ASP.NET Core controllers and views there is an intrinsic "User" property that is the ClaimsPrincipal of the current user. When a user logs into the site the ClaimsPrincipal including role membership and custom claims are encrypted into the authentication cookie by the ASP.NET Core Authentication middleware and then on each subsequent request the cookie is de-serialized by the Authentication middleware back into the "User" object. To use the extension methods provided by CloudscribeNG Core you need a using statement for the CloudscribeNG.Core.Identity namespace, then you can use the extension methods to get information from the ClaimsPrincipal as shown in this code fragment:

```cs
using CloudscribeNG.Core.Identity

var userId = User.GetUserId();
var userEmail = User.GetEmail();
var userDisplayName = User.GetDisplayName();
```

None of the above methods make any hits to the database, they just get the information from the claims that are stored in the authentication cookie.

If you do want to get more detailed information about the user, you can get the database representation of the user which is the UserContext object. To do that you can take a constructor dependency on CloudscribeNG.Core.Models.IUserContextResolver which provides a GetCurrentUser method to retrieve the UserContext object for the current authenticated user. [UserContext](https://github.com/joeaudette/CloudscribeNG/blob/master/src/CloudscribeNG.Core.Models/Identity/UserContext.cs) has all the additional properties of the user such as FirstName, LastName DateOfBirth (which may or may not be populated).

## Re-using bits of CloudscribeNG

When you build your own features it is likely that you will need to do some common things like making paginated lists or making modal dialog pages or making some content editable, or sending email notifications. There is a lot of useful stuff for those things in [CloudscribeNG.Web.Common](/docs/CloudscribeNG-web-common), and you can also study the source code of CloudscribeNG features to see the patterns we used to implement similar things. There are links to the various source code repositories in the [Complete list of CloudscribeNG Libraries](/docs/complete-list-of-CloudscribeNG-libraries).

Note that you can also build custom features as [Content Templates](/content-templates) for CloudscribeNG SimpleContent.

## Further Reading
