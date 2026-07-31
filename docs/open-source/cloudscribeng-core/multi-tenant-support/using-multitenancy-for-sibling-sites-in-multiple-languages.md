# Using multitenancy for sibling sites in multiple languages

CloudscribeNG's multitenancy support can be an ideal solution when you want to publish two or more 'sibling' sites, with the same layout and content, but in different languages.

Note that this approach goes a long way beyond the ability to internationalize individual strings within the user interface by the use of ResX resource files.  In this case, we are talking about duplicate sites whose full navigation structure, content pages and blog posts are all fully replicated in different languages. Ideally in this case you want a simple 'switcher' button that can take the user from their current page (or post) to the same matching page (or post) in a different language.

To achieve this, you can create a multi-tenant CloudscribeNG implementation, using one tenant site for each supported language. Then, when creating pages or blog posts within the CloudscribeNG SimpleContent system, you can use the CorrelationKey property of these pages / posts to indicate that they are counterparts to one another, containing the same information, but in different languages.

## Implementation 

This is a bilingual example, where you would implement a simple button to switch to the 'other' language. 

You would take a very similar approach to supporting a greater number of languages, but you would obviously need a more sophisticated switching solution in the user interface.

### 1 - Configure multitenancy

From the CloudscribeNG installation template, select the option to support multitenancy (you can choose either foldername or hostname multitenancy).

Create two new sibling tenant sites within the CloudscribeNG implementation, with names that you consider appropriate to your two supported languages   

(or in practice you could just create one new site, and use the main 'master' site to support your 'main' laguage).

You are likely to want to configure CloudscribeNG to use [Related Sites Mode](https://www.CloudscribeNG.com/multi-tenant-support) - so that user account credentials are shared between these two sibling sites  

(there would be little point implementing a 'switcher' button between these two sites if the user only had an account in one but not the other).

### 2 - Make the CorrelationKey property in Blog posts and Pages editable via the user interface

In your web application, make local copies of the CloudscribeNG partial views, stored in the correct locations (/Views/Page or /Views/Blog) so that they act as overrides to the underlying CloudscribeNG versions:  

/Blog/EditCorrelationKeyPartial.cshtml  

/Blog/EditWithTemplateCorrelationKeyPartial.cshtml 

and

/Page/EditCorrelationKeyPartial.cshtml   

/Page/EditWithTemplateCorrelationKeyPartial.cshtml 

(the master copies of these are located [here](https://github.com/CloudscribeNG/CloudscribeNG.SimpleContent/tree/master/src/CloudscribeNG.SimpleContent.CompiledViews.Bootstrap4/Views)).

Follow the instructions in these files to comment out one section, and un-comment another, so that this field becomes editable via the user interface.

Now when you create pairs of matching pages or blog posts in the two sibling sites, with the same content expressed in different languages, you should populate this CorrelationKey field with the same matching text string for each of the pair.  This string can by anything you like, so long as it serves as a unique key to cross-reference your pairs of pages.

### 3 - Create a Language Service C# class

This service needs to be capable of using the current HttpContext to examine the path of the HTTP request, read its slug (the URL segment that identifies the current page), and then look for the slug of a matching page in the other tenant site that possesses the same correlation key.

This is just some simplified psudo-code, to help to illustrate the general approach:

```
using CloudscribeNG.Core.Models;
using CloudscribeNG.SimpleContent.Models;

// use whatever namespace you like here...

public class LanguageSwitcherService
{
    private SiteContext currentSite;
    private IPageQueries pageQueries;

    // Dependency injection of a couple of CloudscribeNG services...

    public LanguageSwitcherService(SiteContext currentSite, IPageQueries pageQueries)
    {
        this.currentSite = currentSite;
        this.pageQueries = pageQueries;
    }

    public async Task<string> GetOtherSiteUrl(HttpContext context)
    {        
        var path = context.Request.Path(); 
        var slug = path.Replace("/", "");
        var currentPage = await pageQueries.GetPageBySlug(currentSite.Id, slug);

        // find a page on the 'other' site that has the same correlation key as the current page.
        // otherSiteId (the Guid of the other language tenant to which you wish to link) may need to come from configuration, or be inferred from a look-up query.
        // Note here that CloudscribeNG's IPageQueries contains a useful query GetPageByCorrelationKey.
        var otherPage = await pageQueries.GetPageByCorrelationKey(otherSiteId, currentPage.CorrelationKey);
        return "/" + otherPage.Slug;
    }

    public string GetOtherSiteName()
    {
        // you would probably look this up from configuration, 
        // or figure out some other way to return the appropriate language label, such as "Francais" or "Cymraeg" etc.
    }
}
```

  

In reality the code above is far too simplistic: you will have a lot more work to do in creating this service, since you have to consider blog post routes as well as page routes, preserve any query string parameters, and if you are using hostname multitenancy then you need to modify the returned Url to refer to the other domain name. You will also need to consider what to do if no page with the matching CorrelationKey is found, and other similar error conditions.  

In future we may consider further development to supply a more fully-featured and configuration-driven LanguageSwitcher component... to save you having to write your own.

### 4 - Register your LanguageSwitcherService into the DI chain

```
services.AddScoped<LanguageSwitcherService, LanguageSwitcherService>();
```

### 5 - Create a Language Switcher button for the user interface

Most likely you will want to insert this into the _layout.cshtml file for your theme, in the header or footer.

The button will make use of methods provided by the DI-injected LanguageSwitcherService in order to render a link to the matching page / post of the sibling site.

```
@inject LanguageSwitcherService  langService

var otherUrl = await langService.GetOtherSiteUrl(Context);
var otherSiteName = langService.GetOtherSiteName();

<a class="btn" id="languageSwitcher" href="@otherUrl">@otherSiteName</a>
```

## Other possible uses of the CorrelationKey field 

Note that the usefulness of the CorrelationKey field is not limited to supporting multiple languages.  Depending on how you write your 'switcher' service above, this field could be used for any case where you need to cross-reference matching pages or posts - either in a multitenant implementation or even between pages and posts within a single tenant. For example, you could have a set of content pages containing the text of a book, and a matching set of blog posts providing commentary / criticism on that text, and then use the correlation key to swap back and forth between them.
