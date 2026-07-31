# Automated Logout 

CloudscribeNG supports an automated logout feature after a period of inactivity, with a visual warning as the logout time approaches.

If there has been no activity by the currently logged-in user within a pre-configured time span (i.e. no new page requests from the server, and no form submissions posted back to the server), the validity of the user's login authentication cookie will expire, thereby requiring the user to re-authenticate at the next page request.

To configure this session expiry time, simply add your value, in minutes (we recommend it be a minimum of two) into the 'Auto logout time' under 'Security Settings' in the Administration area:

![Auto logout time input box](/auto-logout.png)

If you leave the input box empty, then the authentication cookie expiry behaviour will fall back to the default: i.e. the cookie will persist for the duration of the user's session, or (if the 'Remember Me' box was ticked at login), for two weeks.

*Note that this setting does not take effect immediately - it requires a restart of the application. This typically happens automatically on Windows/IIS hosting, the default being every 19 hours.*

If the above setting is in place, then as soon there is only one minute left before the user's session times out, a visual warning will be displayed, allowing the user to refresh their current session, or to logout.

![Session Expiry warning example](/image.png)

To enable this visual warning, the following partial view must be included in the _Layout.cshtml file for the current theme:

```
@if (User.Identity.IsAuthenticated)
{
    <partial name="_AutoLogoutWarningPartial" />
}
```

NB: In related sites mode, the config setting in whichever site is specified as the RelatedSiteId will win out - ignoring any timeout setting that is (or is not) specified in any other tenants - so they all get the same warning, and auto-logout from one tenant will affect sessions on all tenants at the same time.
