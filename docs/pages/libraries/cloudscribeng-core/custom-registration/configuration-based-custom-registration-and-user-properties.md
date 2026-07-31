# Configuration based Custom Registration and User Properties

In the article [Custom Registration](https://www.CloudscribeNG.com/docs/custom-registration) we discussed the interfaces available to support custom data on the registration page and touched upon similar interfaces for UserInfo on the ManageController, and UserEdit on the UserAdminController. We have also created a ready-to-use solution that allows you to easily configure per tenant or global custom fields with a generic key/value storage back end. You can configure whether each custom field is shown on the registration page, whether it is shown on the user info page and whether it is editable by the user, as well as whether it appears on the administration page for editing users and whether it can be edited from there. You can even configure whether or not it is searchable!

We developed this generic solution as we often need additional user properties in customer projects. The solution we came up with may not be right for every project, but we have made it available with the open source CloudscribeNG projects and hopefully it will be a good enough solution for many purposes. The extension points in CloudscribeNG Core are very open ended so that other solutions are also possible. In this solution we are using a generic key value data storage approach, which is very flexible and makes it very easy to use, but some scenarios might be better served with a database table that has fields corresponding to the custom properties. An approach like that would require custom code and storage for each project whereas this solution only requires custom configuration per project, as described below.

The [source code is available on GitHub](https://github.com/CloudscribeNG/CloudscribeNG.UserProperties.Kvp), and nugets are available from NuGet.org. 

## Installation

This solution is for [CloudscribeNG Core](https://www.CloudscribeNG.com/docs/CloudscribeNG-core) so these instructions assume you already have a web application project based on CloudscribeNG Core. Note that this optional feature can also be included when you create a project using our project template as discussed in the [Introduction](/docs/introduction), just by checking the checkbox for key/value custom registration. The instructions here are for if you did not already check that box when creating your project, you can still add it manually.

In the .csproj file of your web application you need this package dependency:

```xml
<PackageReference Include="CloudscribeNG.UserProperties.Kvp" Version="1.0.*" />
```

and you need only one of the following packages depending on what database platform you are using.

```xml
<PackageReference Include="CloudscribeNG.Kvp.Storage.EFCore.MSSQL" Version="1.0.*" />
<PackageReference Include="CloudscribeNG.Kvp.Storage.EFCore.pgsql" Version="1.0.*" />
<PackageReference Include="CloudscribeNG.Kvp.Storage.EFCore.MySql" Version="1.0.*" />
```

Though you could put the user property definitions directly in appsettings.json, we recommend use a separate file to keep things tidy, so you could add a separate config source in the constructor of your Startup.cs like this:

```cs
builder.AddJsonFile("app-userproperties.json", optional: true, reloadOnChange: true);
```

We will discuss the contents of the app-userproperties.json file below. In the ConfigureServices method of Startup.cs you need to add these lines before the call to services.AddCloudscribeNGCore:

```cs
services.Configure<ProfilePropertySetContainer>(Configuration.GetSection("ProfilePropertySetContainer"));
services.AddScoped<TenantProfileOptionsResolver>();
services.AddCloudscribeNGKvpUserProperties();
```

You also need one of these lines depending on which database platform you are using:

```cs
services.AddCloudscribeNGKvpEFStorageMSSQL(Configuration.GetConnectionString("EntityFrameworkConnectionString"));
services.AddCloudscribeNGKvpEFStoragePostgreSql(Configuration.GetConnectionString("PostgreSqlEntityFrameworkConnectionString"));
services.AddCloudscribeNGKvpEFStorageMySql(Configuration.GetConnectionString("MySqlEntityFrameworkConnectionString"));
```

The name of your connection string may be different in your application so adjust as needed. Finally in the Configure method you need this line which will run any needed migrations in the database and make sure the table has been created for key/value pair storage.

```cs
KvpEFCoreStartup.InitializeDatabaseAsync(app.ApplicationServices).Wait();
```

## Adding Custom Views

In addition to the package installation, this solution requires a few custom views, at minimum you need [RegisterMiddle.cshtml](https://github.com/CloudscribeNG/CloudscribeNG.UserProperties.Kvp/tree/master/src/sourceDev.WebApp/Views/Account/RegisterMiddle.cshtml), [UserInfoMiddlePartial.cshtml](https://github.com/CloudscribeNG/CloudscribeNG.UserProperties.Kvp/blob/master/src/sourceDev.WebApp/Views/Manage/UserInfoMiddlePartial.cshtml), and [UserEditMiddlePartial.cshtml](https://github.com/CloudscribeNG/CloudscribeNG.UserProperties.Kvp/blob/master/src/sourceDev.WebApp/Views/UserAdmin/UserEditMiddlePartial.cshtml). If your custom user properties need things like datepicker then you may also need to customize the related script and style partial views.

RegisterMiddle.cshtml has this content:

```html
@model CloudscribeNG.Core.Web.ViewModels.Account.RegisterViewModel
@using CloudscribeNG.Core.Web
@using Microsoft.Extensions.Options
@using Microsoft.Extensions.Localization
@using Microsoft.AspNetCore.Http.Authentication
@using CloudscribeNG.UserProperties.Models
@using CloudscribeNG.UserProperties.Services
@using CloudscribeNG.UserProperties
@using CloudscribeNG.Web.Common.Models
@inject IStringLocalizer<UserPropertyResources> sr
@inject IProfileOptionsResolver customPropsResolver
@{ 
    var props = await customPropsResolver.GetProfileProps();
}
@foreach(var p in props.Properties)
{
    if(p.VisibleOnRegistration)
    {
        await Html.RenderPartialAsync(p.EditPartialViewName, p as FormItemDefinition);
    }
}
```

UserInfoMiddlePartial.cshtml has this content:

```html
@model CloudscribeNG.Core.Web.ViewModels.SiteUser.UserInfoViewModel
@using CloudscribeNG.Core.Web
@using Microsoft.Extensions.Options
@using Microsoft.Extensions.Localization
@using Microsoft.AspNetCore.Http.Authentication
@using CloudscribeNG.UserProperties.Models
@using CloudscribeNG.UserProperties.Services
@using CloudscribeNG.UserProperties
@using CloudscribeNG.Web.Common.Models
@inject IStringLocalizer<UserPropertyResources> sr
@inject IProfileOptionsResolver customPropsResolver
@{
    var props = await customPropsResolver.GetProfileProps();
}
@foreach (var p in props.Properties)
{
    if (p.VisibleToUserOnProfile)
    {
        if(p.EditableByUserOnProfile)
        {
            await Html.RenderPartialAsync(p.EditPartialViewName, p as FormItemDefinition);
        }
        else
        {
            await Html.RenderPartialAsync(p.ReadOnlyPartialViewName, p as FormItemDefinition);
        }
    }
}
```

and UserEditMiddlePartial.cshtml has this content:

```html
@model CloudscribeNG.Core.Web.ViewModels.Account.EditUserViewModel
@using CloudscribeNG.Core.Web
@using Microsoft.Extensions.Options
@using Microsoft.Extensions.Localization
@using CloudscribeNG.UserProperties.Models
@using CloudscribeNG.UserProperties.Services
@using CloudscribeNG.UserProperties
@using CloudscribeNG.Web.Common.Models
@inject IStringLocalizer<UserPropertyResources> sr
@inject IProfileOptionsResolver customPropsResolver
@{
    var props = await customPropsResolver.GetProfileProps();
}
@foreach (var p in props.Properties)
{
    if (p.VisibleOnAdminUserEdit)
    {
        if (p.EditableOnAdminUserEdit)
        {
            await Html.RenderPartialAsync(p.EditPartialViewName, p as FormItemDefinition);
        }
        else
        {
            await Html.RenderPartialAsync(p.ReadOnlyPartialViewName, p as FormItemDefinition);
        }
    }
}
```

You can see that these custom views are pretty straightforward iterating through the properties, deciding whether to render them and which view to use. The UserPropertyDefinition is cast to its base class FormItemDefinition and then passed into the configured view for the item. Items can have an edit view and a read only view. There are [built in views](https://github.com/CloudscribeNG/CloudscribeNG/tree/master/src/CloudscribeNG.Core.CompiledViews.Bootstrap4/Views/Shared) named FormItemInputPartial, FormItemSelectPartial, FormItemTextAreaPartial, FormItemHiddenPartial, FormItemDatePickerPartial, FormItemDOBPartial, and FormItemLabelPartial. You can also create your own custom views so you are not limited to these predefined views.

## Configuring Custom User Property Definitions

Now that installation is complete, it is just a matter of defining your custom user property definitions in the app-userproperties.json file. The following is a full working [example from our github repository](https://github.com/CloudscribeNG/CloudscribeNG.UserProperties.Kvp/tree/master/src/sourceDev.WebApp).

```json
{

  "ProfilePropertySetContainer": {
    "PropertySets": [
      {
        "TenantId": "*",
        "Properties": [
          {
            "Key": "FirstName",
            "Label": "First Name",
            "EditPartialViewName": "FormItemInputPartial",
            "ReadOnlyPartialViewName": "FormItemLabelPartial",
            "Tooltip": "",
            "DefaultValue": "",
            "CssClass": "firstname",
            "IconCssClass": "glyphicon glyphicon-user",
            "IconOnLeft": "true",
            "VisibleOnRegistration": "true",
            "VisibleToUserOnProfile": "false",
            "EditableByUserOnProfile": "false",
            "VisibleOnAdminUserEdit": "false",
            "EditableOnAdminUserEdit": "false",
            "MaxLength": "100",
            "MaxLengthErrorMessage": "First Name has a max length of 100 characters",
            "Required": "true",
            "RequiredErrorMessage": "First Name is required",
            "RegexValidationExpression": "",
            "RegexErrorMessage": "",
            "Options": []
          },
          {
            "Key": "LastName",
            "Label": "Last Name",
            "EditPartialViewName": "FormItemInputPartial",
            "ReadOnlyPartialViewName": "FormItemLabelPartial",
            "Tooltip": "",
            "DefaultValue": "",
            "CssClass": "lastname",
            "IconCssClass": "glyphicon glyphicon-user",
            "IconOnLeft": "true",
            "VisibleOnRegistration": "true",
            "VisibleToUserOnProfile": "false",
            "EditableByUserOnProfile": "false",
            "VisibleOnAdminUserEdit": "false",
            "EditableOnAdminUserEdit": "false",
            "MaxLength": "100",
            "MaxLengthErrorMessage": "Last Name has a max length of 100 characters",
            "Required": "true",
            "RequiredErrorMessage": "Last Name is required",
            "RegexValidationExpression": "",
            "RegexErrorMessage": "",
            "Options": []
          },
          {
            "Key": "DateOfBirth",
            "Label": "Date of Birth",
            "EditPartialViewName": "FormItemDOBPartial",
            "ReadOnlyPartialViewName": "FormItemLabelPartial",
            "Tooltip": "",
            "DefaultValue": "",
            "CssClass": "dp",
            "IconCssClass": "glyphicon glyphicon-calendar",
            "IconOnLeft": "true",
            "VisibleOnRegistration": "true",
            "VisibleToUserOnProfile": "false",
            "EditableByUserOnProfile": "false",
            "VisibleOnAdminUserEdit": "false",
            "EditableOnAdminUserEdit": "false",
            "MaxLength": "30",
            "MaxLengthErrorMessage": "Date of Birth has a max length of 30 characters",
            "Required": "true",
            "RequiredErrorMessage": "Date of Birth is required yo!",
            "RegexValidationExpression": "",
            "RegexErrorMessage": "",
            "Options": []
          },
          {
            "Key": "MembershipNo",
            "Label": "Membership Number",
            "EditPartialViewName": "FormItemInputPartial",
            "ReadOnlyPartialViewName": "FormItemLabelPartial",
            "Tooltip": "",
            "DefaultValue": "",
            "CssClass": "memno",
            "IconCssClass": "glyphicon glyphicon-flash",
            "IconOnLeft": "true",
            "VisibleOnRegistration": "true",
            "VisibleToUserOnProfile": "true",
            "EditableByUserOnProfile": "false",
            "VisibleOnAdminUserEdit": "true",
            "EditableOnAdminUserEdit": "true",
            "MaxLength": "30",
            "MaxLengthErrorMessage": "Membership Number has a max length of 30 characters",
            "Required": "true",
            "RequiredErrorMessage": "Membership Number is required",
            "RegexValidationExpression": "",
            "RegexErrorMessage": "",
            "Searchable": "true",
            "Options": []
          },
          {
            "Key": "FavColor",
            "Label": "Favorite Color",
            "EditPartialViewName": "FormItemSelectPartial",
            "ReadOnlyPartialViewName": "FormItemLabelPartial",
            "Tooltip": "",
            "DefaultValue": "",
            "CssClass": "favcolor",
            "IconCssClass": "glyphicon glyphicon-eye-open",
            "IconOnLeft": "true",
            "VisibleOnRegistration": "true",
            "VisibleToUserOnProfile": "true",
            "EditableByUserOnProfile": "true",
            "VisibleOnAdminUserEdit": "true",
            "EditableOnAdminUserEdit": "true",
            "Required": "true",
            "RequiredErrorMessage": "Favorite Color is required",
            "MaxLength": "-1",
            "RegexValidationExpression": "",
            "RegexErrorMessage": "",
            "Searchable": "false",
            "Options": [
              {
                "Text": "Blue",
                "Value": "Blue"
              },
              {
                "Text": "Red",
                "Value": "Red",
                "Selected": "true"
              },
              {
                "Text": "Green",
                "Value": "Green"
              }
            ]
          },
          {
            "Key": "AuthorBio",
            "Label": "Short Bio",
            "EditPartialViewName": "FormItemTextAreaPartial",
            "ReadOnlyPartialViewName": "FormItemLabelPartial",
            "Tooltip": "",
            "DefaultValue": "",
            "CssClass": "bio",
            "VisibleOnRegistration": "true",
            "VisibleToUserOnProfile": "true",
            "EditableByUserOnProfile": "true",
            "VisibleOnAdminUserEdit": "true",
            "EditableOnAdminUserEdit": "true",
            "Required": "true",
            "RequiredErrorMessage": "Bio is required",
            "RegexValidationExpression": "",
            "RegexErrorMessage": "",
            "Searchable": "false",
            "Options": []
          }
        ]
      },
      {
        "TenantId": "92ef765c-c267-4edf-8eab-17de74da3bd2",
        "Properties": [
          {
            "Key": "NickName",
            "Label": "Nickname",
            "EditPartialViewName": "FormItemInputPartial",
            "ReadOnlyPartialViewName": "FormItemLabelPartial",
            "Tooltip": "",
            "DefaultValue": "",
            "CssClass": "firstname",
            "IconCssClass": "glyphicon glyphicon-user",
            "IconOnLeft": "true",
            "VisibleOnRegistration": "true",
            "VisibleToUserOnProfile": "false",
            "EditableByUserOnProfile": "false",
            "VisibleOnAdminUserEdit": "false",
            "EditableOnAdminUserEdit": "false",
            "MaxLength": "100",
            "MaxLengthErrorMessage": "First Name has a max length of 100 characters",
            "Required": "true",
            "RequiredErrorMessage": "First Name is required",
            "RegexValidationExpression": "",
            "RegexErrorMessage": "",
            "Searchable": "true",
            "Options": []
          }
        ]
      }   
    ]
  }

}
```

As you can see in the json we are wiring up [ProfilePropertySetContainer](https://github.com/CloudscribeNG/CloudscribeNG.UserProperties.Kvp/blob/master/src/CloudscribeNG.UserProperties/Models/ProfilePropertySetContainer.cs), which is just a class that has list of [UserPropertySet](https://github.com/CloudscribeNG/CloudscribeNG.UserProperties.Kvp/blob/master/src/CloudscribeNG.UserProperties/Models/UserPropertySet.cs), which is a class with a TenantId and a list of [UserPropertyDefinition](https://github.com/CloudscribeNG/CloudscribeNG.UserProperties.Kvp/blob/master/src/CloudscribeNG.UserProperties/Models/UserPropertyDefinition.cs) which is the class used to represent the configuration for each custom property. So UserPropertySet represents the tenant/site so each site can have it's own custom properties if needed. If UserPropertySet is found with the matching tenantid, then it will also check for one with "*" as the tenantid, and if that exists it will use that, otherwise the site will not have any custom user properties.

To get the list of UserPropertyDefinition for the current site, you can take a dependency on IProfileOptionsResolver or inject it into your views as is shown in the views above.

In the json above you can see that we have defined various custom user properties, including an example for select input and textarea. You can also see that some of the properties we have defined correspond to native properties on SiteUser such as FirstName, LastName, and DateOfBirth. This enables you to optionally require those properties on Registration, but as shown above they should be set as not visible or editable on the UserProfile or UserEdit pages since they already are rendered there as part of the view model. The screenshot below shows the register page corresponding to the configuration in the json above (click it for a larger view).

[![ ](/media/images/custom-registration-ws.jpg)](/media/images/custom-registration.jpg)

Notice how we can even configure icon css classes for each item. The solution also provides server-side validation for required, maxlength, and regular expression validation, and the included views also support client-side validation.

For those custom properties that are do not correspond to existing native properties, then the optional boolean "Searchable" configuration value determines whether that custom field will be included when searching for users via the 'User Management' administration screen.  If omitted, default value for this is false. When true, searching of the field is implemented case-insensitively. 

Properties are displayed in the order in which they are configured, and as mentioned above you can define custom views per item or use the included views that handle common scenarios.

## Rendering custom properties onto the User Management listing screen

If you want the values of some of your custom user properties to be displayed in the User Management listing, then you can extend the configuration settings as follows:

For each custom KVP property that you wish to include, add the 'VisibleOnUserListing' property:

```
{
"Key": "MembershipNo",
"Label": "Membership Number",
"EditPartialViewName": "FormItemInputPartial",
"ReadOnlyPartialViewName": "FormItemLabelPartial",
"Tooltip": "",
"DefaultValue": "",
"CssClass": "memno",
"IconCssClass": "fas fa-hashtag",
"IconOnLeft": "true",
"VisibleOnRegistration": "true",
"VisibleToUserOnProfile": "true",
"EditableByUserOnProfile": "false",
"VisibleOnAdminUserEdit": "true",
"EditableOnAdminUserEdit": "true",
"MaxLength": "30",
"MaxLengthErrorMessage": "Membership Number has a max length of 30 characters",
"Required": "true",
"RequiredErrorMessage": "Membership Number is required",
"RegexValidationExpression": "",
"RegexErrorMessage": "",
"Options": [],
"Searchable": "true",
"VisibleOnUserListing": "true"
},
```

Also, make sure that your solution is referencing the NuGet package: CloudscribeNG.Kvp.Views.BS5     

which contains some custom view overrides that are configured to extend the user listing table with your new properties.

Finally, add another property to your configuration, to specify that one of these custom views (UserAdmin/index_kvp.cshtml) should be used to render the user listing, instead of the normal default UserAdmin/index.cshtml

```
"ProfilePropertySetContainer": {
 "UserListingViewName" :  "index_kvp",
 "PropertySets": [
            {
```

Note that this only works to control the visibility of custom KVP properties - not 'native' properties already present in the cs_users table such as FirstName, LastName, and DateOfBirth.
