# Using the CloudscribeNG Newsletter

The CloudscribeNG Newsletter is a commercial add-on product, and is offered as an option when creating a new project using our project template for Visual Studio. 

### How to obtain licence files

Until a licence is purchased the Newsletter module will show a nagging prompt that includes a link to purchase and activate the module. If you would like to purchase and activate a licence before deploying to a production domain, you can construct the URL as follows (for .NET 6):

[https://www.CloudscribeNG.com/store/activate?token=87edc070-458a-41ec-affd-ee68f77d8888~6~**yourdomain.com**](https://www.CloudscribeNG.com/store/activate?token=87edc070-458a-41ec-affd-ee68f77d8888~6~yourdomain.com)

Replace 'yourdomain.com' with your production domain.

### How to add the Newsletter to an existing CloudscribeNG solution

If you need to add the Newsletter to a pre-existing project, you will need to follow the steps below.  

1. Add the following lines to your project's main .csproj file within an <ItemGroup> element (assuming your project is on .NET6 with Bootstrap 5.

Only add the KVP line if you have the KVP user properties installed, and altering as appropriate for your storage platform: MS SQL Server, MySQL, PostgreSQL):

```xml
    <PackageReference Include="CloudscribeNG.EmailQueue.CoreIntegration" Version="6.0.*" />
    <PackageReference Include="CloudscribeNG.EmailQueue.Data.EFCore.PostgreSql" Version="6.0.*" />
    <PackageReference Include="CloudscribeNG.Email.Templating.Data.EFCore.PostgreSql" Version="6.0.*" />
    <PackageReference Include="CloudscribeNG.EmailList.Web.Mvc" Version="6.0.*" />
    <PackageReference Include="CloudscribeNG.EmailList.Views.Bootstrap5" Version="6.0.*" />
    <PackageReference Include="CloudscribeNG.EmailList.CoreIntegration" Version="6.0.*" />
    <PackageReference Include="CloudscribeNG.EmailList.KvpUserProperties.Integration" Version="6.0.*" />
    <PackageReference Include="CloudscribeNG.EmailList.Data.EFCore.PostgreSql" Version="6.0.*" />
```

2. Run 'dotnet restore' to pull in these packages.

3. If you have not included CloudscribeNG Dynamic Authorisation Policies, then declare the following authorisation policy in your Config/Authorization.cs file:

```
options.AddPolicy("EmailListAdminPolicy",
authBuilder =>
{
   authBuilder.RequireRole("Administrators");
});
```

4. Add the following to CloudscribeNGFeatures.cs in the SetupDataStorage method, altering as appropriate for your storage platform:

```cs
services.AddEmailTemplateStorageMSSQL(connectionString);
services.AddEmailQueueStorageMSSQL(connectionString);
services.AddEmailListStorageMSSQL(connectionString);
```

5. Add the following line to Config/CloudscribeNGFeatures.cs in the method SetupCloudscribeNGFeatures (checking not to duplicate existing lines):

```cs
services.AddEmailListKvpIntegration(config);  // if you have the KVP user properties installed

services.AddEmailQueueBackgroundTask(config);
services.AddEmailQueueWithCloudscribeNGIntegration(config);
services.AddEmailRazorTemplating(config);
services.AddEmailListWithCloudscribeNGIntegration(config);
```

6. Add the following lines to Program.cs in the method  EnsureDataStorageIsReady 

```
EmailQueueDatabase.InitializeDatabaseAsync(scopedServices).Wait();
EmailTemplateDatabase.InitializeDatabaseAsync(scopedServices).Wait();
EmailListDatabase.InitializeDatabaseAsync(scopedServices).Wait();
```

7. Add a link to the Newsletter Administration page into navigation.xml somewhere within the SiteAdmin section of links:

```xml
                   <NavNode key="NewletterAdmin"
                      controller="EmailList"
                      action="Admin"
                      text="Newsletter Administration"
                    iconCssClass="fas fa-envelope"
                      componentVisibility="breadcrumbs,childtree,parenttree"
                      authorizationPolicy="EmailListAdminPolicy">
                        <Children>
                            <NavNode key="EmailLists"
                                    controller="EmailList"
                                    action="Lists"
                                    preservedRouteParameters="listId"
                                    text="Email Lists"
                                    componentVisibility="breadcrumbs,appmenu"
                                    authorizationPolicy="EmailListAdminPolicy">
                            <Children>
                                <NavNode key="EmailListEdit"
                                        controller="EmailList"
                                        action="EditList"
                                        preservedRouteParameters="listId"
                                        text="New List"
                                        componentVisibility="breadcrumbs"
                                        authorizationPolicy="EmailListAdminPolicy">
                                <Children />
                                </NavNode>
                                <NavNode key="EmailSubscribers"
                                    controller="EmailList"
                                    action="ListSubscribers"
                                    preservedRouteParameters="listId"
                                    text="Subscribers"
                                    componentVisibility="breadcrumbs"
                                    authorizationPolicy="EmailListAdminPolicy">
                                <Children>
                                    <NavNode key="EmailSubscriptionEdit"
                                        controller="EmailList"
                                        action="SubscriptionEdit"
                                        preservedRouteParameters="listId"
                                        text="Edit Subscription"
                                        componentVisibility="breadcrumbs"
                                        authorizationPolicy="EmailListAdminPolicy">
                                    <Children />
                                    </NavNode>
                                </Children>
                                </NavNode>
                            </Children>
                            </NavNode>
                            <NavNode key="EmailMessages"
                                        controller="EmailList"
                                        action="Messages"
                                        preservedRouteParameters="listId"
                                        text="Email Messages"
                                        componentVisibility="breadcrumbs,appmenu"
                                        authorizationPolicy="EmailListAdminPolicy">
                            <Children>
                                <NavNode key="EmailMessageEdit"
                                        controller="EmailList"
                                        action="EditMessage"
                                        preservedRouteParameters=""
                                        text="New Message"
                                        componentVisibility="breadcrumbs"
                                        authorizationPolicy="EmailListAdminPolicy">
                                <Children />
                                </NavNode>
                            </Children>
                            </NavNode>
                        </Children>
                    </NavNode>
```

