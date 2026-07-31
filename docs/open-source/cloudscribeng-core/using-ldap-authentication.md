# Using LDAP Authentication

CloudscribeNG Core provides an interface [ILdapHelper](https://github.com/CloudscribeNG/CloudscribeNG/blob/master/src/CloudscribeNG.Core.Models/Identity/ILdapHelper.cs), and a default implementation that is "Not Implemented".

We also have a [working implementation](https://github.com/CloudscribeNG/CloudscribeNG/blob/master/src/CloudscribeNG.Core.Ldap/LdapHelper.cs) and if you inject the working implementation then new settings will appear for LDAP under Administration > Security Settings.

LDAP Authentication can be used with Active Directory and Open LDAP based servers (such as RedHat's 389 Directory Server) so long as your web server can communicate over the network with an Active Directory domain controller or a suitable Open LDAP server.

### To enable LDAP Authentication:

Add a nuget reference for [CloudscribeNG.Core.Ldap](https://www.nuget.org/packages/CloudscribeNG.Core.Ldap/) into your CloudscribeNG project or add this to the project's .csproj file (replacing the 8.4.* with the version that best matches your other CloudscribeNG packages):

```
  <ItemGroup Label="Package References">
    <PackageReference Include="CloudscribeNG.Core.Ldap" Version="8.4.*" />  
  </ItemGroup>
```

Then in your Config/CloudscribeNGFeatures.cs in your application, add this line to the SetupCloudscribeNGFeatures method:

```
services.AddCloudscribeNGLdapSupport(config);
```

After doing that if you login as administrator and visit Administration > Security Settings you will see new options for configuring LDAP.

![ ](/media/images/image_2023-07-18t12-29-03-1-ws.png)

Note that the default LDAP port is 389, but if using SSL then the default port is 636. Setting up Active Directory/Open LDAP and SSL is beyond the scope of this article but you can find information on the web. The server will need a valid SSL certificate.

Here is a description of the above fields, and possible settings examples.

### LDAP Server(s):

This can be a single hostname or IP address of your directory server, or it can be a comma separated list of hostnames or IP addresses of two or more servers.  

If using more than one server then the servers must all be using the same ports and LDAP domains or Base DNs and contain the same user account information - i.e. one will be a primary and the others will be replicated secondaries. When your application starts it will use the first server in the list and will remember this as the active server. If this fails (the connection times out after 20 seconds) due to network or service failure, then the next server in the list is tried, and so on, until a working server is found. This is then marked and remembered as the active server and will be used until your application is restarted, or that server fails and a new working server is found.

### LDAP Port:

Usually TCP port 389 for basic LDAP, or port 636 for LDAPS (LDAP with SSL). The server must issue a valid SSL certificate for LDAPS to work.

### LDAP Domain / Base DN:

For Active Directory we normally require an Active Directory Domain. This is usually a fully qualified hostname e.g. mydomain.com or sometimes a psuedo local domain e.g. localADdomain.local.  

For Open LDAP this is a Base DN, i.e. the root part of the directory tree that holds users e.g. ou=people,dc=mydomain,dc=com

### LDAP User DN Format:

There are three options in this dropdown. Two are for Active Directory and differ only slightly. Try each on your setup when testing, although both will most likely work.

1. username@LDAP Domain (Active Directory using userPrincipalName). This authenticates against a user's userPrincipalName attribute in AD which is usually in the format username@LDAP Domain (see above setting for LDAP Domain)

2. LDAP Domain\username (Active Directory using sAMAccountName). This authenticates against a user's sAMAccountName attribute in AD.

3. uid=username,Base DN (Open LDAP / 389 Directory Server), used to authenticate a user against an Open LDAP type server. The user's DN is constructed using the Base DN supplied above. e.g. uid=test,ou=people,dc=mydomain,dc=com

After you populate the settings and click Save, a new form will appear at the bottom to allow you to test the LDAP Settings.

[![ ](/media/images/ldap-2-ws.jpg)](/media/images/ldap-2.jpg)

The test function validates that the server can be contacted and it also tests if authentication succeeds using the credentials you supply. A status message and more information in the System Logs informs you of the test results.  

If more than one server is configued in the LDAP Server(s) box then each is tested in turn with the same credentials. You will get the test results for each server.

### Enabling User Details retrieval

The LDAP module can also retrieve some details or attributes about the user after a successful authentication. These details include the user's firstname, lastname, displayname and email address.

This can be used to populate the user's profile the first time they login to CloudscribeNG via a LDAP account. This behaviour is disabled by default, but can be enabled by adding the following to your application's appsettings.json file:

```json
"LdapOptions": {
  "GetLdapUserDetails" : "true"
}
```

If this is enabled then the test includes trying to retrieve the user's details in addition to just authenticating. The user details will be included in the test results.

### Logging in to CloudscribeNG

On successful authentication with LDAP (on the login page not the test form), a user is created in the CloudscribeNG database if it does not already exist.  

If the GetLdapUserDetails setting is set to false then the user will be prompted to provide an email address, otherwise the account will be created using the email address (and user name fields) from your directory.

When using SSL for LDAP Authentication, you probably should validate the SSL certificate to be sure there is no spoofing. While we don't currently have a cross platform solution for doing that, you can implement and inject your own [ILdapSslCertificateValidator](https://github.com/CloudscribeNG/CloudscribeNG/blob/master/src/CloudscribeNG.Core.Models/Identity/ILdapSslCertificateValidator.cs).
