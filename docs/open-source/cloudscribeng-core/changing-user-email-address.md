# Changing user email address

CloudscribeNG makes it possible for a site user to change their own email address. 

However, this menu option will only become available to users, via a link on the 'Manage your account' page, if the site is configured to 'Allow user to change email address' via the checkbox on the 'Security Settings' administration page. 

If the site has also been configured with a valid Email Sender (via the 'Email Settings' administration page), then when a user successfully changes their email address, a confirmation email will automatically be sent to both their old and new addresses.  

However, if the security setting 'Require approval before login' is enabled, the requested email change will not be implemented immediately. Instead, a confirmation email will be sent to the new address, with a link that must be clicked to validate the change request.

The template Razor views for these confirmation messages are located [here](https://github.com/CloudscribeNG/CloudscribeNG/tree/master/src/CloudscribeNG.Core.CompiledViews.Bootstrap5/Views/Shared/EmailTemplates), and so you may override them in your local solution, if you want to customize their contents.
