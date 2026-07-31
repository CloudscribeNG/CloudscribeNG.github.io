# Registration Page Configuration

The CloudscribeNG registration experience can be configured in the Administration menu > Register Page Info (found at /siteadmin/registerpageinfo ).

A registration 'pre-amble' can be added, with any content: this will appear above the registration form. You might find this useful to explain any social sign in options, your password policy, etc.

A 'Registration Agreement/Terms of Use' can also be defined. If this is not empty, registering users will be asked to tick a box to indicate their acceptance of the terms and will not be able to proceed without doing so.

In addition, there is an option to 'Require Users To Accept Changed Agreement?'. If ticked, this will prompt users to accept revised terms when they login after any change to the Registration Agreement text. It is advisable to be confident in the text before enabling this option.

![ ](/media/images/image_2023-06-16t20-57-29-1.png)

CloudscribeNG also makes this registration agreement visible at /registrationagreement so it may be desirable to include a link to this address in the site footer. Our Visual Studio template includes this footer link, for you to use or not as suits your site.

If you have the CloudscribeNG Newsletter module, tick boxes will appear on your registration form allowing users to sign up to any Newsletters that have been flagged as 'Include in Sign Up Widgets' = Yes!

The registration page can be protected with reCAPTCHA if desired, by entering appropriate configuration in Administration > Captcha Settings ([find out more about configuring reCAPTCHA](/google-recaptcha-integration))
