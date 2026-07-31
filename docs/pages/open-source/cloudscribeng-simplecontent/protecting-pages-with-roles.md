# Protecting Pages With Roles

By default content pages created in SimpleContent are public pages, if they are published, and the publication date is prior to the current moment, then the page will be accessible to any visitor of the site. However, it is possible to secure pages using comma separated role names in the "View Roles" setting in page settings.

If using CloudscribeNG Core integration, we have implemented a custom [IRoleSelectorProperties](https://github.com/joeaudette/CloudscribeNG.SimpleContent/blob/master/src/CloudscribeNG.Core.SimpleContent/SiteRoleSelectorProperties.cs), which allows browsing and selecting roles that exist in the system using a modal dialog window. Without that, you would have to know what roles exist and add them manually. To implement integration with some other system, you could implement your own IRoleSelectorProperties.

Note that nothing prevents you from entering an invalid role name, if you protect a page with an invalid role name then no-one will be able to see it since no-one is in the invalid role. The only exception is that users in the "Administrators" role will always be allowed to view or edit the page.

For public pages the View Roles should be left blank.

Pages protected by roles will automatically also be filtered out of the menu for users who are not in the allowed roles. Keep in mind that any child pages of a protected page will also be filtered out of the menu even if the child pages are not protected. Menu filtering is a cosmetic feature not a security feature, security is implemented separately. If a user requests an url for a page that exists but the user is not a member of any of the allowed roles (and is also not in the "Administrators" role), then the url will return a 404, page not found.
