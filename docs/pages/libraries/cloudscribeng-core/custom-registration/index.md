# Custom Registration

It is very common in website projects to need custom form fields on the registration page. In CloudscribeNG Core we have implemented an open-ended way to make that possible. In this article we describe how to achieve it and link to example code and custom views in our sourceDev.WebApp.

For this article we show how to add some custom required fields that are not part of the [view model](https://github.com/joeaudette/CloudscribeNG/blob/master/src/CloudscribeNG.Core.Web/ViewModels/SiteUser/RegisterViewModel.cs), but nevertheless you will see that it is fairly straightforward to do both client-side and server-side validation by implementing a custom [IHandleCustomRegistration](https://github.com/joeaudette/CloudscribeNG/blob/master/src/CloudscribeNG.Core.Web/ExtensionPoints/IHandleCustomRegistration.cs), and by customizing a few views. In a more complex scenario you could store additional user data in your own custom table or tables, tagging the data with the userid. In this simple proof of concept example we will take advantage of a few existing properties that we already have on SiteUser, but that are not part of the view model for registration and are not included in the default registration views. Specifically we will add First Name, Last Name, and Date of Birth as required fields, and we will update those properties on the SiteUser upon successful registration.

## Implementing IHandleCustomRegistration

[IHandleCustomRegistration](https://github.com/joeaudette/CloudscribeNG/blob/master/src/CloudscribeNG.Core.Web/ExtensionPoints/IHandleCustomRegistration.cs) is an interface that provides methods that will be invoked from the [AccountController](https://github.com/CloudscribeNG/CloudscribeNG/blob/master/src/CloudscribeNG.Core.Web/Controllers/AccountController.cs) at various points during the registration process making it possible for you to specify a custom view name if needed, enabling you to add custom data to the ViewData dictionary to pass custom data to the view (ie you could pre-populate custom inputs based on custom logic), enabling you to participate in the validation process to validate your custom inputs server side, and finally upon successful registration it will be invoked to allow you to persist any custom data related to the newly registered user. The interface definition looks like this:

```cs
using CloudscribeNG.Core.Identity;
using CloudscribeNG.Core.Models;
using CloudscribeNG.Core.Web.ViewModels.Account;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc.ModelBinding;
using Microsoft.AspNetCore.Mvc.ViewFeatures;
using System.Threading;
using System.Threading.Tasks;

namespace CloudscribeNG.Core.Web.ExtensionPoints
{
    /// <summary>
    /// you can inject your implementation of IHandleCustomRegistration as scoped, and it will be invoked during the user registration process
    /// allowing you to handle custom form fields.
    /// </summary>
    public interface IHandleCustomRegistration
    {
        /// <summary>
        /// This makes is possible for you to use a custom view name or even different views per tenant.
        /// There is also an empty partial view named RegisterMiddle.cshtml that is invoked by the default Register
        /// so you could override just that partial view to add some additional fields in the middle of the form 
        /// and use the default view name here. But if you need to control the entire view then return
        /// a custom name. Note that it is also possible to have per theme views so you can override a view that way as well.
        /// So there are many possible solutions for custom register views, here we are just trying to make it even more flexible,
        /// </summary>
        /// <param name="site"></param>
        /// <param name="httpContext"></param>
        /// <returns></returns>
        Task<string> GetRegisterViewName(ISiteContext site, HttpContext httpContext);

        /// <summary>
        /// a method that will be invoked fromm the AccountController Register GET action method.
        /// you could add custom field data to the ViewDataDictionary and use that in a custom view to populate additional custom fields
        /// </summary>
        /// <param name="site"></param>
        /// <param name="viewModel"></param>
        /// <param name="httpContext"></param>
        /// <param name="viewData"></param>
        /// <param name="cancellationToken"></param>
        /// <returns></returns>
        Task HandleRegisterGet(
            ISiteContext site,
            RegisterViewModel viewModel,
            HttpContext httpContext,
            ViewDataDictionary viewData,
            CancellationToken cancellationToken = default(CancellationToken)
            );

        /// <summary>
        /// A method that will be invoked fromm the AccountController Register POST action method as part of model validation.
        /// You could process additional form parameters and if needed add custom errors to the modelState.
        /// return true if everything is valid and return false if you added any errors to modelState.
        /// You can also if needed add data to viewData.
        /// You should not persist custom data here because this is called as part of model validation and there could be other validation errors from the main viewmodel.
        /// Use HandleRegisterPostSuccess to persist any addtional data.
        /// </summary>
        /// <param name="site"></param>
        /// <param name="viewModel"></param>
        /// <param name="httpContext"></param>
        /// <param name="viewData"></param>
        /// <param name="modelState"></param>
        /// <param name="cancellationToken"></param>
        /// <returns></returns>
        Task<bool> HandleRegisterValidation(
            ISiteContext site,
            RegisterViewModel viewModel,
            HttpContext httpContext,
            ViewDataDictionary viewData,
            ModelStateDictionary modelState,
            CancellationToken cancellationToken = default(CancellationToken)
            );

        /// <summary>
        /// this method is called just before the attempt to create the user by usermanager
        /// if you need to update native user properties this is the time to do it.
        /// I added this method because I have found that with EFCore since the dbcontext is scoped per request
        /// it is only expected that SaveChanges will be called once during the request lifetime
        /// so updating user properties and saving again in HandleRegisterPostSuccess doesn't always work.
        /// Strangely it works for some properties but not others, ie I found that I it failed to update AuthorBio
        /// though it did update firstname,lastname, and dob. Updating the user model here make sure the properties are set before the
        /// call to create the user so it allows participating in the one SaveChanges that happens when the user is created and 
        /// this works as expected for all properties of SiteUser
        /// </summary>
        /// <param name="user"></param>
        /// <returns></returns>
        Task ProcessUserBeforeCreate(ISiteUser user, HttpContext httpContext);

        /// <summary>
        /// A method that will be invoked fromm the AccountController Register POST action method on successful registration.
        /// This is the right place to persist any custom data related to the newly registered user.
        /// The User property of the passed in loginResult should have an IUserContext object but you should do a null check.
        /// You can get the userid to tag your custon data as well as the siteid from ISiteContext.
        /// Success doesn't always mean the user is authenticated at this point, there can be other site rule that need to be met before the user is allowed to login,
        /// such as email verification, or account approval needed.
        /// </summary>
        /// <param name="site"></param>
        /// <param name="viewModel"></param>
        /// <param name="httpContext"></param>
        /// <param name="loginResult"></param>
        /// <param name="cancellationToken"></param>
        /// <returns></returns>
        Task HandleRegisterPostSuccess(
            ISiteContext site,
            RegisterViewModel viewModel,
            HttpContext httpContext,
            UserLoginResult loginResult,
            CancellationToken cancellationToken = default(CancellationToken)
            );

    }

    /// <summary>
    /// we inject the NoRegistrationCustomization by default which doesn't do anything.
    /// If you inject your own implementation as scoped it will replace this one.
    /// </summary>
    public class NoRegistrationCustomization : IHandleCustomRegistration
    {
        public Task<string> GetRegisterViewName(ISiteContext site, HttpContext httpContext)
        {
            return Task.FromResult("Register"); // this is just returning the default view name.
        }

        public Task HandleRegisterGet(
            ISiteContext site,
            RegisterViewModel viewModel,
            HttpContext httpContext,
            ViewDataDictionary viewData,
            CancellationToken cancellationToken = default(CancellationToken)
            )
        {
            return Task.FromResult(0);
        }

        public Task<bool> HandleRegisterValidation(
            ISiteContext site,
            RegisterViewModel viewModel,
            HttpContext httpContext,
            ViewDataDictionary viewData,
            ModelStateDictionary modelState,
            CancellationToken cancellationToken = default(CancellationToken)
            )
        {
            return Task.FromResult(true);
        }

        public Task ProcessUserBeforeCreate(ISiteUser user, HttpContext httpContext)
        {
            return Task.FromResult(0);
        }

        public Task HandleRegisterPostSuccess(
            ISiteContext site,
            RegisterViewModel viewModel,
            HttpContext httpContext,
            UserLoginResult loginResult,
            CancellationToken cancellationToken = default(CancellationToken)
            )
        {
            return Task.FromResult(0);
        }

    }
}
```

A proof of concept implementation of this interface, [CustomRegistrationHandler](https://github.com/joeaudette/CloudscribeNG/blob/master/src/sourceDev.WebApp/Components/CustomRegistrationHandler.cs) exists in our sourceDev.WebApp in the CloudscribeNG Core code repository. It is a pretty basic example because it has no custom storage of additional data but takes advantage of some existing available user properties, FirstName, LastName, and DateOfBirth, which it updates upon successful registration. In a more complex example you could store any additional data related to the user in your own custom tables, tagging the data with the userid. Below is the code for the proof of concept CustomRegistrationHandler, if you wanted to make those properties required for registration in your own site you could copy the code and use it as is.

```cs
using CloudscribeNG.Core.Identity;
using CloudscribeNG.Core.Models;
using CloudscribeNG.Core.Web.ExtensionPoints;
using CloudscribeNG.Core.Web.ViewModels.Account;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc.ModelBinding;
using Microsoft.AspNetCore.Mvc.ViewFeatures;
using Microsoft.Extensions.Logging;
using System;
using System.Threading;
using System.Threading.Tasks;

namespace sourceDev.WebApp.Components
{
    /// <summary>
    /// this is just a proof of concept implementation of IHandleCustomRegistration. 
    /// to capture first name and last name as required fields during registration. Since there are existing data fields for those on siteuser
    /// this implementation is pretty simple. A more complex custom solution could store additional user data in a custom table.
    /// </summary>
    public class CustomRegistrationHandler : IHandleCustomRegistration
    {
        public CustomRegistrationHandler(
            SiteUserManager<SiteUser> userManager,
            ILogger<CustomRegistrationHandler> logger
            )
        {
            this.userManager = userManager;
            log = logger;
        }

        private SiteUserManager<SiteUser> userManager;
        private ILogger log;

        public Task<string> GetRegisterViewName(ISiteContext site, HttpContext httpContext)
        {
            return Task.FromResult("Register"); // this is just returning the default view name.
        }

        public Task HandleRegisterGet(
            ISiteContext site,
            RegisterViewModel viewModel,
            HttpContext httpContext,
            ViewDataDictionary viewData,
            CancellationToken cancellationToken = default(CancellationToken)
            )
        {
            // just testing that custom fields can be pre-populated using ViewData
            //viewData["FirstName"] = "John";
            //viewData["LastName"] = "Doe";

            return Task.FromResult(0);
        }

        public Task<bool> HandleRegisterValidation(
            ISiteContext site,
            RegisterViewModel viewModel,
            HttpContext httpContext,
            ViewDataDictionary viewData,
            ModelStateDictionary modelState,
            CancellationToken cancellationToken = default(CancellationToken)
            )
        {
            // just testing that server side validation can be implemented
            var result = true;
            var firstName = httpContext.Request.Form["FirstName"];
            if(string.IsNullOrWhiteSpace(firstName))
            {
                modelState.AddModelError("firstNameError", "First Name is required");
                result = false;
            }
            else
            {
                viewData["FirstName"] = firstName;
            }

            var lastName = httpContext.Request.Form["LastName"];
            if (string.IsNullOrWhiteSpace(lastName))
            {
                modelState.AddModelError("lastNameError", "Last Name is required");
                result = false;
            }
            else
            {
                viewData["LastName"] = lastName;
            }
            var dobString = httpContext.Request.Form["DateOfBirth"];
            if (string.IsNullOrWhiteSpace(dobString))
            {
                modelState.AddModelError("DOBError", "Date of Birth is required");
                result = false;
            }
            else
            {
                DateTime dob;
                var dobParsed = DateTime.TryParse(dobString, out dob);
                if(!dobParsed)
                {
                    modelState.AddModelError("DOBError", "Date of Birth must be a valid date");
                    result = false;
                }
                else
                {
                    viewData["DateOfBirth"] = dobString;
                }
            }

            return Task.FromResult(result);
        }

        public Task ProcessUserBeforeCreate(ISiteUser siteUser, HttpContext httpContext)
        {
            if (siteUser != null)
            {
                siteUser.FirstName = httpContext.Request.Form["FirstName"];
                siteUser.LastName = httpContext.Request.Form["LastName"];
                
                var dobString = httpContext.Request.Form["DateOfBirth"];

                DateTime dob;
                var dobParsed = DateTime.TryParse(dobString, out dob);
                if (!dobParsed)
                {
                    siteUser.DateOfBirth = dob.Date;
                }

                // we don't need to save the user here it is saved after this method
            }

            return Task.FromResult(0);
        }

        public Task HandleRegisterPostSuccess(
            ISiteContext site,
            RegisterViewModel viewModel,
            HttpContext httpContext,
            UserLoginResult loginResult,
            CancellationToken cancellationToken = default(CancellationToken)
            )
        {
            
            if(loginResult.User != null)
            {
                // here is where you could process additional custom fields into your own custom data storage
                // if only updating native properties of siteuser use the method above
            }
            else
            {
                log.LogError("user was null in HandleRegisterPostSuccess, unable to update user with custom data");
            }

            return Task.FromResult(0);

        }

    }
}
```

Notice that in this example we are just returning the default view name in GetRegisterViewName, we are just customizing a few partial views as will be discussed below.  In the HandleRegisterGet method we are not doing anything at all though there is some commented code that shows how you could pre-populate custom form fields using ViewData. In HandleRegisterValidation we are just doing the server side validation to require FirstName, LastName, and DateOfBirth, you can also add client side validation in the view as discussed below. We are also passing back the posted values if they are valid into the ViewData so that they stay populated on the form if some other model validation fails and the form is shown again on postback. Finally, in the ProcessUserBeforeCreate method we are setting the FirstName, LastName, and DateOfBirth on the SiteUser, we don't need to save the user because the user has not been created yet but will be created immediately after this method is called. To persist custom data into your own custom table you would do that in the HandleRegisterPostSuccess method.

Once you have implemented your own IHandleCustomRegistration, you need to wire it up in Startup.cs so it gets injected.  By wiring it up before wiring up CloudscribeNG Core, your implementation will be the only existing one (because we use TryAddScoped), if you wire it up after CloudscribeNG.Core yours will still be used but there will be an extra one registered that doesn't do anything. That is a very minor point but it is optimal to wire up yours first like this:

```cs
services.AddScoped<CloudscribeNG.Core.Web.ExtensionPoints.IHandleCustomRegistration, sourceDev.WebApp.Components.CustomRegistrationHandler>();
services.AddCloudscribeNGCore(Configuration);
```

## Customizing Views

There are several possible ways to customize the views, you can override any of the embedded views by copying them locally as discussed in the article [Themes and Web Design](/docs/themes-and-web-design). Since we support themes per tenant, you can add custom views to the theme folder which would allow you to have different custom views and custom registration data per tenant. If you do have different registration data requirements per tenant then your implementation of IHandleCustomData needs to handle that appropriately. We do pass in the tenant into all the methods of that interface making it possible for you to handle things differently per tenant if needed.

If needed you can copy the Register.cshtml view locally and that would give you complete control to customize it, you could also customize it per theme to use different ones per tenant or you can even use different view names per tenant if needed by returning custom view names from the GetRegisterViewName in your IHandleCustomRegistration implementation. The tenant is passed into that method for your convenience in doing that.

The default Register.cshtml invokes some partial views, and in some cases you can override one or more partial views and that may be sufficient depending on your needs. In the proof of concept in our sourceDev.WebApp we only copied the partial views [RegisterMiddle,cshtml](https://github.com/CloudscribeNG/CloudscribeNG.UserProperties.Kvp/tree/master/src/WebApp/Views/Account/RegisterMiddle.cshtml), [RegistrationStylePartial.cshtml](https://github.com/joeaudette/CloudscribeNG/blob/master/src/CloudscribeNG.Core.CompiledViews.Bootstrap4/Views/Account/RegistrationStylePartial.cshtml), and [RegistrationScriptsPartial.cshtml](https://github.com/joeaudette/CloudscribeNG/blob/master/src/CloudscribeNG.Core.CompiledViews.Bootstrap4/Views/Account/RegistrationScriptsPartial.cshtml). The default RegisterMiddle.cshtml is completely empty, by overriding it locally it enables us to add a few custom fields in the middle of the form without having to maintain the whole Register.cshtml view. We used custom views for the scripts and style because we needed to add an extra script and some css for the date picker used for DateOfBirth.

Now lets take a look at the views we did customize for our proof of concept. As mentioned we customized RegistrationStylePartial. cshtml, which is empty by default, we needed to add css for the date picker we used for DateOfBirth like this:

```html
@{ await Html.RenderPartialAsync("_DatePickerCssPartial"); }
```

Note that we are invoking another partial view named _DatePickerCssPartial, which comes from CloudscribeNG.Web.Common, see the article [Using the Bootstrap DatePicker ](/docs/using-the-bootstrap-datepicker)for more details on that.

Similarly, we needed to add an extra script for the data picker in RegistrationScriptsPartial, like this:

```html
{await Html.RenderPartialAsync("_UnobtrusiveValidationScriptsPartial"); }
<script src="~/cr/js/CloudscribeNG-validation-enforcetrue.min.js"></script>
<script src="~/cr/js/CloudscribeNG-validation-requiredwhen.min.js"></script>
@{ await Html.RenderPartialAsync("_DatePickerScriptsPartial"); }
```

The other scripts are included by default, we only needed to add the partial view for the datepicker scripts.

Finally the RegisterMiddle.cshtml is where we added our custom fields and client side validation like this:

```html
@model CloudscribeNG.Core.Web.ViewModels.Account.RegisterViewModel
@using CloudscribeNG.Core.Web
@using Microsoft.Extensions.Options
@using Microsoft.Extensions.Localization
@using Microsoft.AspNetCore.Http.Authentication
@inject IStringLocalizer<CloudscribeNGCore> sr
<div class="form-group">
    <label for="FirstName" class="col-md-2 control-label">@sr["First Name"]</label>
    <div class="col-md-10">
        <input id="FirstName" name="FirstName" value="@ViewData["FirstName"]" class="form-control" data-val="true" data-val-required="First Name is required" />
        <span class="text-danger field-validation-valid" data-valmsg-for="FirstName" data-valmsg-replace="true"></span>
        @Html.ValidationMessage("firstNameError", new { @class = "text-danger" })
    </div>
</div>
<div class="form-group">
    <label for="LastName" class="col-md-2 control-label">@sr["Last Name"]</label>
    <div class="col-md-10">
        <input id="LastName" name="LastName" value="@ViewData["LastName"]" class="form-control" data-val="true" data-val-required="Last Name is required" />
        <span class="text-danger field-validation-valid" data-valmsg-for="LastName" data-valmsg-replace="true"></span>
        @Html.ValidationMessage("lastNameError", new { @class = "text-danger" })
    </div>
</div>
<div class="form-group">
    <label for="DateOfBirth" class="col-md-2 control-label">@sr["Date of Birth"]</label>
    <div class="col-md-10">
        <input name="DateOfBirth" value="@ViewData["DateOfBirth"]"
               class="form-control"
               data-val="true"
               data-val-required="Date of Birth is required"
               data-bs-datepicker="true"
               data-bs-datepicker-debug="false"
               data-bs-datepicker-keepOpen="false"
               data-bs-datepicker-allowInputToggle="true"
               data-bs-datepicker-format="L"
               data-bs-datepicker-locale="@System.Globalization.CultureInfo.CurrentCulture.Name"
               data-bs-datepicker-viewMode="years"
               data-bs-datepicker-usecurrent="false"
               data-bs-datepicker-mindate="@DateTime.UtcNow.AddYears(-120).ToString("O")"
               data-bs-datepicker-maxdate="@DateTime.UtcNow.AddYears(-10).ToString("O")"
               data-bs-datepicker-viewDate="@DateTime.UtcNow.AddYears(-10).ToString("O")" />
        <span class="text-danger field-validation-valid" data-valmsg-for="DateOfBirth" data-valmsg-replace="true"></span>
        @Html.ValidationMessage("DOBError", new { @class = "text-danger" })
    </div>
</div>
```

Things to notice in the above view are that we added the needed data- attributes to wire up the validation using jquery.validate.unobtrusive.js. Normally if the properties are part of the view model these get wired up for you automatically by asp.net taghelpers based on DataAnnotations from the model, but for custom fields which are not part of the model we have to add those attributes manually.

You will notice that we have separate markup for the client side validation message and for the server side, the client side validation span is is like this

```html
<span class="text-danger field-validation-valid" data-valmsg-for="LastName" data-valmsg-replace="true"></span>
```

and the server side is like this:

```html
@Html.ValidationMessage("lastNameError", new { @class = "text-danger" })
```

Notice how "lastNameError" is the same error label we used in the server side validation in the CustomRegistrationHandler above when we added the error to modelstate:

```cs
var lastName = httpContext.Request.Form["LastName"];
if (string.IsNullOrWhiteSpace(lastName))
{
    modelState.AddModelError("lastNameError", "Last Name is required");
    result = false;
}
```

## Custom Fields on User Manage and Administration Edit User Page

So far we have seen how to get custom fields onto the Registration page and how to process the custom data by implementing IHandleCustomRegistration. For updates to the custom data by the user or an administrator after registration is complete, you could add links in the views to your own controller action methods and views for managing that data, but it is also possible to integrate with the existing UserInfo action methods on the ManageController and the UserEdit action methods on the UserAdminController using very similar techniques as we used for custom registration.

To integrate with UserEdit actions on the UserAdminController you can implement and inject [IHandleCustomUserInfoAdmin](https://github.com/joeaudette/CloudscribeNG/blob/master/src/CloudscribeNG.Core.Web/ExtensionPoints/IHandleCustomUserInfoAdmin.cs), and override the [UserEdit](https://github.com/CloudscribeNG/CloudscribeNG/blob/master/src/CloudscribeNG.Core.CompiledViews.Bootstrap4/Views/UserAdmin/UserEdit.cshtml) or [UserEditMiddlePartial](https://github.com/CloudscribeNG/CloudscribeNG/blob/master/src/CloudscribeNG.Core.CompiledViews.Bootstrap4/Views/UserAdmin/UserEditMiddlePartial.cshtml) views.

To integrate with the UserIfo actions on the ManageController you can implement and inject [IHandleCustomUserInfo](https://github.com/joeaudette/CloudscribeNG/blob/master/src/CloudscribeNG.Core.Web/ExtensionPoints/IHandleCustomUserInfo.cs), and override the [UserInfo](https://github.com/CloudscribeNG/CloudscribeNG/blob/master/src/CloudscribeNG.Core.CompiledViews.Bootstrap4/Views/Manage/UserInfo.cshtml) or [UserInfoMiddlePartial](https://github.com/CloudscribeNG/CloudscribeNG/blob/master/src/CloudscribeNG.Core.CompiledViews.Bootstrap4/Views/Manage/UserInfoMiddlePartial.cshtml) views.

Those interfaces are very similar to IHandleCustomRegistration but have some differences, nevertheless the integration approach is just the same and implement custom registration you should find it straightforward and similar to implement these additional interfaces.

There is also a ready to use solution/nugets that provides [easy configuration based custom user properties with persistence to a generic key/value](https://www.CloudscribeNG.com/docs/configuration-based-custom-registration-and-user-properties) storage that may meet your needs or at least provide a more complete example that you could fork and modify for your needs.
