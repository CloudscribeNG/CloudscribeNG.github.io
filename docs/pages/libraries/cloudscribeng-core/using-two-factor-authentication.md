# Using Two Factor Authentication

CloudscribeNG Core has built in support for Two Factor Authentication, which provides an extra level of security for web application user accounts. The idea behind two factor authentication is to provide a way to keep your account secure even if someone manages to get your account credentials such as email address and password, with two factor authentication enabled they would still not be able to login to your account without the second factor.

There have been many high profile cases of data breaches in the news where hackers have managed to get user account data often including email addresses and passwords. Even if the passwords are encrypted or hashed, given enough time and computing power hackers can often crack the passwords. Also, we often don't hear about big data breaches until long after they have happened. So having another layer of protection is a good thing and highly recommended.

## Start with a good password

Two factor authentication is an extra security step but you should start with good practices for your passwords. There are some basic rules you should keep in mind about using passwords.This is not a comprehensive or exhaustive list of best practices, it is just a few essential things to keep in mind when creating passwords.

	
- Always use strong passwords, they should be long, the longer the better, consider using long sentences.
	
- Passwords should not be constructed from things that are easy to guess or information that people can find out about from your social media. For example don't use the name of your child or wife or pet, or birth dates or your car or favorite sports teams or players or anything like that that hackers can guess from things you share online.
	
- Don't use the same password for different accounts, especially for your most important accounts such as your email provider and your bank. Remember that if someone hacks your email account they can then often get your password for sites you use from password reset or recovery since the recovery links will get sent to your email. So it is very important to use strong passwords especially for your email accounts.

## Enabling Two Factor Authentication in CloudscribeNG Core

For websites and applications powered by CloudscribeNG Core, it is relatively straightforward to enable two factor authentication. Enabling two factor authentication is something users have to do for themselves, it is not something that you as a website administrator can do on their behalf.

Start by installing an authenticator app like Microsoft Authenticator for [Android](https://go.microsoft.com/fwlink/?Linkid=825072) and [iOS](https://go.microsoft.com/fwlink/?Linkid=825073) or Google Authenticator for [Android](https://play.google.com/store/apps/details?id=com.google.android.apps.authenticator2&hl=en) and [iOS](https://itunes.apple.com/us/app/google-authenticator/id388497605?mt=8). Note that you can use either one of those with any account, you are not limited to using a Google or Microsoft email account.

I used the Microsoft one, after installing the app it would not let me use it without first enabling a pin on my phone which is a good thing. If you are going to use your phone to secure a web account then your phone should be secured as well in case it gets lost or stolen.

[![ ](/media/images/ms-authnticator-0-ws.png)](/media/images/ms-authnticator-0.png)

Now that the app is installed login to your CloudscribeNG Core web site and click either the "hello" message or the gravatar/avatar link to get to the Account Management page.

![ ](/media/images/cs-accountlink.jpg)

From there you will see the link for two factor authentication.

[![ ](/media/images/cs-account-index-ws.jpg)](/media/images/cs-account-index.jpg)

Click the two-factor authentication link and you will see.

[![ ](/media/images/cs-account2fa1-ws.jpg)](/media/images/cs-account2fa1.jpg)

Click the Add authenticator button and you will see something like this:

[![ ](/media/images/cs-account2fa2-ws.jpg)](/media/images/cs-account2fa2.jpg)

Now go back to the authenticator app on your phone and click the Add account button

[![ ](/media/images/ms-authnticator-1-ws.png)](/media/images/ms-authnticator-1.png)

Choose "Other" as the account type

[![ ](/media/images/ms-authnticator-12-ws.png)](/media/images/ms-authnticator-12.png)

Then the app will ask to use your camera, which you should allow. Then simply aim it at the QR code on the web page.

[![ ](/media/images/ms-authnticator-2-ws.png)](/media/images/ms-authnticator-2.png)

Once it focuses it will automatically scan the QR code which provides it a secret it can use to generate codes for two factor authentication. The codes will change every 30 seconds. Now type the code into the "Verification Code" input in the web page to enable two factor authentication with the Authenticator app. Next it will show you some recovery codes that you can save in a text file in a safe location. If your phone were lost or stolen you could still login by using one of the recovery codes. It generates 10 codes and you can use any one of them one time only to login without the authenticator app. You can also reset them at any time. Resetting the recovery codes invalidates any previously generated recovery codes.

[![ ](/media/images/cs-account2fa3-ws.jpg)](/media/images/cs-account2fa3.jpg)

After saving the codes somewhere if you go back to the main two factor screen it will show this:

[![ ](/media/images/cs-account2fa4b-ws.jpg)](/media/images/cs-account2fa4b.jpg)

If your phone was lost or stolen you would want to reset the authenticator key and configure the app again on your new phone. You can also reset your recovery codes or disable two factor authentication at any time.

Now that you have enabled two factor authentication, if you logout and then login again you will be asked for the code which is updated every 30 seconds on your phone.

[![ ](/media/images/cs-account2fa5-ws.jpg)](/media/images/cs-account2fa5.jpg)

On your home computer you can check the box to remember this machine then you won't have to enter the code to login from that machine but if you try to login from a different machine or different web browser, you will need to enter the code.

Note that two factor authentication works seamlessly with [Social Authentication ](/docs/using-social-authentication)and with [IdentityServer integration](/docs/identityserver-integration).

## Troubleshooting

Given the very short 30 second lifetime of the codes generated by the authenticator app, it is very important to have accurate time on the web server. When I first tried it on one of my virtual machines the codes were failing because the time on the server was off by 2 minutes. In looking at my server it seemed to correctly be using a time service to sync the time, but for some reason it was off by 2 minutes compared to my local machine. After some research I found some Microsoft documentation about virtual machines:

> 

"To disable the Hyper-V time synchronization provider, shut down the VM and clear the Time synchronization check box under Integration Services."

So I contacted my host and asked him to see if that was enabled and if so to disable it. Sure enough, that was the problem. After re-syncing the time on the server it now showed the same time as my local machine and the codes worked.
