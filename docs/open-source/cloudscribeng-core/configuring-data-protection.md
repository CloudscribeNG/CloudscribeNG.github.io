# Configuring Data Protection

In CloudscribeNG Core we are using encryption to secure a few items kept in the database including the SMTP password and the secrets for social authentication. Setting up the data protection keys is an important deployment consideration that you will typically want to handle when you first deploy your web application.

To encrypt these items we are using the [ASP.NET Core Data Protection API](https://docs.microsoft.com/en-us/aspnet/core/security/data-protection/). In order to be able to decrypt the data for use, **we need to have persistent keys**.  If the keys are lost or changed, then decryption will fail and we won't be able to use the encrypted settings. Since the data protection keys are also used to encrypt the authentication token for cookie authentication, persistent keys are needed in general. If you don't configure persistent keys then temporary keys will be used and these will be lost each time the application restarts and that would mean users would have to login again after each application startup because their old authentication token would no longer be valid.

The data protection api has a number of configurable options related to key storage. The approach used will typically depend on your hosting environment.

## IIS Hosting

The recommended solution if you are using traditional IIS Web Server hosting is to use this [Provision-AutoGenKeys.ps1](https://github.com/dotnet/aspnetcore/blob/main/src/DataProtection/Provision-AutoGenKeys.ps1) powershell script to provision a keyring for the application pool that will be used to persist the crypto keys. The way to use it is to run powershell as administrator and pass in the application pool name like this:

```bash
.\Provision-AutoGenKeys.ps1 your-app-pool-name
```

If you do that, then this one line of code in Startup.cs is all that is needed:

```cs
services.AddDataProtection();
```

When you build a project using our [Visual Studio template](https://marketplace.visualstudio.com/items?itemName=joeaudette.CloudscribeNGProjectTemplate), we wire this up for you in Config\DataProtection.cs

## Azure

For Azure hosting, you can store the keys in either blob storage or redis, [see the official documentation here](https://docs.microsoft.com/en-us/aspnet/core/security/data-protection/implementation/key-storage-providers#azure-and-redis).

## Persisting the Keys to the File System

It is possible to store the keys in the file system for example using code like this in Startup.cs (or Config\DataProtection.cs):

```cs
string pathToCryptoKeys = Path.Combine(Environment.ContentRootPath, "dp_keys");
services.AddDataProtection()
     .PersistKeysToFileSystem(new System.IO.DirectoryInfo(pathToCryptoKeys))
     .ProtectKeysWithCertificate("thumbprint");
```

To protect the keys at rest, you can use an X.509 certificate on any platform or other Windows specific mechanisms on Windows, see the [official documentation](https://docs.microsoft.com/en-us/aspnet/core/security/data-protection/implementation/key-encryption-at-rest#data-protection-implementation-key-encryption-at-rest) for how to set that up.
