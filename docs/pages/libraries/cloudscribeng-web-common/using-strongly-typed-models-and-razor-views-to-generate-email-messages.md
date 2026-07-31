# Using Strongly Typed Models and Razor Views to generate email messages

CloudscribeNG.Web.Common has a [ViewRenderer](https://github.com/joeaudette/CloudscribeNG/blob/master/src/CloudscribeNG.Web.Common/Razor/ViewRenderer.cs) that makes it easy to use Razor templates for email in ASP.NET Core. Normally Razor is used for MVC views to render html for web page requests. With our ViewRenderer we can render to a string and then use the string as the message body for email.

The "strongly typed" model can be a string or a complex model class. In CloudscribeNG.SimpleContent we have a [ProjectEmailService](https://github.com/joeaudette/CloudscribeNG.SimpleContent/blob/master/src/CloudscribeNG.SimpleContent.Web/Services/ProjectEmailService.cs) that uses it like this with a [CommentNotificationModel](https://github.com/joeaudette/CloudscribeNG.SimpleContent/blob/master/src/CloudscribeNG.SimpleContent.Models/CommentNotificationModel.cs) to send notifications about comments on blog posts:

```cs
public async Task SendCommentNotificationEmailAsync(
            IProjectSettings project,
            IPost post,
            IComment comment,
            string postUrl,
            string approveUrl,
            string deleteUrl
            )
        {
            var smtpOptions = GetSmptOptions(project);

            if (smtpOptions == null)
            {
                var logMessage = $"failed to send comment notification email because smtp settings are not populated for project {project.Id}";
                log.LogError(logMessage);
                return;
            }

            if (string.IsNullOrWhiteSpace(project.CommentNotificationEmail))
            {
                var logMessage = $"failed to send comment notification email because CommentNotificationEmail is not populated for project {project.Id}";
                log.LogError(logMessage);
                return;
            }

            if (string.IsNullOrWhiteSpace(project.EmailFromAddress))
            {
                var logMessage = $"failed to send comment notification email because EmailFromAddress is not populated for project {project.Id}";
                log.LogError(logMessage);
                return;
            }

            var model = new CommentNotificationModel(project, post, comment, postUrl);
            var subject = "Blog comment: " + post.Title;

            string plainTextMessage = null;
            string htmlMessage = null;
            var sender = new EmailSender();

            try
            {
                try
                {
                    htmlMessage 
                        = await viewRenderer.RenderViewAsString<CommentNotificationModel>("CommentEmail", model);
                }
                catch(Exception ex)
                {
                    log.LogError("error generating html email from razor template", ex);
                    return;
                }
                
                await sender.SendEmailAsync(
                    smtpOptions,
                    project.CommentNotificationEmail, //to
                    project.EmailFromAddress, //from
                    subject,
                    plainTextMessage,
                    htmlMessage,
                    comment.Email //replyto
                    ).ConfigureAwait(false);
            }
            catch (Exception ex)
            {
                log.LogError("error sending comment notification email", ex);
            }

        }
```

This example from CloudscribeNG Core [SiteEmailMessageSender](https://github.com/joeaudette/CloudscribeNG/blob/master/src/CloudscribeNG.Core.Web/Components/Messaging/SiteEmailMessageSender.cs), uses a single string as a model and it renders both html and plain text message bodies which are both included in the same message.

```cs
public async Task SendAccountConfirmationEmailAsync(
            ISiteContext siteSettings,
            string toAddress,
            string subject,
            string confirmationUrl)
        {
            var smtpOptions = await GetSmptOptions().ConfigureAwait(false);
            if (smtpOptions == null)
            {
                var logMessage = $"failed to send account confirmation email because smtp settings are not populated for site {siteSettings.SiteName}";
                log.LogError(logMessage);
                return;
            }
            
            var sender = new EmailSender();
            try
            {
                var plainTextMessage
                = await viewRenderer.RenderViewAsString<string>("EmailTemplates/ConfirmAccountTextEmail", confirmationUrl).ConfigureAwait(false);

                var htmlMessage
                    = await viewRenderer.RenderViewAsString<string>("EmailTemplates/ConfirmAccountHtmlEmail", confirmationUrl).ConfigureAwait(false);

                await sender.SendEmailAsync(
                    smtpOptions,
                    toAddress,
                    smtpOptions.DefaultEmailFromAddress,
                    subject,
                    plainTextMessage,
                    htmlMessage).ConfigureAwait(false);
            }
            catch (Exception ex)
            {
                log.LogError("error sending account confirmation email", ex);
            }

        }
```

We normally think of Razor as a way to generate html, and it is, but I have also had success using it for generating plain text message bodies. The thing to keep in mind is that Razor will normally html encode what you render, and we don't want that for plain text, so you use the @Html.Raw method which renders without html encoding because it assumes the passed in content is already html encoded. Below is an example of a plain text Razor template, the model is a string which is an url that we don't want to be html encoded since it is plain text:

```html
@using CloudscribeNG.Core.Models
@inject SiteContext Site
@model string 

@{
    Layout = "_LayoutTextEmailNotification";
    ViewData["Title"] = "Account Approved";
}

Your account at @Site.SiteName has been approved. 
You may now login by clicking here: @Html.Raw(Model)
```

You can refer to the email templates for [CloudscribeNG.SimpleContent](https://github.com/joeaudette/CloudscribeNG.SimpleContent/tree/master/src/CloudscribeNG.SimpleContent.Web.Views.Bootstrap3/Views/Shared), and for [CloudscribeNG.Core](https://github.com/joeaudette/CloudscribeNG/tree/master/src/CloudscribeNG.Core.Web.Views.Bootstrap3/Views/Sys/Shared) to see more examples of templates or get a better understanding.