On completion you should have the following:

	
- In Administration, a "Newsletter Administration" item than leads to /emaillist/admin from which you can administer Email Lists (each with a set of subscribers) and Email Messages (which can be sent to any List).
	
- The ability to send Messages to List subscribers.
	
- The ability to add a "Sign up..." form on your home page or elsewhere (if you want it - see below).
	
- The ability to customise the underlying template that is used for your Messages.

Before any emails will actually get sent out, you will need to have configured and tested your site's email settings, most likely in the SmtpOptions section of the appsettings.Production.json file.

### How to add the sign-up widget to a page

The Newsletter module includes a sign-up widget, which is implemented as a view component that can be displayed on a home page or elsewhere.

If your home page (or any other page) is not coming from CloudscribeNG SimpleContent, you can simply add this to your Razor view:

```cs
@await Component.InvokeAsync("EmailListSubscribe", new { })

@section Scripts {
    <script src="~/cr/js/jquery.unobtrusive-ajax.min.js"></script>
}
```

If your home page is coming from CloudscribeNG SimpleContent, you could add an override of the [PageBottomPartial.cshtml view](https://github.com/CloudscribeNG/CloudscribeNG.SimpleContent/blob/master/src/CloudscribeNG.SimpleContent.CompiledViews.Bootstrap5/Views/Page/PageBottomPartial.cshtml) (which is empty by default) by adding a file at this location in your solution \Views\Page\PageBottomPartial.cshtml containing the following:

```cs
@model CloudscribeNG.SimpleContent.Web.ViewModels.PageViewModel

@if (Context.Request.Path == "/" || Context.Request.Path == "/home")
{
    Html.Resource(@<script src="~/cr/js/jquery.unobtrusive-ajax.min.js"></script>, "js");
    @await Component.InvokeAsync("EmailListSubscribe", new { })
}
```

If you generate your site from the CloudscribeNG template, and select the option to use the Newsletter at install time, then this PageBottomPartial view will be created for you.

The widget relies on both the "EmailListSubscribe" view component, and a supporting jquery.unobtrusive-ajax reference. Note that it renders the widget only when the current page is the root homepage (though you can adapt it to your own purposes).

Note that if you are already importing jquery.unobtrusive-ajax into your page from somewhere else (e.g. the main _Layout page) then you don't want it twice: otherwise there is a chance the subscription form will create duplicate sign-ups when it is posted.

**Important note: **the widget only displays a sign-up form if you have already configured at least one newsletter list in your site with the 'Include in Sign Up Widgets' checkbox selected.. otherwise it will display nothing. Likewise, for a user who has authenticated to the CloudscribeNG site, if that user is already subscribed to all available email lists, then the widget will display nothing.

It is also possible to customize the appearance of the widget itself. For example, override the outer parts of the widget by adding a Razor view here:

\Views\Shared\Components\EmailListSubscribe\Default.cshtml

in which you can modify the default markup, which is as follows:

```cs
﻿@model CloudscribeNG.EmailList.Web.Mvc.ViewModels.EmailSubscribeViewModel
@inject IStringLocalizer<CloudscribeNG.EmailList.Web.EmailListResources> sr
<div class="card newsletter-subscribe-card">
    <div class="card-header text-light bg-dark p-1 ps-2">
        <h5 class=" m-0 p-0">
            <span class="fa fa-envelope"></span> @sr["Sign up for our newsletter!"]
        </h5>
    </div>
    <div id="emailsubcontainer" class="card-body pt-4">
        @await Html.PartialAsync("EmailListSignUpPartial", Model)
    </div>
</div>
```

Note that if you have configured reCAPTCHA in your site, the sign-up form will be protected.

Likewise, you can customise the inner markup of the form itself by overriding the [EmailListSignUpPartial view](http://github.com/exeGesIS-SDM/CloudscribeNG.Messaging/blob/master/src/CloudscribeNG.EmailList.Views.Bootstrap5/Views/Shared/EmailListSignUpPartial.cshtml).

### What if a user signs up more than once?

An authenticated user who has already subscribed to a newsletter will not see the "Sign up" form on the home page. However an unauthenticacted user will see the form, as CloudscribeNG doesn't know who they are. If a user signs up again, using an email that has already been used to subscribe, and has been verified, they will see a message like 

> 

Thanks for signing up! Your subscription is confirmed.

If the email has not been previously verified, the user will be sent an email with a link to verify their subscription. The first name and last name they entered the first time will be retained and used.

### Importing subscribers

On the page that lists the subscribers for a list (Administration > Newsletter Administration > Lists > Subscribers) there's an Import tool to load subscribers in bulk from a comma-separated values text file. The csv file must have field names on the first line as follows: Email,FirstName,LastName,IsVerified,IpAddress

Subsequent lines must contain the subscriber data, with at least the Email value present. If other values are included they must be in their right order, separated by commas, with each line ending in CrLf. If values for FirstName,LastName,IsVerified,IpAddress are omitted, the correct number of commas must still be present in each line, i.e. 4.

Note that the import process will not prevent duplicates being added.

### How to customise the underlying template that is used for your Messages

IN PREP
