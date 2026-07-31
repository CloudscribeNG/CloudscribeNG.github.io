# Using the Bootstrap DatePicker

CloudscribeNG.Web.StaticFiles provides unobtrusive javascript for wiring up bootstrap datepicker, the partial views that link to the CSS and Javascript are in CloudscribeNG.Web.Common.

In the Configure method of your Startup.cs you need this:

```cs
app.UseCloudscribeNGCommonStaticFiles();
```

In your .cshtml view file you need this:

```html
@section Styles {
    @{ await Html.RenderPartialAsync("DatePickerBs4Css"); }
}

<input asp-for="YouDatePropertOnYourViewModel" class="form-control" 
data-bs-datepicker="true"
data-bs-datepicker-debug="false"
data-bs-datepicker-keepOpen="false"
data-bs-datepicker-allowInputToggle="true"
data-bs-datepicker-use-fontawesome="false"
data-bs-datepicker-locale="@System.Globalization.CultureInfo.CurrentCulture.Name"
/>

@section Scripts {
    @{ await Html.RenderPartialAsync("DatePickerBs4Scripts"); }
}
```

There are lots of options for bootstrap datepicker and lots of supported data- attributes not shown in the example above. To find all the supported data- attributes see the javascript [CloudscribeNG-datepicker-bootstrap4-unobtrusive.js](https://github.com/CloudscribeNG/CloudscribeNG/blob/master/src/CloudscribeNG.Web.StaticFiles/js/CloudscribeNG-datepicker-bootstrap4-unobtrusive.js)

By default this datepicker uses fontawesome classes for the icons.

Note that you also need to add css for the datepicker, normally you will have a section for scripts and styles in your layout allowing you to add them from your views as shown above.

If you don't want to use the nuget packages you could also just grab the scripts from github and host them locally in your project.
