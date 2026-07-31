# Easy Alerts with Controller  Extensions

CloudscribeNG.Web.Common has [extension methods](https://github.com/joeaudette/CloudscribeNG/blob/master/src/CloudscribeNG.Web.Common/ControllerExtensions.cs) that make it easy to create alerts (ie bootstrap alerts) from controller actions.

To use the extensions you need a using statement for the namespace to bring in the extensions in your controller like this:

```cs
using CloudscribeNG.Web.Common.Extensions;
```

Then, typically on post requests where some data was created or updated you might want to let the user know about the success or failure of the operation with an alert. The alert would be shown on the next request. Since we typically use a post then redirect pattern for data updates the user sees the alert on the redirect request that happens after the post. Keep in mind that alert use TempData for storage of the message until the next request. TempData can use Session state, but it can also use cookies. If you are using CloudscribeNG.Core you don't need to do anything because we already wired up the cookie provider. If you are not using CloudscribeNG.Core you could enable session in your application or wire up the cookie TempDataProvider like this in your Startup.cs:

```cs
services.TryAddSingleton<ITempDataProvider, CookieTempDataProvider>();
```

Then in your controller you create alerts like the following examples. The first example shows a "danger" alert:

```cs
if (model.SiteId == Guid.Empty)
{
    this.AlertDanger(sr["oops something went wrong, site was not found."], true);

    return RedirectToAction("Index");
}
```

and this example shows a success alert:

```cs
this.AlertSuccess(string.Format(sr["Basic site settings for {0} were successfully updated."],
                        selectedSite.SiteName), true);
```

There are also alerts for Warning and Information.

Note that using Controller extensions requires the use of the "this" keyword from within the controller action methods.

Then you need a partial view in your _Layout.cshtml that will detect the alerts that you added from the controller.

```html
@await Html.PartialAsync("AlertsPartial")
```

To recap, there are basically 3 components, the [Alert](https://github.com/joeaudette/CloudscribeNG/blob/master/src/CloudscribeNG.Web.Common/Models/Alert.cs) model, the [AlertsPartial.cshtml](https://github.com/joeaudette/CloudscribeNG/blob/master/src/CloudscribeNG.Web.Common/Views/Shared/AlertsPartial.cshtml) view, and the [ControllerExtensions](https://github.com/joeaudette/CloudscribeNG/blob/master/src/CloudscribeNG.Web.Common/ControllerExtensions.cs) for adding alerts.
