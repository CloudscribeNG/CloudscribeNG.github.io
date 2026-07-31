# Adding JavaScript and CSS to Pages

Developers might wish to add their own custom JavaScript and CSS to a SimpleContent page. For example, you may want to build your own SPA application, perhaps with JavaScript that talks to a custom web API.

You can easily put any required raw HTML into your page content, using the source view of the editor. You could perhaps also paste raw inline JavaScript and CSS blocks in there too - but that would not be ideal. Depending on the configuration of the site, such inline script and style blocks might be stripped out by the editor on saving, or (if your site has a strict Content Security Policy in place which disallows 'UnsafeInline') they might be prevented from running when the page is loaded.

In addition, it is preferable that CSS should go at the top of the page in the HEAD element, and JavaScript should go at the very bottom of the page.

SimpleContent makes it easy to add JavaScript and CSS to pages, via a Developer Tools screen, but since this is an advanced and powerful feature, it is disabled by default.

To enable this feature you can add a configuration setting in appsettings.json like this:

```json
"PageEditOptions": {
"AlwaysShowDeveloperLink": false,
"DeveloperAllowedRole": "Administrators"
 }
```

You could configure it to always show the Developer Tools button for anyone who has edit permission ("AlwaysShowDeveloperLink": true), or you can specify a DeveloperAllowedRole, so that if the user is in that specified role **and** has page edit permissions, they will see the Developer Tools button on the edit page.

In the Developer Tools page, you have the ability to add direct URL references to any JS and CSS resources you want to include (e.g. external CDN references, if you wish), plus - from CloudscribeNG v.8.4 onwards - also the ability to specify your own custom block of 'raw' JavaScript for inclusion into the page.

Note that when you add URL references to JS and CSS resources, you can specify the sort order in which those references are rendered onto the page.  However, when you supply your own block of free-text raw JS, this will always be rendered onto the page **after **any URL-type JS references.  This makes sense if you wanted, for example, to place a URL reference to something like DataTables.js, and then initialize it from your own inline script.

Note that when inputting your own custom JS, you do not need to enclose it within script tags. 

Any custom JS that you specify directly in the Raw JavaScript box will be subject to validation, to guard against input of any potentially malicious commands.  Any validation fails will be displayed when the Save JavaScript submit button is clicked.

![ ](/media/images/image_2025-08-20t10-30-51-2-ws.png)

 

---

**Troubleshooting**:  if your specified Raw JavaScript does not get rendered back out onto your page, and if you are working with a CloudscribeNG site that has been upgraded from pre-v.8.4, then check whether you have a local view override of Page/Index.cshtml in your WebApp.

If so, that will need to be edited to include provision for rendering your Raw JavaScript:

```
@if (Model.CurrentPage?.Script != null && !string.IsNullOrWhiteSpace(Model.CurrentPage.Script))
{
    <script src="@Url.Action("InlineScript", "Page", new { pageId = Model.CurrentPage.Id, v = Model.CurrentPage.LastModified.Ticks })"></script>
}
```
