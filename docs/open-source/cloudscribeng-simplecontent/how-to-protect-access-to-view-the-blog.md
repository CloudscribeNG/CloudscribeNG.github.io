# How to protect access to view the blog

In general blogging is a public publishing medium, people usually allow anonymous visitors to view their blogs. Often blogs are used to help drive viewers to a site for marketing purposes. So by default the blog allows anonymous access to read blog posts.

However, there may be some use cases where people would like to limit access to the blog feature to authenticated users or specific roles. In the latest version of CloudscribeNG SimpleContent we added an authorization policy for viewing the blog. The default policy still allows anonymous users, but by having a named policy to protect it, you are able to override the policy with your own requirements.

The default authorization policies for SimpleContent are as follows:

```cs
options.AddPolicy("BlogViewPolicy", policy =>
	policy.RequireAssertion(context =>
	{
		return true; //allow anonymous
	})
	);

options.AddPolicy(
		"BlogEditPolicy",
		authBuilder =>
		{
			//authBuilder.RequireClaim("blogId");
			authBuilder.RequireRole("Administrators", "Content Administrators");
		}
	 );

options.AddPolicy(
	"PageEditPolicy",
	authBuilder =>
	{
		authBuilder.RequireRole("Administrators", "Content Administrators");
	});
```

If you are using CloudscribeNG Core with CloudscribeNG SimpleContent, those policies are added with this one line:

```cs
services.AddAuthorization(options =>
{
    // other policies left out for brevity

    // you would comment out this line and add the policies here directly
    // with your own requirements such as roles or claims
	options.AddCloudscribeNGCoreSimpleContentIntegrationDefaultPolicies();

});
```

You could comment out or remove the one line shown above and then add the policies yourself and customize the requirements to meet the policy.

See also the [official documentation for ASP.NET Core Policy Based Authorization](https://docs.microsoft.com/en-us/aspnet/core/security/authorization/policies).
