# CloudscribeNG Commercial Product Libraries

CloudscribeNG commercial libraries are published as nuget packages just like [our open source libraries](/docs/complete-list-of-CloudscribeNG-libraries). You can find [all of our libraries on nuget.org](https://www.nuget.org/profiles/CloudscribeNG), the open source libraries have blue icons and the commercial libraries have green icons. The easiest way to get our products is by using our [Project Template for Visual Studio](https://marketplace.visualstudio.com/items?itemName=joeaudette.CloudscribeNGProjectTemplate), which can create an application configured with our open source and commercial libraries in various configurations based on the options you choose. We also have reference sample applications on github that show how to wire up the nuget packages in case you want to integrate our products with existing web applications. You can find links to the reference applications in the list below and in the product details listed in [our store](/store). All of our commercial products are released under our [commercial product license](https://www.CloudscribeNG.com/CommercialProductLicense.md), and an Enterprise licence is also available if you would like to obtain the source code to make further modifications for your own projects (please contact us for details). Please note that product licences are valid for each major release of CloudscribeNG, correlating with the .NET version, so a licence purchased for .NET 8 will need to be renewed after CloudscribeNG has upgraded to .NET 10.

Some of our commercial libraries can be used with any ASP.NET Core MVC application and do not require CloudscribeNG Core, but all of our commercial libraries work optimally when used with our open source libraries.

We put a lot of effort into all of our products both open source and commercial, but we depend on sales of our commercial products to support our continued work.

## Forms and Surveys

[Product Details](/products/CloudscribeNG-forms-and-surveys-solution)

[Github sample reference application](https://github.com/CloudscribeNG/sample-forms-surveys)

	
- [CloudscribeNG.Forms.Web](https://www.nuget.org/packages/CloudscribeNG.Forms.Web/) - the main library
	
- [CloudscribeNG.Forms.Bootstrap4](https://www.nuget.org/packages/CloudscribeNG.Forms.Bootstrap4/) - views for Bootstrap 4
	
- [CloudscribeNG.Forms.CoreIntegration](https://www.nuget.org/packages/CloudscribeNG.Forms.CoreIntegration/) - integration for CloudscribeNG Core
	
- [CloudscribeNG.Forms.ContentTemplate.Bootstrap4](https://www.nuget.org/packages/CloudscribeNG.Forms.ContentTemplate.Bootstrap4/) - integration to easily place forms on pages in CloudscribeNG SimpleContent
	
- [CloudscribeNG.Forms.Models](https://www.nuget.org/packages/CloudscribeNG.Forms.Models/)
	
- [CloudscribeNG.Forms.Data.NoDb](https://www.nuget.org/packages/CloudscribeNG.Forms.Data.NoDb/)
	
- [CloudscribeNG.Forms.Data.EFCore.Common](https://www.nuget.org/packages/CloudscribeNG.Forms.Data.EFCore.Common/)
	
- [CloudscribeNG.Forms.Data.EFCore.MSSQL](https://www.nuget.org/packages/CloudscribeNG.Forms.Data.EFCore.MSSQL/)
	
- [CloudscribeNG.Forms.Data.EFCore.MySql](https://www.nuget.org/packages/CloudscribeNG.Forms.Data.EFCore.MySql/)
	
- [CloudscribeNG.Forms.Data.EFCore.PostgreSql](https://www.nuget.org/packages/CloudscribeNG.Forms.Data.EFCore.PostgreSql/)
	
- [CloudscribeNG.Forms.Data.EFCore.SQLite](https://www.nuget.org/packages/CloudscribeNG.Forms.Data.EFCore.SQLite/)

## TalkAbout Comment System

[Product Details](/products/CloudscribeNG-talkabout-comment-system)

## TalkAbout Forums 

[Product Details](/products/CloudscribeNG-talkabout-forums)

## Newsletter

[Product Details](/products/CloudscribeNG-newsletter-solution)

[Github sample reference application](https://github.com/CloudscribeNG/sample-newsletter)

	
- [CloudscribeNG.EmailList.Web.Mvc](https://www.nuget.org/packages/CloudscribeNG.EmailList.Web.Mvc/) - the main library
	
- [CloudscribeNG.EmailList.Views.Bootstrap4](https://www.nuget.org/packages/CloudscribeNG.EmailList.Views.Bootstrap4/) - views for Bootstrap 4
	
- [CloudscribeNG.EmailList.CoreIntegration](https://www.nuget.org/packages/CloudscribeNG.EmailList.CoreIntegration/) - integration for CloudscribeNG Core
	
- [CloudscribeNG.EmailList.KvpUserProperties.Integration](https://www.nuget.org/packages/CloudscribeNG.EmailList.KvpUserProperties.Integration/) - integration to include newsletter sign up on registration and user profile and management pages in CloudscribeNG Core
	
- [CloudscribeNG.EmailList.Models](https://www.nuget.org/packages/CloudscribeNG.EmailList.Models/)
	
- [CloudscribeNG.EmailList.Data.EFCore.Common](https://www.nuget.org/packages/CloudscribeNG.EmailList.Data.EFCore.Common/)
	
- [CloudscribeNG.EmailList.Data,EFCore.MSSQL](https://www.nuget.org/packages/CloudscribeNG.EmailList.Data.EFCore.MSSQL/)
	
- [CloudscribeNG.EmailList.Data.EFCore.MySql](https://www.nuget.org/packages/CloudscribeNG.EmailList.Data.EFCore.MySql/)
	
- [CloudscribeNG.EmailList.Data.EFCore.PostgreSql](https://www.nuget.org/packages/CloudscribeNG.EmailList.Data.EFCore.PostgreSql/)
	
- also depends on CloudscribeNG Email Templating and CloudscribeNG Email Queue, see below

## Membership Paywall

[Product Details](/products/CloudscribeNG-membership-paywall)

[Github sample reference application](https://github.com/CloudscribeNG/sample-membership-paywall)

	
- [CloudscribeNG.Membership.Web.Mvc](https://www.nuget.org/packages/CloudscribeNG.Membership.Web.Mvc/) - main library
	
- [CloudscribeNG.Membership.Views.Bootstrap4](https://www.nuget.org/packages/CloudscribeNG.Membership.Views.Bootstrap4/) - views for Bootstrap 4
	
- [CloudscribeNG.Membership.Models](https://www.nuget.org/packages/CloudscribeNG.Membership.Models/)
	
- [CloudscribeNG.Membership.Data.EFCore.Common](https://www.nuget.org/packages/CloudscribeNG.Membership.Data.EFCore.Common/)
	
- [CloudscribeNG.Membership.Data.EFCore.MSSQL](https://www.nuget.org/packages/CloudscribeNG.Membership.Data.EFCore.MSSQL/)
	
- [CloudscribeNG.Membership.Data.EFCore.MySql](https://www.nuget.org/packages/CloudscribeNG.Membership.Data.EFCore.MySql/)
	
- [CloudscribeNG.Membership.Data.EFCore.PostgreSql](https://www.nuget.org/packages/CloudscribeNG.Membership.Data.EFCore.PostgreSql/)
	
- [CloudscribeNG.Membership.HangfireIntegration](https://www.nuget.org/packages/CloudscribeNG.Membership.HangfireIntegration/) - for processing background tasks such as removing users from granted roles when membership expires
	
- [CloudscribeNG.Membership.StripeIntegration](https://www.nuget.org/packages/CloudscribeNG.Membership.StripeIntegration/) - integration with CloudscribeNG.StripeIntegration, see below
	
- [CloudscribeNG.Membership.StripeIntegration.Bootstrap4](https://www.nuget.org/packages/CloudscribeNG.Membership.StripeIntegration.Bootstrap4/)
	
- also depends on CloudscribeNG StripeIntegration and CloudscribeNG Email.Templating, and CloudscribeNG Email Queue, see below

## Email Templating

This is not a standalone product, it is used for email templates in the newsletter solution and for membership renewal reminders in the paywall solution

	
- [CloudscribeNG.EmailTemplating.Web](https://www.nuget.org/packages/CloudscribeNG.Email.Templating.Web/) - main package
	
- [CloudscribeNG.EmailTemplating.Services](https://www.nuget.org/packages/CloudscribeNG.Email.Templating.Services/)
	
- [CloudscribeNG.EmailTemplating.Models](https://www.nuget.org/packages/CloudscribeNG.Email.Templating.Models/)
	
- [CloudscribeNG.EmailTemplating.Data.NoDb](https://www.nuget.org/packages/CloudscribeNG.Email.Templating.Data.NoDb/)
	
- [CloudscribeNG.EmailTemplating.Data.EFCore.Common](https://www.nuget.org/packages/CloudscribeNG.Email.Templating.Data.EFCore.Common/)
	
- [CloudscribeNG.EmailTemplating.Data.EFCore.MSSQL](https://www.nuget.org/packages/CloudscribeNG.Email.Templating.Data.EFCore.MSSQL/)
	
- [CloudscribeNG.EmailTemplating.Data.EFCore.MySql](https://www.nuget.org/packages/CloudscribeNG.Email.Templating.Data.EFCore.MySql/)
	
- [CloudscribeNG.EmailTemplating.Data.EFCore.PostgreSql](https://www.nuget.org/packages/CloudscribeNG.Email.Templating.Data.EFCore.PostgreSql/)
	
- [CloudscribeNG.EmailTemplating.Data.EFCore.SQLite](https://www.nuget.org/packages/CloudscribeNG.Email.Templating.Data.EFCore.SQLite/)

## Email Queue

This is not a standalone product, it is used in the newsletter solution and in the paywall solution

	
- [CloudscribeNG.EmailQueue.Services](https://www.nuget.org/packages/CloudscribeNG.EmailQueue.Services/) - main library
	
- [CloudscribeNG.EmailQueue.CoreIntegration](https://www.nuget.org/packages/CloudscribeNG.EmailQueue.CoreIntegration/) - integration with CloudscribeNG Core
	
- [CloudscribeNG.EmailQueue.HangfireIntegration](https://www.nuget.org/packages/CloudscribeNG.EmailQueue.HangfireIntegration/) - used for processing the email queue on a background task
	
- [CloudscribeNG.EmailQueue.Models](https://www.nuget.org/packages/CloudscribeNG.EmailQueue.Models/)
	
- [CloudscribeNG.EmailQueue.Data.EFCore.Common](https://www.nuget.org/packages/CloudscribeNG.EmailQueue.Data.EFCore.Common/)
	
- [CloudscribeNG.EmailQueue.Data.EFCore.MSSQL](https://www.nuget.org/packages/CloudscribeNG.EmailQueue.Data.EFCore.MSSQL/)
	
- [CloudscribeNG.EmailQueue.Data.EFCore.MySql](https://www.nuget.org/packages/CloudscribeNG.EmailQueue.Data.EFCore.MySql/)
	
- [CloudscribeNG.EmailQueue.Data.EFCore.PostgreSql](https://www.nuget.org/packages/CloudscribeNG.EmailQueue.Data.EFCore.PostgreSql/)

## Stripe Integration

This is not a standalone product, it is used in the paywall solution and will be used in other ecommerce solutions in the future

	
- [CloudscribeNG.StripeIntegration.Mvc](https://www.nuget.org/packages/CloudscribeNG.StripeIntegration.Mvc/) - main library
	
- [CloudscribeNG.Core.StripeIntegration](https://www.nuget.org/packages/CloudscribeNG.Core.StripeIntegration/) - integration for CloudscribeNG Core
	
- [CloudscribeNG.StripeIntegration.Mvc.Bootstrap4](https://www.nuget.org/packages/CloudscribeNG.StripeIntegration.Mvc.Bootstrap4/)
	
- [CloudscribeNG.StripeIntegration.Models](https://www.nuget.org/packages/CloudscribeNG.StripeIntegration.Models/)
	
- [CloudscribeNG.StripeIntegration.Data.NoDb](https://www.nuget.org/packages/CloudscribeNG.StripeIntegration.Data.NoDb/)
	
- [CloudscribeNG.StripeIntegration.Data.EFCore.Common](https://www.nuget.org/packages/CloudscribeNG.StripeIntegration.Data.EFCore.Common/)
	
- [CloudscribeNG.StripeIntegration.Data.EFCore.MSSQL](https://www.nuget.org/packages/CloudscribeNG.StripeIntegration.Data.EFCore.MSSQL/)
	
- [CloudscribeNG.StripeIntegration.Data.EFCore.MySql](https://www.nuget.org/packages/CloudscribeNG.StripeIntegration.Data.EFCore.MySql/)
	
- [CloudscribeNG.StripeIntegration.Data.EFCore.PostgreSql](https://www.nuget.org/packages/CloudscribeNG.StripeIntegration.Data.EFCore.PostgreSql/)
	
- [CloudscribeNG.StripeIntegration.Data.EFCore.SQLite](https://www.nuget.org/packages/CloudscribeNG.StripeIntegration.Data.EFCore.SQLite/)

# Trial Mode Limitations

Our commercial libraries are free to try, but typically have alert messages shown every few page requests or other limitations that can be removed by installing a license activation file. Activation files are sold per host name, so you need to purchase a separate activation file for each host name where you use the product in order to remove the trial mode limitations.

![screen shot of a trial mode alert message](/media/images/product-alert.jpg)

Clicking the link in the alert will take you to the CloudscribeNG store passing a token for the product and the current host name so that you can create a license activation and purchase the needed file to remove the alert or other product limitation.

You should get the product working locally, then deploy to your production environment before purchasing the activation file since the activation file is always for a specific host name. You generally should not purchase an activation file for localhost unless you don't mind buying a license just to get rid of the alerts in your development environment.

## Installing the activation file

The activation file for each product and host name must be installed to remove the trial mode alerts and limitations. You need to create a folder named LicenseFiles in the root of your web application. You download the activation file and put it in that folder, or if you need to purchase activation files for more than one host name, you can organize them into sub folders corresponding to the host name. For example if you site is running at www.somedomain.com then you would create the folder structure /LicenseFiles/www.somedomain.com/ and put the activation file for that host name in the folder named the same as the host name. This is useful if you want to purchase activation files for different environments that use different host names and also useful if you are using host name based multi-tenancy. It is a good idea to check these files into your source code repository along with your application code.
