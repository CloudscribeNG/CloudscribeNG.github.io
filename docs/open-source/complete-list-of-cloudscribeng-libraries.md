# Complete list of open source CloudscribeNG libraries

There are two "big" sets of functionality: **CloudscribeNG Core** ([documentation](/docs/CloudscribeNG-core)) which provides management of sites, users, roles, and claims, and **CloudscribeNG SimpleContent** ([documentation](/docs/CloudscribeNG-simplecontent)) which is a blog and content engine. There is also integration between these sets of features so they can work together, but CloudscribeNG Core can be used without CloudscribeNG SimpleContent, and CloudscribeNG SimpleContent can work without CloudscribeNG Core.

The best way to wire up these NuGet packages is using our [Project Template for Visual Studio](https://marketplace.visualstudio.com/items?itemName=joeaudette.CloudscribeNGProjectTemplate), or our [Project Template for dotnet new](https://www.nuget.org/packages/CloudscribeNG.templates/), as explained in the [Introduction](https://www.CloudscribeNG.com/docs/introduction).

See also the [complete list of our commercial libraries](/docs/complete-list-of-CloudscribeNG-commercial-product-libraries).

## CloudscribeNG Core Set of Libraries

	
- CloudscribeNG.Core.Web ([NuGet](http://www.nuget.org/packages/CloudscribeNG.Core.Web/) | [GitHub](https://github.com/joeaudette/CloudscribeNG/tree/master/src/CloudscribeNG.Core.Web) | [Documentation](https://www.CloudscribeNG.com/docs/CloudscribeNG-core)) - this is the main project to reference.
	
- CloudscribeNG.Core.CompiledViews.Bootstrap4 ([NuGet](https://www.nuget.org/packages/CloudscribeNG.Core.CompiledViews.Bootstrap4/) | [GitHub](https://github.com/CloudscribeNG/CloudscribeNG/tree/master/src/CloudscribeNG.Core.CompiledViews.Bootstrap4))
	
- CloudscribeNG.Core.CompiledViews.Bootstrap5 ([NuGet](https://www.nuget.org/packages/CloudscribeNG.Core.CompiledViews.Bootstrap5/) | [GitHub](https://github.com/CloudscribeNG/CloudscribeNG/tree/master/src/CloudscribeNG.Core.CompiledViews.Bootstrap5))
	
- CloudscribeNG.Core.Models ([NuGet](http://www.nuget.org/packages/CloudscribeNG.Core.Models) | [GitHub](https://github.com/joeaudette/CloudscribeNG/tree/master/src/CloudscribeNG.Core.Models)) - models and storage interfaces.
	
- CloudscribeNG.Core.Identity ([NuGet](http://www.nuget.org/packages/CloudscribeNG.Core.Identity) | [GitHub](https://github.com/joeaudette/CloudscribeNG/tree/master/src/CloudscribeNG.Core.Identity)) - multi-tenant implementation of ASP.NET Core Identity.
	
- CloudscribeNG.FileManager.CoreIntegration ([NuGet](http://www.nuget.org/packages/CloudscribeNG.FileManager.CoreIntegration) | [GitHub](https://github.com/joeaudette/CloudscribeNG/tree/master/src/CloudscribeNG.FileManager.CoreIntegration)) - integrates CloudscribeNG FileManager with CloudscribeNG Core.
	
- CloudscribeNG.Core.Storage.NoDb ([NuGet](http://www.nuget.org/packages/CloudscribeNG.Core.Storage.NoDb) | [GitHub](https://github.com/joeaudette/CloudscribeNG/tree/master/src/CloudscribeNG.Core.Storage.NoDb)) - implementation of the storage interfaces defined in CloudscribeNG.Core.Models using [NoDb](https://github.com/joeaudette/NoDb) file system storage.
	
- CloudscribeNG.Core.Storage.EFCore.SQLite ([NuGet](https://www.nuget.org/packages/CloudscribeNG.Core.Storage.EFCore.SQLite/) | [GitHub](https://github.com/joeaudette/CloudscribeNG/tree/master/src/CloudscribeNG.Core.Storage.EFCore.SQLite)) - implementation of the storage interfaces defined in CloudscribeNG.Core.Models using EntityFrameworkCore and SQLite.
	
- CloudscribeNG.Core.Storage.EFCore.MSSQL ([NuGet](https://www.nuget.org/packages/CloudscribeNG.Core.Storage.EFCore.MSSQL/) | [GitHub](https://github.com/joeaudette/CloudscribeNG/tree/master/src/CloudscribeNG.Core.Storage.EFCore.MSSQL)) - implementation of the storage interfaces defined in CloudscribeNG.Core.Models using EntityFrameworkCore and Microsoft SqlServer.
	
- CloudscribeNG.Core.Storage.EFCore.pgsql ([NuGet](http://www.nuget.org/packages/CloudscribeNG.Core.Storage.EFCore.pgsql) | [GitHub](https://github.com/joeaudette/CloudscribeNG/tree/master/src/CloudscribeNG.Core.Storage.EFCore.pgsql)) - implementation of the storage interfaces defined in CloudscribeNG.Core.Models using EntityFrameworkCore and PostgreSQL.
	
- CloudscribeNG.Core.Storage.EFCore.MySql ([NuGet](http://www.nuget.org/packages/CloudscribeNG.Core.Storage.EFCore.MySql) | [GitHub](https://github.com/joeaudette/CloudscribeNG/tree/master/src/CloudscribeNG.Core.Storage.EFCore.MySql)) - implementation of storage interfaces defined in CloudscribeNG.Core.Models using EntityFrameworkCore and MySQL.
	
- CloudscribeNG.Core.Storage.EFCore.Common ([NuGet](http://www.nuget.org/packages/CloudscribeNG.Core.Storage.EFCore.Common) | [GitHub](https://github.com/joeaudette/CloudscribeNG/tree/master/src/CloudscribeNG.Core.Storage.EFCore.Common)) - code used in common by the above 4 libraries, ie those all depend on this one.

### Optional Integration Libraries

	
- CloudscribeNG.Core.SimpleContent ([NuGet](http://www.nuget.org/packages/CloudscribeNG.Core.SimpleContent) | [GitHub](https://github.com/joeaudette/CloudscribeNG.SimpleContent/tree/master/src/CloudscribeNG.Core.SimpleContent)) - this integrates CloudscribeNG SimpleContent with CloudscribeNG Core.
	
- CloudscribeNG.SimpleContactForm.CoreIntegration ([NuGet](https://www.nuget.org/packages/CloudscribeNG.SimpleContactForm.CoreIntegration/) | [GitHub](https://github.com/CloudscribeNG/CloudscribeNG.SimpleContactForm/tree/master/src/CloudscribeNG.SimpleContactForm.CoreIntegration)) integration for CloudscribeNG.SimpleContactForm and CloudscribeNG.Core.
	
- CloudscribeNG.Core.IdentityServerIntegration ([NuGet](http://www.nuget.org/packages/CloudscribeNG.Core.IdentityServerIntegration) | [GitHub](https://github.com/joeaudette/CloudscribeNG/tree/master/src/CloudscribeNG.Core.IdentityServerIntegration)) - integrates with IdentityServer4.
	
- CloudscribeNG.IdentityServerIntegration.CompiledViews.Bootstrap4 ([NuGet](https://www.nuget.org/packages/CloudscribeNG.IdentityServerIntegration.CompiledViews.Bootstrap4/) | [GitHub](https://github.com/CloudscribeNG/CloudscribeNG/tree/master/src/CloudscribeNG.IdentityServerIntegration.CompiledViews.Bootstrap4))
	
- CloudscribeNG.IdentityServerIntegration.CompiledViews.Bootstrap5 ([NuGet](https://www.nuget.org/packages/CloudscribeNG.IdentityServerIntegration.CompiledViews.Bootstrap5/) | [GitHub](https://github.com/CloudscribeNG/CloudscribeNG/tree/master/src/CloudscribeNG.IdentityServerIntegration.CompiledViews.Bootstrap5))
	
- CloudscribeNG.Core.IdentityServer.EFCore.MSSQL - ([NuGet](http://www.nuget.org/packages/CloudscribeNG.Core.IdentityServer.EFCore.MSSQL) | [GitHub](https://github.com/joeaudette/CloudscribeNG/tree/master/src/CloudscribeNG.Core.IdentityServer.EFCore.MSSQL)) - data storage for IdentityServer4 integration using EntityFrameworkCore and Microsoft SqlServer.
	
- CloudscribeNG.Core.IdentityServer.EFCore.SQLite ([NuGet](https://www.nuget.org/packages/CloudscribeNG.Core.IdentityServer.EFCore.SQLite/) | [GitHub](https://github.com/joeaudette/CloudscribeNG/tree/master/src/CloudscribeNG.Core.IdentityServer.EFCore.SQLite)) -data storage for IdentityServer4 integration using EntityFrameworkCore and SQLite.
	
- CloudscribeNG.Core.IdentityServer.EFCore.pgsql ([NuGet](http://www.nuget.org/packages/CloudscribeNG.Core.IdentityServer.EFCore.pgsql) | [GitHub](https://github.com/joeaudette/CloudscribeNG/tree/master/src/CloudscribeNG.Core.IdentityServer.EFCore.pgsql)) - data storage for IdentityServer4 integration using EntityFrameworkCore and PostgreSQL.
	
- CloudscribeNG.Core.IdentityServer.EFCore.MySql ([NuGet](http://www.nuget.org/packages/CloudscribeNG.Core.IdentityServer.EFCore.MySql) | [GitHub](https://github.com/joeaudette/CloudscribeNG/tree/master/src/CloudscribeNG.Core.IdentityServer.EFCore.MySql)) - data storage for IdentityServer4 integration using EntityFrameworkCore and MySQL.
	
- CloudscribeNG.Core.IdentityServer.EFCore.Common ([NuGet](http://www.nuget.org/packages/CloudscribeNG.Core.IdentityServer.EFCore.Common) | [GitHub](https://github.com/joeaudette/CloudscribeNG/tree/master/src/CloudscribeNG.Core.IdentityServer.EFCore.Common)) - the 3 libraries above all depend on this one.

### Optional Extension Point Customization Libraries for CloudscribeNG Core

	
- CloudscribeNG.UserProperties ([NuGet](https://www.nuget.org/packages/CloudscribeNG.UserProperties/) | [GitHub](https://github.com/CloudscribeNG/CloudscribeNG.UserProperties.Kvp)) - models for configuration based custom user properties.
	
- CloudscribeNG.UserProperties.Kvp ([NuGet](https://www.nuget.org/packages/CloudscribeNG.UserProperties.Kvp) | [GitHub](https://github.com/CloudscribeNG/CloudscribeNG.UserProperties.Kvp) | [Documentation](/docs/configuration-based-custom-registration-and-user-properties)) - implementations of [IHandleCustomRegistration](https://github.com/CloudscribeNG/CloudscribeNG.UserProperties.Kvp/blob/master/src/CloudscribeNG.UserProperties.Kvp/KvpRegistrationHandler.cs), [IHandleCustomUserInfo](https://github.com/CloudscribeNG/CloudscribeNG.UserProperties.Kvp/blob/master/src/CloudscribeNG.UserProperties.Kvp/KvpUserInfoHandler.cs), and [IHandleCustomUserInfoAdmin](https://github.com/CloudscribeNG/CloudscribeNG.UserProperties.Kvp/blob/master/src/CloudscribeNG.UserProperties.Kvp/KvpUserInfoAdminHandler.cs), that uses the above models, and persists custom data to a generic key/value data storage using libraries below.
	
- CloudscribeNG.Kvp.Models ([NuGet](https://www.nuget.org/packages/CloudscribeNG.Kvp.Models) | [GitHub](https://github.com/CloudscribeNG/CloudscribeNG.UserProperties.Kvp)) - models for generic key/value storage.
	
- CloudscribeNG.Kvp.Storage.EFCore.MSSQL ([NuGet](https://www.nuget.org/packages/CloudscribeNG.Kvp.Storage.EFCore.MSSQL/) | [GitHub](https://github.com/CloudscribeNG/CloudscribeNG.UserProperties.Kvp)) - storage for CloudscribeNG.Kvp.Models implemented for Microsoft SqlServer using EntityFramework Core.
	
- CloudscribeNG.Kvp.Storage.EFCore.SQLite ([NuGet](https://www.nuget.org/packages/CloudscribeNG.Kvp.Storage.EFCore.SQLite/) | [GitHub](https://github.com/CloudscribeNG/CloudscribeNG.UserProperties.Kvp)) -storage for CloudscribeNG.Kvp.Models implemented for SQLite using EntityFramework Core.
	
- CloudscribeNG.Kvp.Storage.EFCore.pgsql ([NuGet](https://www.nuget.org/packages/CloudscribeNG.Kvp.Storage.EFCore.pgsql/) | [GitHub](https://github.com/CloudscribeNG/CloudscribeNG.UserProperties.Kvp)) - storage for CloudscribeNG.Kvp.Models implemented for PostgreSql using EntityFramework Core.
	
- CloudscribeNG.Kvp.Storage.EFCore.MySql ([NuGet](https://www.nuget.org/packages/CloudscribeNG.Kvp.Storage.EFCore.MySql/) | [GitHub](https://github.com/CloudscribeNG/CloudscribeNG.UserProperties.Kvp)) - storage for CloudscribeNG.Kvp.Models implemented for MySqll using EntityFramework Core.
	
- CloudscribeNG.Kvp.Storage.NoDb ([NuGet](https://www.nuget.org/packages/CloudscribeNG.Kvp.Storage.NoDb/) | [GitHub](https://github.com/CloudscribeNG/CloudscribeNG.UserProperties.Kvp)) - storage for CloudscribeNG.Kvp.Models implemented using NoDb file system storage, this is not recommended for use with large sites and lots of users.

## CloudscribeNG SimpleContent Libraries

	
- CloudscribeNG.SimpleContent.Web ([NuGet](http://www.nuget.org/packages/CloudscribeNG.SimpleContent.Web) | [GitHub](https://github.com/joeaudette/CloudscribeNG.SimpleContent/tree/master/src/CloudscribeNG.SimpleContent.Web) | [Documentation](https://www.CloudscribeNG.com/docs/CloudscribeNG-simplecontent)) - MVC controllers - main package to reference.
	
- CloudscribeNG.SimpleContent.CompiledViews.Bootstrap4 ([NuGet](https://www.nuget.org/packages/CloudscribeNG.SimpleContent.CompiledViews.Bootstrap4/) | [GitHub](https://github.com/CloudscribeNG/CloudscribeNG.SimpleContent/tree/master/src/CloudscribeNG.SimpleContent.CompiledViews.Bootstrap4))
	
- CloudscribeNG.SimpleContent.CompiledViews.Bootstrap5 ([NuGet](https://www.nuget.org/packages/CloudscribeNG.SimpleContent.CompiledViews.Bootstrap5/) | [GitHub](https://github.com/CloudscribeNG/CloudscribeNG.SimpleContent/tree/master/src/CloudscribeNG.SimpleContent.CompiledViews.Bootstrap5))
	
- CloudscribeNG.SimpleContent.ContentTemplates.Bootstrap4 ([NuGet](https://www.nuget.org/packages/CloudscribeNG.SimpleContent.ContentTemplates.Bootstrap4/) | [GitHub](https://github.com/CloudscribeNG/CloudscribeNG.SimpleContent/tree/master/src/CloudscribeNG.SimpleContent.ContentTemplates.Bootstrap4) | [Documentation](/docs/content-templates) | [Blog Post](https://www.CloudscribeNG.com/blog/2018/08/06/announcing-content-templates-for-simplecontent)) - content template features including image gallery, bing map, and easy layouts.
	
- CloudscribeNG.SimpleContent.ContentTemplates.Bootstrap5 ([NuGet](https://www.nuget.org/packages/CloudscribeNG.SimpleContent.ContentTemplates.Bootstrap5/) | [GitHub](https://github.com/CloudscribeNG/CloudscribeNG.SimpleContent/tree/master/src/CloudscribeNG.SimpleContent.ContentTemplates.Bootstrap5) | [Documentation](/docs/content-templates) | [Blog Post](https://www.CloudscribeNG.com/blog/2018/08/06/announcing-content-templates-for-simplecontent)) - content template features including image gallery, bing map, and easy layouts.
	
- CloudscribeNG.SimpleContent.Models ([NuGet](http://www.nuget.org/packages/CloudscribeNG.SimpleContent.Models) | [GitHub](https://github.com/joeaudette/CloudscribeNG.SimpleContent/tree/master/src/CloudscribeNG.SimpleContent.Models)) - models and storage interfaces.
	
- CloudscribeNG.SimpleContent.Storage.NoDb ([NuGet](http://www.nuget.org/packages/CloudscribeNG.SimpleContent.Storage.NoDb) | [GitHub](https://github.com/joeaudette/CloudscribeNG.SimpleContent/tree/master/src/CloudscribeNG.SimpleContent.Storage.NoDb)) - implementations of the storage interfaces defines in CloudscribeNG.SimpleContent.Models using NoDb file system storage.
	
- CloudscribeNG.SimpleContent.Storage.EFCore.SQLite ([NuGet](https://www.nuget.org/packages/CloudscribeNG.SimpleContent.Storage.EFCore.SQLite/) | [GitHub](https://github.com/joeaudette/CloudscribeNG.SimpleContent/tree/master/src/CloudscribeNG.SimpleContent.Storage.EFCore.SQLite)) - implementations of the storage interfaces defined in CloudscribeNG.SimpleContent.Models using EntityFrameworkCore and SQLite.
	
- CloudscribeNG.SimpleContent.Storage.EFCore.MSSQL ([NuGet](http://www.nuget.org/packages/CloudscribeNG.SimpleContent.Storage.EFCore.MSSQL) | [GitHub](https://github.com/joeaudette/CloudscribeNG.SimpleContent/tree/master/src/CloudscribeNG.SimpleContent.Storage.EFCore.MSSQL)) - implementations of the storage interfaces defined in CloudscribeNG.SimpleContent.Models using EntityFrameworkCore and Microsoft SqlServer.
	
- CloudscribeNG.SimpleContent.Storage.EFCore.pgsql ([NuGet](http://www.nuget.org/packages/CloudscribeNG.SimpleContent.Storage.EFCore.pgsql) | [GitHub](https://github.com/joeaudette/CloudscribeNG.SimpleContent/tree/master/src/CloudscribeNG.SimpleContent.Storage.EFCore.pgsql)) - implementations of the storage interfaces defined in CloudscribeNG.SimpleContent.Models using EntityFrameworkCore and PostgreSQL.
	
- CloudscribeNG.SimpleContent.Storage.EFCore.MySQL ([NuGet](http://www.nuget.org/packages/CloudscribeNG.SimpleContent.Storage.EFCore.MySQL) | [GitHub](https://github.com/joeaudette/CloudscribeNG.SimpleContent/tree/master/src/CloudscribeNG.SimpleContent.Storage.EFCore.MySQL)) - implementations of the storage interfaces defined in CloudscribeNG.SimpleContent.Models using EntityFrameworkCore and MySQL.
	
- CloudscribeNG.SimpleContent.Storage.EFCore.Common ([NuGet](http://www.nuget.org/packages/CloudscribeNG.SimpleContent.Storage.EFCore.Common) | [GitHub](https://github.com/joeaudette/CloudscribeNG.SimpleContent/tree/master/src/CloudscribeNG.SimpleContent.Storage.EFCore.Common)) - the above 3 libraries all depend on this one.
	
- CloudscribeNG.SimpleContent.Syndication ([NuGet](http://www.nuget.org/packages/CloudscribeNG.SimpleContent.Syndication) | [GitHub](https://github.com/joeaudette/CloudscribeNG.SimpleContent/tree/master/src/CloudscribeNG.SimpleContent.Syndication)) - provides the RSS feed implementation for SimpleContent Blog.
	
- CloudscribeNG.SimpleContent.MetaWeblog ([NuGet](http://www.nuget.org/packages/CloudscribeNG.SimpleContent.MetaWeblog) | [GitHub](https://github.com/joeaudette/CloudscribeNG.SimpleContent/tree/master/src/CloudscribeNG.SimpleContent.MetaWeblog)) - provides the metaweblog api implementation for using [Open Live Writer](/docs/using-open-live-writer) with SimpleContent.
	
- CloudscribeNG.SimpleContent.Security.SimpleAuth (NuGet | GitHub) a lightweight alternative to CloudscribeNG Core integration for small sites with a few pre-configured users. You could use this for small sites, but I generally recommend still use CloudscribeNG Core but use NoDb storage, that way you get nice administrative features.

## Other Libraries

	
- CloudscribeNG.Web.StaticFiles ([NuGet](https://www.nuget.org/packages/CloudscribeNG.Web.StaticFiles/) | [GitHub](https://github.com/CloudscribeNG/CloudscribeNG/tree/master/src/CloudscribeNG.Web.StaticFiles)) - commonly used static resources served as embedded files, CkEditor, Dataepicker, etc. These files used to be in CloudscribeNG.Web.Common but were separated.
	
- CloudscribeNG.Web.Common ([NuGet](http://www.nuget.org/packages/CloudscribeNG.Web.Common) | [GitHub](https://github.com/joeaudette/CloudscribeNG/tree/master/src/CloudscribeNG.Web.Common) | [Documentation](/docs/CloudscribeNG-web-common)) - lots of useful things: see the documentation for details.
	
- CloudscribeNG.DateTimeUtils ([NuGet](https://www.nuget.org/packages/CloudscribeNG.DateTimeUtils/) | [GitHub](https://github.com/CloudscribeNG/CloudscribeNG/tree/master/src/CloudscribeNG.DateTimeUtils)) - [easy time zone handling](/docs/easy-timezone-handling)
	
- CloudscribeNG.FileManager.Web ([NuGet](http://www.nuget.org/packages/CloudscribeNG.FileManager.Web) | [GitHub](https://github.com/joeaudette/CloudscribeNG/tree/master/src/CloudscribeNG.FileManager.Web)) - a standalone file manager, also used for file browsing and upload within CKeditor.
	
- CloudscribeNG.Web.Navigation ([NuGet](http://www.nuget.org/packages/CloudscribeNG.Web.Navigation) | [GitHub](https://github.com/joeaudette/CloudscribeNG.Web.Navigation/tree/master/src/CloudscribeNG.Web.Navigation) | [Documentation](/docs/CloudscribeNG-web-navigation)) - a solution for menus and breadcrumbs.
	
- CloudscribeNG.Web.SiteMap ([NuGet](http://www.nuget.org/packages/CloudscribeNG.Web.SiteMap) | [GitHub](https://github.com/joeaudette/CloudscribeNG.Web.Navigation/tree/master/src/CloudscribeNG.Web.SiteMap)) - a library for building an xml feed for submitting google sitemaps.
	
- CloudscribeNG.Web.SiteMap.FromNavigation ([NuGet](http://www.nuget.org/packages/CloudscribeNG.Web.SiteMap.FromNavigation) | [GitHub](https://github.com/joeaudette/CloudscribeNG.Web.Navigation/tree/master/src/CloudscribeNG.Web.SiteMap.FromNavigation)) - integration between the 2 libraries above to allow using the same tree of date that we build for the menu to build the google sitemap.
	
- CloudscribeNG.Web.Pagination ([NuGet](http://www.nuget.org/packages/CloudscribeNG.Web.Pagination) | [GitHub](https://github.com/joeaudette/CloudscribeNG.Web.Pagination)) - a TagHelper for implementing paginated lists.
	
- CloudscribeNG.Email.Senders ([NuGet](https://www.nuget.org/packages/CloudscribeNG.Email.Senders/) | [GitHub](https://github.com/CloudscribeNG/CloudscribeNG/tree/master/src/CloudscribeNG.Email.Senders)) - a simple helpers and a common api for sending email using SMTP, SendGrid, MailGun, or ElasticEmail.
	
- CloudscribeNG.SimpleContactForm ([NuGet](http://www.nuget.org/packages/CloudscribeNG.SimpleContactForm) | [GitHub](https://github.com/joeaudette/CloudscribeNG.SimpleContactForm)) -  very basic contact form that can relay email and has recaptcha support.
	
- CloudscribeNG.Web.Localization ([NuGet](http://www.nuget.org/packages/CloudscribeNG.Web.Localization) | [GitHub](https://github.com/joeaudette/CloudscribeNG.Web.Localization) | [Documentation](/docs/localization)) - a little more flexible than standard ASP.NET Core localization.
	
- CloudscribeNG.Web.SimpleAuth ([NuGet](http://www.nuget.org/packages/CloudscribeNG.Web.SimpleAuth) | [GitHub](https://github.com/joeaudette/CloudscribeNG.Web.SimpleAuth)) - a minimal login system for a small set of users pre-defined in a config file.
	
- CloudscribeNG.Logging.Web ([NuGet](http://www.nuget.org/packages/CloudscribeNG.Logging.Web) | [GitHub](https://github.com/joeaudette/CloudscribeNG.Logging/tree/master/src/CloudscribeNG.Logging.Web)) - simple custom logger with a UI for browsing/deleting logged events ie errors and warnings.
	
- CloudscribeNG.Logging.NoDb ([NuGet](http://www.nuget.org/packages/CloudscribeNG.Logging.NoDb) | [GitHub](https://github.com/joeaudette/CloudscribeNG.Logging/tree/master/src/CloudscribeNG.Logging.NoDb)) - log storage for CloudscribeNG.logging.Web using NoDb file system storage.
	
- CloudscribeNG.Logging.EFCore.MSSQL ([NuGet](http://www.nuget.org/packages/CloudscribeNG.Logging.EFCore.MSSQL) | [GitHub](https://github.com/joeaudette/CloudscribeNG.Logging/tree/master/src/CloudscribeNG.Logging.EFCore.MSSQL)) - log storage for CloudscribeNG.Logging.Web using EntityFrameworkCore and Microsoft SqlServer.
	
- CloudscribeNG.Logging.EFCore.pgsql ([NuGet](http://www.nuget.org/packages/CloudscribeNG.Logging.EFCore.pgsql) | [GitHub](https://github.com/joeaudette/CloudscribeNG.Logging/tree/master/src/CloudscribeNG.Logging.EFCore.pgsql)) - log storage for CloudscribeNG.Logging.Web using EntityFrameworkCore and PostgreSQL.
	
- CloudscribeNG.Logging.EFCore.MySql ([NuGet](http://www.nuget.org/packages/CloudscribeNG.Logging.EFCore.MySql) | [GitHub](https://github.com/joeaudette/CloudscribeNG.Logging/tree/master/src/CloudscribeNG.Logging.EFCore.MySql)) - log storage for CloudscribeNG.Logging.Web using EntityFrameworkCore and MySQL.
	
- CloudscribeNG.Logging.EFCore.Common ([NuGet](http://www.nuget.org/packages/CloudscribeNG.Logging.EFCore.Common) | [GitHub](https://github.com/joeaudette/CloudscribeNG.Logging/tree/master/src/CloudscribeNG.Logging.EFCore.Common)) - the 3 libraries above all depend on this one.
	
- CloudscribeNG.Syndication ([NuGet](http://www.nuget.org/packages/CloudscribeNG.Syndication) | [GitHub](https://github.com/joeaudette/CloudscribeNG.Syndication)) - a re-useable RSS Feed generator for ASP.NET Core.
	
- CloudscribeNG.MetaWeblog ([NuGet](http://www.nuget.org/packages/CloudscribeNG.MetaWeblog) | [GitHub](https://github.com/joeaudette/CloudscribeNG.MetaWeblog)) - a re-useable implementation of the metaweblog api for ASP.NET Core.
	
- NoDb ([NuGet](http://www.nuget.org/packages/NoDb) | [GitHub](https://github.com/joeaudette/NoDb)) - a "no database" file system storage, not branded as "CloudscribeNG" but nevertheless an important library in the CloudscribeNG ecosystem.

## Dynamic Authorization Policies

[Github sample reference application](https://github.com/CloudscribeNG/sample-dynamic-authorization-policy)

	
- [CloudscribeNG.DynamicPolicy.Web.Mvc](https://www.nuget.org/packages/CloudscribeNG.DynamicPolicy.Web.Mvc/) - main package.
	
- [CloudscribeNG.DynamicPolicy.CoreIntegration](https://www.nuget.org/packages/CloudscribeNG.DynamicPolicy.CoreIntegration/) - integration with CloudscribeNG Core.
	
- [CloudscribeNG.DynamicPolicy.Web.Views.Bootstrap4](https://www.nuget.org/packages/CloudscribeNG.DynamicPolicy.Web.Views.Bootstrap4/)
	
- [CloudscribeNG.DynamicPolicy.Web.Views.Bootstrap5](https://www.nuget.org/packages/CloudscribeNG.DynamicPolicy.Web.Views.Bootstrap5/)
	
- [CloudscribeNG.DynamicPolicy.Models](https://www.nuget.org/packages/CloudscribeNG.DynamicPolicy.Models/)
	
- [CloudscribeNG.DynamicPolicy.Storage.NoDb](https://www.nuget.org/packages/CloudscribeNG.DynamicPolicy.Storage.NoDb/)
	
- [CloudscribeNG.DynamicPolicy.Storage.EFCore.Common](https://www.nuget.org/packages/CloudscribeNG.DynamicPolicy.Storage.EFCore.Common/)
	
- [CloudscribeNG.DynamicPolicy.Storage.EFCore.MSSQL](https://www.nuget.org/packages/CloudscribeNG.DynamicPolicy.Storage.EFCore.MSSQL/)
	
- [CloudscribeNG.DynamicPolicy.Storage.EFCore.MySql](https://www.nuget.org/packages/CloudscribeNG.DynamicPolicy.Storage.EFCore.MySql/)
	
- [CloudscribeNG.DynamicPolicy.Storage.EFCore.PostgreSql](https://www.nuget.org/packages/CloudscribeNG.DynamicPolicy.Storage.EFCore.PostgreSql/)
	
- [CloudscribeNG.DynamicPolicy.Storage.EFCore.SQLite](https://www.nuget.org/packages/CloudscribeNG.DynamicPolicy.Storage.EFCore.SQLite/)

## Libraries for Progressive Web Apps

	
- CloudscribeNG.PwaKit ([Nuget ](https://www.nuget.org/packages/CloudscribeNG.PwaKit/)| [GitHub](https://github.com/CloudscribeNG/pwakit/tree/master/src/CloudscribeNG.PwaKit) | [Documentation](https://www.CloudscribeNG.com/CloudscribeNG-pwakit)) - this main library has no dependencies on CloudscribeNG Core and could be used in any ASP.NET Core application.
	
- CloudscribeNG.PwaKit.Integration.CloudscribeNGCore ([NuGet ](https://www.nuget.org/packages/CloudscribeNG.PwaKit.Integration.CloudscribeNGCore/)| [GitHub](https://github.com/CloudscribeNG/pwakit/tree/master/src/CloudscribeNG.PwaKit.Integration.CloudscribeNGCore))
	
- CloudscribeNG.PwaKit.Integration.SimpleContent ([NuGet ](https://www.nuget.org/packages/CloudscribeNG.PwaKit.Integration.SimpleContent/)| [GitHub](https://github.com/CloudscribeNG/pwakit/tree/master/src/CloudscribeNG.PwaKit.Integration.SimpleContent))
	
- CloudscribeNG.PawKit.Storage.EFCore.Common ([NuGet ](https://www.nuget.org/packages/CloudscribeNG.PwaKit.Storage.EFCore.Common/)| [GitHub](https://github.com/CloudscribeNG/pwakit/tree/master/src/CloudscribeNG.PwaKit.Storage.EFCore.Common))
	
- CloudscribeNG.PwaKit.Storage.EFCore.MSSQL ([NuGet ](https://www.nuget.org/packages/CloudscribeNG.PwaKit.Storage.EFCore.MSSQL/)| [GitHub](https://github.com/CloudscribeNG/pwakit/tree/master/src/CloudscribeNG.PwaKit.Storage.EFCore.MSSQL))
	
- CloudscribeNG.PwaKit.Storage.EFCore.MySql ([NuGet ](https://www.nuget.org/packages/CloudscribeNG.PwaKit.Storage.EFCore.MySql/)| [GitHub](https://github.com/CloudscribeNG/pwakit/tree/master/src/CloudscribeNG.PwaKit.Storage.EFCore.MySql))
	
- CloudscribeNG.PwaKit.Storage.EFCore.PostgreSql ([NuGet ](https://www.nuget.org/packages/CloudscribeNG.PwaKit.Storage.EFCore.PostgreSql/)| [GitHub](https://github.com/CloudscribeNG/pwakit/tree/master/src/CloudscribeNG.PwaKit.Storage.EFCore.PostgreSql))
	
- CloudscribeNG.PwaKit.Storage.EFCore.SQLite ([NuGet ](https://www.nuget.org/packages/CloudscribeNG.PwaKit.Storage.EFCore.SQLite/)| [GitHub](https://github.com/CloudscribeNG/pwakit/tree/master/src/CloudscribeNG.PwaKit.Storage.EFCore.SQLite))
	
- CloudscribeNG.PwaKit.Storage.NoDb ([NuGet ](https://www.nuget.org/packages/CloudscribeNG.PwaKit.Storage.NoDb)| [GitHub](https://github.com/CloudscribeNG/pwakit/tree/master/src/CloudscribeNG.PwaKit.Storage.NoDb))
