# Adding Tables and Data for Custom Features

This question came up in [our gitter chat room](https://gitter.im/joeaudette/CloudscribeNG), thanks to Troy aka @bbqchickenrobot:

> 

"say I have some tables with my own data and they use entity framework migrations…. would this cause a problem if I run the migration updates from within the project?"

Since CloudscribeNG components are shipped as NuGet packages, not as a stand alone application, you own the main web application code. We do provide a project template for the command line or for Visual Studio as discussed in the [Introduction article](/docs/introduction), and that generates the main web application for you and wires up the CloudscribeNG NuGet packages for you. But after that the application code is yours and you can easily integrate your own code to wire up your own Entity Framework DbContext classes and to run migrations and seed data if needed.

If you look at the generated web application code you can see how we wire up our DbContext classes and how we run migrations for CloudscribeNG libraries, and you can use the same patterns for your own custom components.

You wire up your DbContext classes with dependency injection in the ConfigureServices method of Startup.cs. Below is example code showing how we setup several CloudscribeNG components that use Entity Framework:

```cs
var connectionString = Configuration.GetConnectionString("EntityFrameworkConnectionString");
services.AddCloudscribeNGCoreEFStorageMSSQL(connectionString);
services.AddCloudscribeNGLoggingEFStorageMSSQL(connectionString);
services.AddCloudscribeNGKvpEFStorageMSSQL(connectionString);
```

Note that we have created extension methods on IServiceCollection to encapsulate the code that adds the DbContext classes to DI (Dependency Injection). Generally, DbContext classes are registered as "Scoped" dependencies, which means they will be created once per web request and will be disposed automatically for us at the end of the web request.

The code to execute migrations, goes in Program.cs, though generally you should encapsulate that code into methods in other classes, the methods should be invoked from Program.cs as shown below:

```cs
public class Program
{
	public static void Main(string[] args)
	{
	    var host = BuildWebHost(args);
		
	    using (var scope = host.Services.CreateScope())
	    {
		    var services = scope.ServiceProvider;
			
		    try
		    {
			    EnsureDataStorageIsReady(services);

		    }
		    catch (Exception ex)
		    {
			    var logger = services.GetRequiredService<ILogger<Program>>();
			    logger.LogError(ex, "An error occurred while migrating the database.");
		    }
	    }

	    host.Run();
    }

    public static IWebHost BuildWebHost(string[] args) =>
		WebHost.CreateDefaultBuilder(args)
			.UseStartup<Startup>()
			.Build();

    private static void EnsureDataStorageIsReady(IServiceProvider services)
    {
	    CoreEFStartup.InitializeDatabaseAsync(services).Wait();

	    LoggingEFStartup.InitializeDatabaseAsync(services).Wait();

	    SimpleContentEFStartup.InitializeDatabaseAsync(services).Wait();
			
    }
}
```

As mentioned before, DbContext classes generally are wired up with "Scoped" lifetime, since there is no specific web request in the context of Program.cs where the application is just being launched, we have to get the IServiceProvider, then create a Scope manually, then use the Scope to get a scoped version of IServiceProvider, and that can be passed into your own static method where you can run migrations and seed data if needed. The Scoped IServiceProvider is needed to get the DbContext because the DbContext is registered with a Scoped lifetime.

We have 3 such static methods shown which are invoked from within the EnsureDataStorageIsReady method. You can create your own similar static methods for your custom features and then add another line in the EnsureDataStorageIsReady method to invoke your static method. So for example lets consider the line for CoreEFStartup.InitializeDatabaseAsync(services).Wait(); That is an async static method that looks like this:

```cs
using CloudscribeNG.Core.Models;
using CloudscribeNG.Core.Models.Geography;
using CloudscribeNG.Core.Storage.EFCore.Common;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Options;
using System;
using System.Threading.Tasks;

namespace Microsoft.AspNetCore.Hosting // so it will show up in Program.cs without adding a using
{
    public static class CoreEFStartup
    {

        public static async Task InitializeDatabaseAsync(IServiceProvider serviceProvider)
        {
            var siteConfigAccessor = serviceProvider.GetService<IOptions<SiteConfigOptions>>();
            var db = serviceProvider.GetService<ICoreDbContext>();
            await db.Database.MigrateAsync();
            await EnsureData(db, siteConfigAccessor.Value);

        }

        private static async Task EnsureData(
            ICoreDbContext db,
            SiteConfigOptions config
            )
        {
            int rowsAffected = 0;
            
            int count = await db.Countries.CountAsync<GeoCountry>();
            if(count == 0)
            {
                foreach(GeoCountry c in InitialData.BuildCountryList())
                {
                    db.Countries.Add(c);
                }

                rowsAffected = await db.SaveChangesAsync();
            }
            // ... more code omitted for brevity
                 
        }
    }
}
```

You can see that InitializeDatabaseAsync uses the passed in Scoped IServiceProvider to get the DbContext then it runs the migrations then it invokes another method "EnsureData" which tests if the initial data already exists or not and if not it seeds that databases with the initial data.

So for custom data, you would follow the same pattern and create a static async method similar to the one shown above, and invoke it from Program.cs passing in the scoped IServiceProvider.
