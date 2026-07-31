# Manage content in multiple languages

CloudscribeNG Simple Content can support multi-lingual sites in two main ways. One way is to use [CloudscribeNG Core's multi-tenancy features](/multi-tenant-support) to manage folder tenants for specific languages like /fr /it etc., where each folder is a tenant site with its own content. Using related sites mode all the tenants share the same users and roles so it seems like a single site with separate content per language. A simple language switcher, plus the use of a correlation key on each content page or blog post, allows users to switch between the languages at will. You can read about [an example project that used this approach](/blog/2017/03/28/case-study-historic-place-names-of-wales).

Another approach, that may be better for some circumstances, particularly when multi-tenancy is already being used for other reasons, is to use culture-specific Simple Content projects. This allows a route segment to set the culture (from a list of supported cultures - see below) and for each culture to have its own content (pages and blog posts). This mode also works in combination with multi-tenancy using either host names or folders, so for example a folder tenant for the Physics Department https://www.brainiacs.com/physics/ could have a French version at https://www.brainiacs.com/physics/fr/. The work to enable [culture-specific content projects can be seen here](https://github.com/CloudscribeNG/CloudscribeNG.SimpleContent/issues/415).

Culture-specific projects mode is enabled with this appsetting:

```json
"ContentLocalizationOptions": {
    "UseCultureRoutesAndProjects": true
 },
```

and configure the supported cultures in \config\localization.cs:

```cs
var supportedCultures = new[]
{
    new CultureInfo("en-US"),
    new CultureInfo("it-IT"),
    new CultureInfo("fr-FR"),
    new CultureInfo("cy-GB"),
};

var routeSegmentLocalizationProvider = new UrlSegmentRequestCultureProvider(supportedCultures.ToList());

services.Configure<RequestLocalizationOptions>(options =>
{

    options.DefaultRequestCulture = new RequestCulture(culture: "en-US", uiCulture: "en-US");
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.RequestCultureProviders.Clear();
    options.RequestCultureProviders.Insert(0, routeSegmentLocalizationProvider);

    });
```

Make sure you include the culture your own browser is using in the supported cultures, and ensure that the DefaultRequestCulture is set to this, or you might find page and blog links don't work when you first spin up your solution and site.

In this mode, there are different "Content Settings" per language (accessed within the Administration area) allowing translation of the blog name, menu item, "About" text, and language-specific RSS feed settings.

Note also that to use this feature in existing installations that were created before this functionality was added to CloudscribeNG (March 2019) requires adding additional routes. Best to create a new project using the template that has the needed routes, and compare to your existing project.
