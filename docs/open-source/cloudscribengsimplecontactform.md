# CloudscribeNG.SimpleContactForm

The CloudscribeNG template includes an option to install a contact form which enables users to send a simple email message to a site administrator. 

Before the contact form will be displayed, there must be an SMTP server configured in the appsettings.json (or appsettings.Production.json) file, as follows:

```
    "SmtpOptions": {
        "Server": "smtp.yoursite.com",
        "Port": "25",
        "User": "noreply@yoursite.com",
        "Password": "your_password",
        "UseSsl": "false",
        "RequiresAuthentication": "true",
        "DefaultEmailFromAddress": "noreply@yoursite.com",
        "DefaultEmailFromAlias": "noreply@yoursite.com"
    },
```

The contact form also needs to be configured in the appsettings, as follows:

```
    "ContactFormSettings": {
        "Id": "default",
        "NotificationEmailCsv": "youradministrator@yoursite.com",
        "NotificationSubject": "Contact Form Submission",
        "CopySubmitterEmailOnSubmission": "true"
    },
```

**NotificationEmailCsv **specifies a comma-separated list of email addresses to which the form submission email will be sent.  

If no value is set for this, and if CloudscribeNG is configured to require administrator approval of new users, then contact form submissions will instead be sent to the list of email addresses configured to receive notification of new unapproved users (this is specified in the CloudscribeNG Security Settings).

**CopySubmitterEmailOnSubmission **is a true/false boolean that determines whether a copy of the form submission should be sent to the person submitting the form.
