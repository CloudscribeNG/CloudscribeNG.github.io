# CloudscribeNG.SimpleContent

CloudscribeNG SimpleContent is a user-friendly open source blog and content engine that allows you to create pages and blog posts right from the web browser using a simple editor, content templates, or the [Open Live Writer](/docs/using-open-live-writer) desktop application. The Docs and Blog sections of this site are powered by CloudscribeNG SimpleContent.

CloudscribeNG SimpleContent is available on NuGet, see the SimpleContent section in our [complete list of CloudscribeNG libraries](https://www.CloudscribeNG.com/docs/complete-list-of-CloudscribeNG-libraries). Source code is available on [GitHub](https://github.com/joeaudette/CloudscribeNG.SimpleContent).

## Features

	
- Create and edit pages and blog posts right from the web browser or using [Open Live Writer](/docs/using-open-live-writer)
	
- Built in image browser, uploader, cropper, with configurable automatic resizing, and even drag/drop images right into the editor
	
- Built-in Page Manager - for easy drag/drop arrangement of the page hierarchy
	
- For technical articles includes built in syntax highlighter using the [CodeSnippet plugin in CKEditor](/docs/customizing-the-editor)
	
- Pages can be protected by roles for private or premium content
	
- Schedule posts and pages to be published on a future date
	
- [Content templates](/content-templates) for simple editing and consistent output
	
- Supports blog urls with or without date segments
	
- Optional internal comment system for the blog. Built in support for Disqus and not difficult to integrate some other comment system
	
- Blog RSS feed built in at /api/rss
	
- [Google Site Map](https://www.CloudscribeNG.com/docs/easy-google-sitemaps) built in at /api/sitemap
	
- Responsive [theming support](/docs/themes-and-web-design) based on Bootstrap
	
- Uses HTML 5 microdata to add semantic meaning and improve SEO
	
- Cross-platform, runs on ASP.NET Core which works on Windows, Mac, and Linux
	
- No database required - can use json for pages and can use json or xml for blog posts via [NoDb](https://github.com/joeaudette/NoDb). The XML format is the same as [MiniBlog](https://github.com/madskristensen/MiniBlog) and BlogEngine.NET
	
- You can optionally use a database - it currently supports MS SQL Server, PostgresSQL, and MySQL using Entity Framework Core
	
- Can use either [CloudscribeNG Core](https://www.CloudscribeNG.com/docs/CloudscribeNG-core) or [CloudscribeNG SimpleAuth](https://github.com/joeaudette/CloudscribeNG.Web.SimpleAuth) for user accounts (though we recommend using CloudscribeNG Core even for small sites)
	
- Supports [multiple tenants](https://www.CloudscribeNG.com/docs/multi-tenant-support) via integration using CloudscribeNG Core
	
- [Supports localization](https://www.CloudscribeNG.com/docs/localization)
	
- Can [manage content in multiple languages](/manage-content-in-multiple-languages)

## Getting Started

See the [Introduction](https://www.CloudscribeNG.com/docs/introduction) to learn about our Visual Studio plugin and "dotnet new" project templates. This is the best way to start new projects with CloudscribeNG SimpleContent. The Starter Kits discussed below were the first way we came up with to help people get started but our new project templates are now the best way to get started. Using the project templates you can generate projects with configurations that correspond to the starter kits but with even more options such as including our SimpleContactForm, and custom Registration page fields using key/value storage.

You can also integrate CloudscribeNG components into your existing solutions, and just use the project created by the project template as a reference of how to wire up the needed components in the Startup.cs and Program.cs of your application.  

## Publishing

Visual Studio provides a way to publish to the file system, which you would use for producing a set of files that can be uploaded to traditional [IIS web hosting](https://docs.microsoft.com/en-us/aspnet/core/publishing/iis), or [linux hosting](https://docs.microsoft.com/en-us/aspnet/core/publishing/linuxproduction), or you can publish to [Microsoft Azure](https://docs.microsoft.com/en-us/aspnet/core/tutorials/publish-to-azure-webapp-using-vs), or even to [Docker containers](https://docs.microsoft.com/en-us/aspnet/core/publishing/docker). 

## Learn More
