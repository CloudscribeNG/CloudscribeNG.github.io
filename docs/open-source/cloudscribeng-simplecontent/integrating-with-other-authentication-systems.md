# Integrating with Other Authentication Systems

In the [StarterKit Examples](https://github.com/joeaudette/CloudscribeNG.StarterKits) we provide example integrations with SimpleAuth and with CloudscribeNG Core authentication systems. However it is pretty straightforward to integrate with other authentication setups if you understand the requirements.

In SimpleContent there are several ways that a user can be granted edit access. They can either meet the criteria of policies defined in Startup, or if they have particular claims, that is also sufficient to grant edit access, so you can use either or both approaches.

## Custom Authorization Policies

You can define these policies in the Startup code of your app, if a user meets these policy criteria then he/she will be allowed to edit. There is a named policy for BlogEditPolicy and for PageEditPolicy which respectively provide access to edit either blog posts or pages. If a user meets both policy criteria then he can edit both pages and posts.

```cs
services.AddAuthorization(options =>
{

	// this is what the above extension adds
	options.AddPolicy(
		"BlogEditPolicy",
		authBuilder =>
		{
			//authBuilder.RequireClaim("blogId");
			authBuilder.RequireRole("Administrators");
		}
	 );

	options.AddPolicy(
		"PageEditPolicy",
		authBuilder =>
		{
			authBuilder.RequireRole("Administrators");
		});

});
```

## Granting Edit Permission via Claims

Granting edit permissions by claims is independent of the policy approach, either approach can be used, as long as a user either meets the policy requirements or has the required claims is sufficient to grant edit permission.

If a user has a claim named "ContentEditor" with a value equal to the project id, then the user can edit both blog posts and pages.

If a user has a claim named "BlogEditor" with a value equal to the project id, then the user can edit blog posts.

If a user has a claim named "PageEditor" with a value equal to the project id, then the user can edit pages.

In a SingleTenant setup such as in the [SimpleAuth-Single Tenant Sample](https://github.com/joeaudette/CloudscribeNG.StarterKits/tree/master/SimpleContent-SimpleAuth-nodb), typically the default project id will be used, the default project id is literally the word "default".

In the [multi-tentant SimpleAuth sample](https://github.com/joeaudette/CloudscribeNG.StarterKits/tree/master/SimpleContent-SimpleAuth-nodb-multitenant) the project id is listed in the app-content-project-settings.json file.

In the CloudscribeNG Core samples the project id is the same as the Site Id Guid as a string.

## Supporting Metaweblog API / Open Live Writer

The security for the metaweblog api is handled a little differently since it is an RPC (Remote Procedure Call) style api. To make this work with your custom authentication system you need to implement [IProjectSecurityResolver](https://github.com/joeaudette/CloudscribeNG.SimpleContent/blob/master/src/CloudscribeNG.SimpleContent.Models/IProjectSecurityResolver.cs) which has a single method returning a [ProjectSecurityResult](https://github.com/joeaudette/CloudscribeNG.SimpleContent/blob/master/src/CloudscribeNG.SimpleContent.Models/ProjectSecurityResult.cs). To get an idea of how to implement that, you can take a look at the implementations for [CloudscribeNG Core](https://github.com/joeaudette/CloudscribeNG.SimpleContent/blob/master/src/CloudscribeNG.Core.SimpleContent.Integration/ProjectSecurityResolver.cs) and for [SimpleAuth](https://github.com/joeaudette/CloudscribeNG.SimpleContent/blob/master/src/CloudscribeNG.SimpleContent.Security.SimpleAuth/ProjectSecurityResolver.cs). Basically both implementations are just checking the user for claims or policy criteria, and returning a result indicating the user's permissions.

## CloudscribeNG Core Notes

Note that CloudscribeNG Core has built in functionality for administrators to add custom claims to user accounts, so you can give users edit permissions directly from the UI without needing to add users to roles that meet the policy requirements. Also the integration library provides a UI for managing project settings, and in this UI it also shows a list of any users who do have the editor claims so you can be aware of who has edit access easily.
