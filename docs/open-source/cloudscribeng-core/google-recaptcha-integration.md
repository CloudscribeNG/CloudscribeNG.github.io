# Google reCAPTCHA integration

CloudscribeNG enables the protection of login and registration pages with Google reCAPTCHA. The CloudscribeNG [simple Contact form](/CloudscribeNGsimplecontactform) is also protected if included in the application. Only v2 reCAPTCHA is supported at present, using the "I'm not a robot" Checkbox or Invisible options.

Google reCAPTCHA can be managed at [https://www.google.com/recaptcha/admin](https://www.google.com/recaptcha/admin)

Create a "site" choosing the v2 option you prefer, then add the domain of your CloudscribeNG site. You can also add "localhost" for the reCAPTCHA to work in development.

Add the public and private keys to the CloudscribeNG Captcha Settings page (/siteadmin/captcha), and tick the box for "Use Invisible reCaptcha?" if that option was chosen. Choose whether to enable the reCAPTCHA mechanism on the login and/or registration pages.

![CloudscribeNG Google reCAPTCHA settings](/media/images/2022/10/16/CloudscribeNG-recaptcha-settings.png)

The settings may alternatively be configured in appsettings.Production.json.

Custom application components, such as a public form that might be susceptible to abuse by bots, can also be protected easily, if you study the registration and login implementations in our open source code.

NB if you create v3 keys and use them in a CloudscribeNG site with the "Use Invisible reCaptcha?" option ticked, it will appear to work; however it will not really be protecting the site as at present CloudscribeNG is not parsing the v3 reCAPTCHA score response.
