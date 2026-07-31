# Multi Tenant Support

CloudscribeNG Core supports multi-tenant web sites using a single database and website installation. User accounts can be common to all tenants, or segregated. Two mutually exclusive models are supported: Host Name based or Folder based. To give a simple example of what we mean, let's say we want two sites, one for the Physics Department and one for Chemistry, at our online University "brainiacs.com".

Host Name tenants:

https://physics.brainiacs.com/my-page  

https://chemistry.brainiacs.com/my-page

Folder tenants:

https://www.brainiacs.com/physics/my-page  

https://www.brainiacs.com/chemistry/my-page

Another scenario might be a bi-lingual site for the university, managed as two tenant sites, so you might have

Host Name tenants (read about [an example project that used this approach](/blog/2017/03/28/case-study-historic-place-names-of-wales))

https://english.brainiacs.com/my-page  

https://francais.brainiacs.com/ma-page

Folder tenants:

https://www.brainiacs.com/english/my-page  

https://www.brainiacs.com/francais/ma-page

You can use one or the other for a given installation. In all cases the first site created is considered the master site. In the database it has the field IsServerAdminSite set to true, and it can create new "child" sites as well as manage child site settings, users, and roles.

By default, a user can only sign in to the tenant site in which their account was created. There is also a "Related Sites Mode" setting. When this is enabled, the users and roles attached to one site will be used by all the sites in the installation. This means a user can sign in to all tenant sites with the same credentials, and in folder tenants the same authentication cookie will work across all sites so they will only need to sign in once.

The users and roles and other data are tagged with a SiteId and this is used when the data is retrieved. In the case of RelatedSitesMode the users and roles are all tagged with the RelatedSiteId, but other data can still be site specific.

## Host Name Sites

The tenant is resolved by the host name, ie "somedomain.com" is one host name, "www.somedomain.com" is another host name, and "www.anotherdomain.com" is another. You can map one or more host names to any given CloudscribeNG tenant site. Any host names that are not mapped to a specific tenant site are mapped to the default site, ie the "master" site. Typically for SEO you don't want duplicate content at different host names, so the notion of a preferred host name is also going to be supported so that if a site is handling multiple host names you can redirect all of them to the preferred one. ie you may prefer "somedomain.com" vs "www.somedomain.com" and you can redirect requests for "www.somedomain.com" to "somedomain.com" if that is your preferred host name.

For a given domain name you can create as many host name DNS "A" records as you like "host1.somedomain.com", "host2.somedomain.com", "cheeseburger.somedomain.com" or "whatever.somedomain.com".

You can create separate new sites using those host names all in a single installation of CloudscribeNG: you create the site and assign the host name mapping.

Of course the ip address for the host name "A" record must be the ip address where the site is running, and if it is a shared ip address then you must also have host name bindings added to the site in the web server, eg IIS (Windows Internet Information Server), so that the web server will pass the request to the correct site.

This need for creating DNS host names and host header mappings in IIS means that a technical person has to be involved when setting up a new site; non-technical people cannot easily provision a new site in this model.

## Folder Name Sites

The tenant is resolved by the first folder segment in the url, except for the first site which "must" be a root level site. The fist site created during setup is considered as the "master site" and it does not have any folder mappings assigned to it and it must run as a root level site in order to resolve the child site ids correctly from the first url segment.

So the master site must work with an url like "somedomain.com/" or "www.somedomain.com/" and then child sites can be created with urls like: "somedomain.com/site2/" "somedomain.com/foo/" "somedomain.com/cheeseburger/" "somedomain.com/whatever/"

Folder based child sites are easier to provision and are very useful for sub-sites that are like sections of one site but with different authoring teams.

## Configuration

This example from appsettings.json shows the current default settings. You can easily change or override these settings.

```json
"MultiTenantOptions": {
"Mode": "FolderName",
"UseRelatedSitesMode": "false",
"RelatedSiteId": "00000000-0000-0000-0000-000000000000",
"RelatedSiteAliasId":"",
"UserPerSiteWwwRoot": "true",
"UserPerSiteThemes": "true",
"UseSharedThemes": "true",
"SiteFilesFolderName": "sitefiles",
"SiteThemesFolderName": "themes",
"SiteContentFolderName": "wwwroot",
"SiteUploadFilesRootFolderName": "siteuploadfiles",
"SharedThemesFolderName": "SharedThemes",
"ThemeStaticFilesFolderName": "wwwroot",
"RootUserCanSignInToTenants": "false",
"RootSiteId": "00000000-0000-0000-0000-000000000000"
},
"NewUserOptions": {
DefaultNewUserRoles": "Authenticated Users;Content Administrators"
}
```

Mode can be FolderName or HostName or None.

If UseRelatedSitesMode is set to true, then all sites will lookup users and roles using the RelatedSiteId, which would typically be the first site created. You can find the siteid in the site list or in the database.

