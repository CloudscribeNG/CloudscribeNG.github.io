# CloudscribeNG.Core

The CloudscribeNG.Core set of libraries provides management for users, roles, and claims for either a single web site or a [multi-tenant](/docs/multi-tenant-support) aka multi-site installation using a single database. The users, roles, and claims are all tagged with a SiteId, the site is resolved first and then all data is retrieved using the SiteId, so that each site has separate users, roles, and claims. It also supports the concept of "Related Sites Mode" which allows you to use one set of users, roles, and claims in all the tenant sites. You can also disable the multi-tenancy feature if you only want a single site, but the data is still tagged with a SiteId, so you could always enable it later if you change your mind.

## Why Start From Scratch?

Every web application or website project tends to need a certain amount of basic functionality: why build this over and over?

If you start a new web application project in Visual Studio using the standard project templates, what you get is just a basic implementation for user accounts via ASP.NET Identity. Those templates don't provide you any method for creating administrative users or creating roles or managing users and user role membership. You would typically have to implement that stuff yourself, and if you are like me, you don't want to have to implement that stuff again and again on every project. CloudscribeNG.Core aims to provide that for you with careful, well thought out implementations that adhere to [OWASP web security guidelines](https://www.owasp.org/index.php/Main_Page).

### What Is Included

	
- Login and registration, with support for social authentication. Options for recaptcha on the login and registration pages.
	
- Support for extra content on the login page.
	
- Support for extra content and a terms of use section on the registration page. If you populate the terms of use then users will be required to check a box indicating that they accept the terms in order to register and login. Also if you change the terms later you can optionally force all users to re-accept the changed terms.
	
- User Management (optionally [multi-tenant](/docs/multi-tenant-support) user management) you can create and manage user accounts, create and manage roles and user role membership, and add custom claims to users all from the UI. You can optionally disable self-serve user registration so that only users that you add are allowed. 
	
- If you change a user's role membership, the role cookie will be updated automatically so the changes are effective right away.
	
- If you lock a user account or delete a user, the user will be signed out automatically.
	
- [A theme system that supports both shared themes and per tenant themes](/docs/themes-and-web-design). You can set the theme from a dropdown list in Administration > Site Settings, and the starter kits have a bunch of bootstrap themes included, and you can also make your own themes.
	
- Support for "Site is Closed" - you can set a site as closed and users will not be able to navigate any pages in the site, they will only see the message you provide on the closed page. Users can still login but only members of the Administrators or Content Administrators roles will be allowed to navigate the site, all other users will be redirected to the closed message.
	
- You can optionally require a confirmed email address for users if you add SMTP settings for email. A confirmation email will be sent to the user and the user will not be able to login until they click the link to confirm their email address.
	
- You can optionally require approval of new accounts before a user can login, and you can get notification when new users register so you can decide whether to approve the account. There is a separate page to make it easy to find users who have not yet been approved or who have not yet confirmed their email address. New user, and new user approval notifications require you to set a valid email address for the site administrator(s).
	
- If you setup social authentication, you can optionally make social authentication the only allowed way to sign in.
	
- You can configure SMS settings for Twilio, and then users can enable 2 factor authentication using their phone.
	
- There is a company information section where you can define company name, address, email etc, and then you can show that information in the footer by customizing the layout. SiteContext is already injected into the layout and the company information are just properties on that so you can wrap your own markup around whichever of those properties you want to show.

## Getting Started

See the [Introduction](https://www.CloudscribeNG.com/docs/introduction) to learn about how to start new projects with our Visual Studio template extension, or our dotnet new project template.

Currently CloudscribeNG Core supports NoDb file system storage, or EntityFrameworkCore with either Microsoft Sql Server, PostgreSql, or MySql.

When you first run CloudscribeNG Core it automatically creates the first tenant/site, and a user with administrator role. You can login as admin@admin.com with the password admin. Be sure to change those account credentials before deploying your site.

You can see the complete list of [CloudscribeNG.Core NuGet packages here](http://www.nuget.org/packages?q=CloudscribeNG.Core), and you can find the [source code on github](https://github.com/joeaudette/CloudscribeNG).

CloudscribeNG Core also provides integration with IdentityServer4, so that you can use openid connect and JWT authentication for SPA (Single Page Application) style apps. 

## Need Content Editing Too?

For editable pages and a blog, CloudscribeNG.Core integrates with [CloudscribeNG.SimpleContent](/docs/CloudscribeNG-simplecontent), and this combination may provide all that you need for many sites.

## Learn More
