# Migrating Users with other Password Hash formats

CloudscribeNG is built on [ASP.NET Core Identity](https://docs.microsoft.com/en-us/aspnet/core/security/authentication/identity?view=aspnetcore-2.2&tabs=visual-studio) and uses the password hashing provided by ASP.NET Core Identity library.

Frequently we get requests for advice on how to migrate users from the older ASP.NET Membership system into the new Identity system. The newer Identity system uses a newer stronger hashing than the older membership system so the passwords are not directly compatible. There exists [a community project for this](https://github.com/abomadi/ids.core.membership.plugin) that has been used to migrate from DotNetNuke. However that is fairly complicated in that it implements a FallbackAccountService that subclasses the CloudscribeNG AccountService. More recently a new interface has been added to CloudscribeNG, [IFallbackPasswordHashValidator](https://github.com/CloudscribeNG/CloudscribeNG/blob/master/src/CloudscribeNG.Core.Identity/IFallbackPasswordHashValidator.cs), so to migrate from membership today you could implement that interface and inject it which seems less drastic than subclassing/overriding the AccountService. Maybe someone will do that and package it up to share.

The main thing when implementing this interface is that if you successfully validate the password with your fallback hash validator then you should return PasswordVerificationResult.SuccessRehashNeeded so that the user's password will be updated to the new hash format.

In one of our recent projects we needed to migrate users from mojoPortal which used the older Membership system but it had custom hashing using sha512 so it was not the standard Membership provider. We implemented the IFallbackPasswordHashValidator using the mojoPortal hashing, and have used it successfully in our client project. The source code [is here](https://github.com/CloudscribeNG/CloudscribeNG/tree/master/src/CloudscribeNG.MigrationHelper.mojoPortal), and it is [available on nuget.org](https://www.nuget.org/packages/CloudscribeNG.MigrationHelper.mojoPortal/).

The users from mojoportal have to be migrated into the CloudscribeNG.cs_User table from the mojoportal mp_Users table using sql. You should populate the CloudscribeNG PasswordHash field in the format Pwd|PasswordSalt where Pwd and PasswordSalt come from the mojoportal mp_Users table. Note that this also only works if your mojoportal site was configured to use hashed passwords.

If you migrate data from mojoportal to CloudscribeNG as described above and you want to use our implementation just [install the nuget](https://www.nuget.org/packages/CloudscribeNG.MigrationHelper.mojoPortal/), and then add this in your Startup.cs code:

```cs
services.AddMojoPortalPasswordMigration();
```

Now when users login to your new CloudscribeNG web application they can use the same password they used in your previous mojoPortal web application. On the first successful login the password hash will be updated to the newer hashing used in ASP.NET Core Identity.
