# Introduction

CloudscribeNG™ is a loosely-coupled collection of libraries for building web sites and applications on ASP.NET Core. The libraries are packaged as NuGet packages and available on NuGet.org and the source code for the open source libraries is [available on GitHub.com](https://github.com/CloudscribeNG). As discussed below we also provide project templates for Visual Studio, and for dotnet new command line, letting you pick and choose the features you want: the template will generate a web application that wires up the needed nuget packages for you, creating a complete working application that you can publish as is or customize with your own code. With the default options you get a fully working user-friendly content management system. The big advantage of generating your own project (instead of us making CloudscribeNG a downloadable compiled app) is that by having full control of the main web app you can easily add any code you want or integrate with other libraries: there are no barriers to customization.

There are 2 groups of libraries that correspond to the "big" features, [CloudscribeNG Core](/docs/CloudscribeNG-core) (which provides multi-tenant or single-tenant web application foundation with management for user, roles, claims and more) and [CloudscribeNG SimpleContent](/docs/CloudscribeNG-simplecontent) (a user-friendly content and blogging engine); then there are a number of other libraries that provide various smaller functionality that you may find useful in your projects even if you are not using the "big" CloudscribeNG features. See the [Complete list of open source CloudscribeNG libraries](/docs/complete-list-of-CloudscribeNG-libraries) to get an idea of the breadth and scope of what CloudscribeNG provides for you.

CloudscribeNG is not a framework *per se*, it is a set of ready-built functionality for common things needed by most websites or applications. It is very extensible and customizable.  Most things that you might want to customize or change are implemented as interfaces so that you can easily inject your own custom implementations for various things, and some things are configurable by application settings. If you find something that you want to change but don't know how or can't find the information you need on how to change it, you can always ask questions in our [community forum](https://www.CloudscribeNG.com/forum). We are open to introducing more interfaces where needed if there is demand to customize things that are not currently easy to change, so it never hurts to ask.

See also our [list of commercial libraries](/docs/complete-list-of-CloudscribeNG-commercial-product-libraries) that can provide additional features and functionality that you may find helpful.

### Why use CloudscribeNG?

When you start a new ASP.NET Core web project in Visual Studio using the built in project templates, at most you get a starter site that provides functionality to register and login, but it doesn't provide anything for managing users and role memberships and provides no way to make a user with administrative permissions. It also doesn't give you any way to edit content. You would have to build all of that for yourself. When you start a new project with CloudscribeNG, all of that and much more is built already and provided for you, so you can immediately get working on the main features of your project.

## Getting Started

### Prerequisites

You can use CloudscribeNG to develop web applications on Windows, Linux, or MacOS using the latest [.NET Core SDK](https://www.microsoft.com/net/download/core). You can use the [.NET Core command line interface (CLI)](https://docs.microsoft.com/en-us/dotnet/core/tools/?tabs=netcore2x) to build, run, and publish your own projects. You can write code using your favorite text editor such as [Visual Studio Code](https://code.visualstudio.com/) which is available free on all 3 operating systems. You can also use the full blown [Visual Studio IDE](https://www.visualstudio.com/vs/) on Windows or MacOS. On Windows you need Visual Studio 2019. I have not tried Visual Studio for Mac myself so let me know how it goes if you try it.

### Starting New Projects in Visual Studio

We've made it very easy to get started building new projects with CloudscribeNG by providing an extension for Visual Studio 2019 (not yet tested on Visual Studio for Mac). You can [download the CloudscribeNG Project Template extension free from the Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=joeaudette.CloudscribeNGProjectTemplate). Close all instances of Visual Studio and double click the download to install. Then when you open Visual Studio you will see a new CloudscribeNG project template under the C# Web section.

[![ ](/media/images/CloudscribeNG-vsix-1-ws.jpg)](/media/images/CloudscribeNG-vsix-1.jpg)

When you create a new project a dialog window will appear to allow you to choose which data storage platform you wish to use and which CloudscribeNG features to include. Currently we have support for Microsoft SqlServer, MySql, PostgreSql, SQLite, and NoDb "no database" file system storage. Every project will use [CloudscribeNG Core](https://www.CloudscribeNG.com/docs/CloudscribeNG-core), and you can optionally also include [CloudscribeNG SimpleContent](https://www.CloudscribeNG.com/docs/CloudscribeNG-simplecontent) (a user friendly blog and content engine), CloudscribeNG Logging which logs to data storage and provides a UI for viewing the log, CloudscribeNG Simple Contact Form, [Custom Registration with key/value pair storage](https://www.CloudscribeNG.com/docs/configuration-based-custom-registration-and-user-properties), and [integration with IdentityServer4](https://www.CloudscribeNG.com/docs/identityserver-integration).  There is also an option to include a basic setup for working with React, together with a couple of demo React applications implemented in TypeScript (tsx) and built via Webpack - but be aware that option requires you to have installed a modern version of [Node.js and npm](https://nodejs.org/en/download/). 

![ ](/media/images/image_2025-03-07t14-03-19-8-ws.png)

There is also a youtube demo of project creation with an earlier version of our Visual Studio project template.

### Starting New Projects with the .NET CLI (command line interface)

We also have a project template that works with the dotnet new command using the .NET Core CLI, making it very easy to start new projects from the command line. To install the template (NuGet package) open a command window or powershell window and enter the following command:

```bash
dotnet new -i "CloudscribeNG.templates::*"
```

Note the above command is also used to upgrade the template to the latest version, so it is good to run this command frequently.

If you later want to uninstall the project template, you can use the command:

```bash
dotnet new -u CloudscribeNG
```

Once you have the CloudscribeNG project template installed you should enter the following command to see the available parameters that you can pass to the template in order to choose which data storage platform to use and which features to install:

```bash
PS D:\_scratch> dotnet new CloudscribeNG --help                                                                           Usage: new [options]

Options:
  -h, --help          Displays help for this command.
  -l, --list          Lists templates containing the specified name. If no name is specified, lists all templates.
  -n, --name          The name for the output being created. If no name is specified, the name of the current directory is used.
  -o, --output        Location to place the generated output.
  -i, --install       Installs a source or a template pack.
  -u, --uninstall     Uninstalls a source or a template pack.
  --nuget-source      Specifies a NuGet source to use during install.
  --type              Filters templates based on available types. Predefined values are "project", "item" or "other".
  --dry-run           Displays a summary of what would happen if the given command line were run if it would result in a template creation.
  --force             Forces content to be generated even if it would change existing files.
  -lang, --language   Filters templates based on language and specifies the language of the template to create.

CloudscribeNG web project template (C#)
Author: Joe Audette
Options:                                                                                                                
  -M|--MultiTenantMode        Multi-tenancy configuration.                                                              
                                  FolderName    - There is a root tenant, and other tenants can be added starting at a folder segment.
                                  HostName      - You will be able to create additional sites by specifying a host name. Additional tenants require DNS and web server settings.
                                  None          - A single tenant installation. This can be changed later by configuration.
                              Default: FolderName                                                                       

  -S|--SimpleContentConfig    SimpleContent configuration.                                                              
                                  a    - Pages and Bog with Pages as default route                                      
                                  b    - Pages and Blog with Home Controller as default route                           
                                  c    - Blog ONLY with Blog as default route                                           
                                  d    - Blog ONLY with Home Controller as default route                                
                                  z    - Not installed, SimpleContent will not be included                              
                              Default: a                                                                                

  -N|--NonRootPagesSegment    If you are using SimpleContent pages with SimpleContentConfig option b, this will be used as the url segment for the pages to start at.
                              string - Optional                                                                         
                              Default: p                                                                                

  -No|--NonRootPagesTitle     If you are using SimpleContent pages with SimpleContentConfig option b, this will be used as the title for the pages top level menu item.
                              string - Optional                                                                         
                              Default: Articles                                                                         

  -C|--ContactForm            Include CloudscribeNG SimpleContactForm.                                                    
                              bool - Optional                                                                           
                              Default: false / (*) true                                                                 

  -K|--KvpCustomRegistration  Include CloudscribeNG key/value pair custom registration.                                   
                              bool - Optional                                                                           
                              Default: false / (*) true                                                                 

  -I|--IdentityServer         Include IdentityServer4 integration.                                                      
                              bool - Optional                                                                           
                              Default: false / (*) true                                                                 

  -Q|--QueryTool              Include Query Tool.                                                                                   
                              bool - Optional                                                                                       
                              Default: false    

  -L|--Logging                Include CloudscribeNG logging and log viewer UI.                                            
                              bool - Optional                                                                           
                              Default: true                                                                             

  -F|--FormBuilder            Include CloudscribeNG Forms and Surveys add on product.                                     
                              bool - Optional                                                                           
                              Default: false / (*) true                                                                 

  -P|--Paywall                Include CloudscribeNG Membership Paywall add on product. Cannot be used with NoDb or SQLite.
                              bool - Optional                                                                           
                              Default: false / (*) true                                                                 

  -Ne|--Newsletter            Include CloudscribeNG Newsletter/Email List add on product. Cannot be used with NoDb or SQLite.
                              bool - Optional                                                                           
                              Default: false / (*) true                                                                 

  -Co|--CommentSystem         Include CloudscribeNG TalkAbout GDPR friendly comment system add on product.                
                              bool - Optional                                                                           
                              Default: false / (*) true                                                                 

  -Fo|--Forum                 Include CloudscribeNG TalkAbout GDPR friendly forum add on product.                         
                              bool - Optional                                                                           
                              Default: false / (*) true                                                                 

  -D|--DynamicPolicy         Include CloudscribeNG Dynamic Authorization Policies.                                       
                              bool - Optional                                                                           
                              Default: true                                                                             

  -Da|--DataStorage            The data storage platform you wish to use.                                                
                                  NoDb      - A no-database file system storage                                         
                                  SQLite    - SQLite storage using Entity Framework Core                                
                                  MSSQL     - Microsoft SqlServer storage using Entity Framework Core                   
                                  pgsql     - PostgreSql storage using Entity Framework Core                            
                                  MySql     - MySql storage using Entity Framework Core
                                  AllStorage- All database storage types (for developing new CloudscribeNG modules)
                              Default: NoDb                                                                             
                                                              
  -R|--React                  Include a react sample client app, plus Webpack to build it                            
                              bool - Optional                                                                           
                              Default: false                                                                             

  -H|--HttpsPort              Port number to use to configure SSL in launchSettings.json.                               
                              integer - Optional                                                                        

  -Ke|--KestrelPort           Port number to use to configure Kestrel in launchSettings.json.                           
                              integer - Optional                                                                        

  -II|--IISExpressPort        Port number to use to configure IIS Express in launchSettings.json.                       
                              integer - Optional                                                                        

* Indicates the value used if the switch is provided without a value.
PS D:\_scratch>  
```

So, for example to use all the available features with PostgreSql you would enter the commands:

```bash
mkdir yourprojectname
cd yourprojectname

dotnet new CloudscribeNG -Da pgsql -S a -C true -K true -I true -L true -R true
```

The first 2 commands are just creating a folder for your project and then moving into the folder. The folder name will be used as the project name. Note that while my example is explicitly passing in all the parameter values, you can leave out parameters if you are using the default values. You can also optionally pass in some parameters that are used for the kestrel and IIS web ports but I recommend leave those out so they will be randomly set to valid values.

Note that the template creates a readme.html file that is customized with information depending on which features and configurations you use when you create your project. This readme.html file is opened automatically if you use the Visual Studio project template but it doesn't open automatically if you use the command line so you should open it manually in that case. It has information about the default login credentials and any additional configuration that may be needed for the features you have included in your project.

Notes regarding databases: In general you should choose which database type you are going to use and specify that when you create your new CloudscribeNG project. The AllStorage database option is primarily for developers wishing to build new CloudscribeNG modules. It gives you a project where all of the database types are wired up and you choose which you are going to use and test via config in appsettings.json. You can switch between the different types in order to create your database migrations for each database type that your new module might need. 

## Publishing

Visual Studio provides a way to publish to the file system which you would use for producing a set of files that could be uploaded to more traditional [IIS web hosting](https://docs.microsoft.com/en-us/aspnet/core/publishing/iis), or [linux hosting](https://docs.microsoft.com/en-us/aspnet/core/publishing/linuxproduction), or you can publish to [Microsoft Azure](https://docs.microsoft.com/en-us/aspnet/core/tutorials/publish-to-azure-webapp-using-vs), or even to [Docker containers](https://docs.microsoft.com/en-us/aspnet/core/publishing/docker). 

You can also [publish from the command line using the .NET Core CLI](https://docs.microsoft.com/en-us/dotnet/core/tools/dotnet-publish?tabs=netcore2x).

For some of the more advanced scenarios it can be useful to see working demos that are already populated with data. For example we have a sample with IdentityServer4 integration and several working client applications that authenticate with IdentityServer4 using JWT authentication to protect the web apis. You may find that helpful to compare how it is setup if you have any trouble getting your own client applications working.

## Learn More Starting at These Main Topics

	
- [CloudscribeNG.Core](/docs/CloudscribeNG-core)
	
- [CloudscribeNG.SimpleContent](/docs/CloudscribeNG-simplecontent)
	
- [CloudscribeNG.Web.Common](/docs/CloudscribeNG-web-common)
	
- [CloudscribeNG.Web.Navigation](/docs/CloudscribeNG-web-navigation)
	
- [Themes and Web Design](/docs/themes-and-web-design)
	
- [Localization](https://www.CloudscribeNG.com/docs/localization)
