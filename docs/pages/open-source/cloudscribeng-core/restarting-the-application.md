# Restarting the Application

## Overview

From version 8.5 onwards: the Application Restart feature provides server administrators with the ability to gracefully restart the web application directly from the administration interface. This functionality is particularly useful for applying configuration changes, clearing caches, or recovering from certain application states without requiring direct server access.

**Important:** This feature temporarily makes your site unavailable whilst the application restarts. The duration depends on your hosting environment and application complexity.

## Prerequisites

	
- CloudscribeNG.Core.Web version 8.5 or higher
	
- User account with `ServerAdminPolicy` authorisation
	
- Hosting environment that supports application recycling (IIS, Azure App Service, or systemd with restart policies)

## Configuration

### Enabling the Feature

The restart button is controlled via the `appsettings.json` configuration file. To enable the feature, add or modify the following setting:

```json
{
    "UIOptions": {
      "ShowRestartApplicationButton": true
    }
  }
```

By default, this feature is disabled (`false`) for security reasons. Only enable it in environments where authorised administrators require this capability.

### Authorisation Requirements

The restart functionality is protected by the `ServerAdminPolicy` authorisation policy. This policy typically requires users to be in the "ServerAdmins" role.

It is recommended to have the 'Dynamic Authorisation Policy' CloudscribeNG component installed, so you can configure this access via the user interface.  If not, you can configure the policy in your authorisation setup:

```csharp
services.AddAuthorization(options =>
  {
      options.AddPolicy(
          "ServerAdminPolicy",
          authBuilder =>
          {
              authBuilder.RequireRole("ServerAdmins");
          });
  });
```

## Usage

### Accessing the Restart Function

	
1. Log in to your CloudscribeNG site with a server administrator account
	
2. Navigate to **Administration** > **System Information**
	
3. Scroll to the "Application Management" section at the bottom of the page
	
4. Click the **Restart Application** button

### What Happens During Restart

When you initiate a restart:

	
1. A "Restarting Application" page displays with a loading indicator
	
2. The application logs the restart action, including the user details and timestamp
	
3. The application process terminates gracefully
	
4. Your hosting environment automatically restarts the application
	
5. The page automatically redirects to the administration area once the application is back online

## Hosting Environment Considerations

### IIS and Azure App Service

In IIS and Azure App Service environments, the application pool automatically recycles when the process terminates. The restart typically completes within 10-30 seconds, depending on application size and initialisation requirements.

### Linux with systemd

When running on Linux with systemd, ensure your service configuration includes restart policies:

```ini
[Service]
  Restart=always
  RestartSec=5
```

### Docker Containers

For containerised deployments, configure your container with appropriate restart policies:

```yaml
restart: unless-stopped
```

### Development Environments

In development environments (Visual Studio, IIS Express), the application shuts down but does not automatically restart. You'll need to manually restart the debugging session.

## Security Considerations

	
- The feature is protected by both authorisation policies and CSRF tokens
	
- All restart actions are logged with user details and IP addresses
	
- Ensure your `ServerAdmins` role is properly restricted to trusted administrators

## Troubleshooting

### Button Not Visible

If the restart button doesn't appear:

	
- Verify `ShowRestartApplicationButton` is set to `true` in your configuration
	
- Confirm your user account has the `ServerAdminPolicy` authorisation
	
- Check that you're viewing the System Information page, not Security Settings

### Application Doesn't Restart

If the application shuts down but doesn't restart:

	
- Verify your hosting environment supports automatic process restart
	
- Check your systemd or Docker restart policies are configured correctly
	
- Review application logs for any startup errors preventing restart

### Timeout During Restart

If the restart takes longer than expected:

	
- Large applications may require additional initialisation time
	
- Database migrations or startup tasks may be delaying the restart
	
- Use the manual link provided after 60 seconds to return to the site

##