Note the DefaultNewUserRoles property which includes "Authenticated Users" which is the name of a role that will be added to all users as they are created. I recommend keeping that role, but if you want to add additional roles make them semicolon separated, as shown.

The **SiteFilesFolderName **is the root folder where site specific themes and related static resources go. They are organized into separate sub folders per site using the site alias id ie s1, s2, etc. In early versions of CloudscribeNG the user upload files were also stored there, but later we added the **SiteUploadFilesRootFolderName **setting to separate the the user uploaded files from the theme files. Site specific uploads are organized similarly using alias id sub folders so that uploaded files are separated per site. The reason we decided to put the user uploaded files in a separate folder is to make it more docker friendly so you could mount a docker volume for the siteuploadfiles folder. You could not really do that with the themes because the layout and views in a theme need to be compiled as part of the application so it would be problematic to mount a volume for the sitefiles folder. In order not to break existing sites that already had user uploads below sitefiles folder, we kept "sitefiles" as the default value for SiteUploadFilesRootFolderName in the MultiTenantOptions class, but we override it to "siteuploadfiles" with the default appsettings in the latest project template so when new projects are created they get setup using siteuploadfiles folder.

Note that if you are using related sites mode and you want all the sites to also share the same media folder (user uploads), you can specify the RelatedSiteAliasId that matches the master site and all sites will use the same user uploads. If you don't specify this then each site will have its own separate user uploads folder. The aliasid is just a simple string like s1, s2, you can find it in site settings. It is used so we don't have to use an ugly guid (like the site id) for the folder name.

## Should I Host Multiple Customer Sites as Multi-Tenant?

That is of course a business decision that is up to you. Note that the data for all tenants of a given installation are in the same database tables. Two sites managed as tenants within one CloudscribeNG site may use less server resource, and reduce maintenance time needed for upgrades, but on the other hand the isolation between data with different owners is less clear cut, it makes moving one site to different hosting less simple, and maintenance on one of the sites will impact on the other. So in general we see multi-tenancy as a useful thing for multiple sites owned by a single customer, and we recommend a separate installation per customer.

## Managing Sites

When you first setup CloudscribeNG Core, the first site is created for you automatically, and this site is set in the database with IsServerAdminSite = true, making it the root administrative site. This setting allows members of the Administrators role in the site to create other sites and manage users and roles for other sites. Other sites created from the UI are not flagged as server admin sites so administrators of those sites can only manage the site they belong to.

By default users and roles are separate for each site. It is possible for the same user to register and login to multiple sites but their membership in one site does not grant them access to protected resources in other sites (with the exception of Administrators in the administrative site who as mentioned before can manage other sites, but they do this while logged into the root administrative site, they are not able to login to the other sites without creating a user account in the other sites). There are site settings for each site to enable or disable certain things. For example it is possible to disable registration, so no users can register on a site and only administrators can add users to the site.

As you can see in this model the site (aka tenant) is resolved first based on the hostname or the first folder segment of the request. The user must then log into the site or tenant to access any protected resources and their access to protected resources depends on the configured authorization policies and the user's roles and claims (within the given site) which are compared against the requirements of the policy. The authorization policies are configured in the Startup.cs of the web application, where they can be customized. So for example there is a policy protecting the administrative area in CloudscribeNG Core, it requires the user to be in the Administrators role. While each site will have a role named Administrators, the user must be in the role for the site resolved from the request. Members of the Administrators role of one site are not administrators of other sites, even though other sites also have a role with that name. A user could have a separate account in each site with the same email and they could be added to the Administrators role of each site if you want to make them administrators but users and roles are not shared across sites unless the system is configured with related sites mode. For more information on defining custom authorization policies see the [documentation here](https://docs.microsoft.com/en-us/aspnet/core/security/authorization/policies).

When you create multiple sites with CloudscribeNG core there is nothing protected by default except the administrative area in each site. If you have a public home controller that allows anonymous access then users can still browse the home page of any site. If you wanted to prevent that then you would have to define a policy to protect the home page controller, then you could decorate the controller class or specific controller actions that you want to protect with a policy attribute:

```cs
[Authorize(Policy = "HomePagePolicy")]
```

You would have to define the policy with that name in Startup.cs something like this:

```cs
services.AddAuthorization(options =>
{
	options.AddPolicy(
		"HomePagePolicy",
		authBuilder =>
		{
			authBuilder.RequireRole("Administrators", "Content Administrators");
		});
		// add more policies here as needed

});
```

If instead of using a standard home controller, you use CloudscribeNG.SimpleContent, it allows you to create virtual pages and each virtual page has a setting for "AllowedViewRoles". The setting is empty by default, but if you add one or more roles to it then the page would be protected by the required view roles and will not be accessible to anyone who is not a member of the role. The page would also be automatically filtered out of the menu in this case for users who are not members of the needed roles.

## What about a user per tenant solution?

This question comes up from time to time: it seems some people want to be able to resolve the user first and then determine the tenant based on the user. Sorry but that is not how it works in CloudscribeNG Core. What would be possible is to have tenants defined that correspond to users, but then in the root administrative site you would need a custom feature to let the user enter his email address, then you could lookup the tenant in a custom table and redirect the user to the url of the tenant. So you would need a master list in the database to relate the email address of the user to a tenant.

## What about a database per tenant solution?

That wasn't the goal for CloudscribeNG Core: the main aim is to make it easy to provision new sites. A database-per-tenant solution would require manual steps to add new tenants, such as creating a new database and database user and keeping a list of the tenants and their database connection strings in a central master database. We don't have any plans to implement that kind of a solution near term, but we are not ruling out doing that in the future. We are also interested in providing a way to import and export tenant data to facilitate moving a tenant to another installation or database. We don't have a timeline for implementing that, but the interim solution is that if we need to provide a copy of a single tenant we can make a copy of the entire site database, and delete the other tenant data in the copy, leaving just the data of the required single tenant. As discussed above, we recommend an installation per customer, so if there are multiple tenants in the installation, they are tenants that all belong to one customer and we don't often need to separate the data later.

## Further guidance about UseRelatedSitesMode

With UseRelatedSitesMode set to true, it is still possible to enable registration in each of the tenants, and the process will respect the settings in the tenant (e.g. whether to require approval and whether to validate the email address). But if the aim is to provide one set of users equal access to all tenants, it is probably more sensible to switch off registration in all tenants other than the one identified by the RelatedSiteId, and direct users to register in that site.

Having these two different modes of operation provides powerful options to meet differing project requirements, but it's good to make this decision at the start of the project rather than changing in a live site. The implications of changing this setting in a live site with registered users are as follows.

Changing UseRelatedSitesMode from false to true. Tenants other than the one identified by the RelatedSiteId will appear to lose their existing users and roles, and those users will not be able to sign in. This can be resolved by updating [cs_User].[SiteId] values for all users to the RelatedSiteId.

Changing UseRelatedSitesMode from true to false. Tenants other than the one identified by the RelatedSiteId will have no users or roles. Existing users will be able to sign in to the site identified by the RelatedSiteId, but not to other tenants. This can be resolved by updating [cs_User].[SiteId] values for particular users to a tenant, creating new accounts for users, or having users register in the tenant. In addition you may need to create roles for each tenant (see [cs_Role]) and associate users with these roles for the tenant ([cs_UserRole]); these operations can be undertaken in the database or in the administrative pages of the site, so long as you have one administrative account able to sign in.

## [RootUserCanSignInToTenants Mode]()

When setting up a multi-tenant site, administrative users of the main 'root' site often want to login to the tenant sites, for configuration and support purposes. With UseRelatedSitesMode this is simple, but when tenant users are isolated this used to require creating separate accounts in each tenant site, which can be laborious and inconvenient if there are many tenant sites.

Since version 4.1.13 of CloudscribeNG.Core.Web we now support a mode called 'RootUserCanSignInToTenants'.  When this mode is enabled, then any user of the specified 'root' site will also be able to log in to any tenant site within the overall CloudscribeNG deployment, using the username and password from their root account. 

Note that this is not a 'single sign-on' mechanism.  The root user will need to re-authenticate to every tenant site that they wish to visit, via its Login screen (each site will set a separate authentication cookie).  However, the user's authentication credentials and other user account properties (e.g. display name) will remain the same within every tenant site. The CloudscribeNG database continues to treat them as a single user, belonging to the root site - i.e. there will still be just one user record for them in the CloudscribeNG users table.

RootUserCanSignInToTenants mode is incompatible with RelatedSitesMode: you cannot enable both at the same time.  If you enable RelatedSitesMode, then any attempts to enable RootUserCanSignInToTenants mode will be ignored.

To enable RootUserCanSignInToTenants mode, add the following two entries to the MultiTenantOptions section of your appSettings.json:

```cs
"RootUserCanSignInToTenants": "true",
"RootSiteId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa"
```

where the RootSiteId value is replaced by the Guid of the site that you wish to regard as the root site - i.e. the site whose users will be granted the above additional authentication rights.

Note that this setting has no impact at all on users of non-root tenant sites: they can still only login to their own tenant sites.

When tenant sites are configured to use [Social Authentication](/using-social-authentication), root site administrators should continue to sign in to tenant sites with username and password, as social authentication in a tenant site doesn't work correctly with this mode.

## Roles and Authorization Policies in RootUserCanSignInToTenants Mode

When a root site user logs in to a tenant site using their root site credentials in RootUserCanSignInToTenants mode, they will still be subject to any restrictions specified by authorization policies declared in the **visited** tenant. 

However, when those authorization policies specify that the user needs to be in a specific role, then this is evaluated for the visiting user on the basis of their roles in their **home** (root) site - i.e. they "bring with them" any role memberships that they have been granted in the root site.

This evaluation is done by role name (case sensitive), so you might need to consider whether you want to define identically named roles in root and tenant sites, depending on the authorization privileges you want your root site users to experience when they visit a tenant site.
