# Content Cloning

Occasionally, in a multi tenant scenario, you may wish to clone the content between one site and another. For example you could create a tenant site that you use a bit like a template - it may contain a set of simple content pages and/or blog posts already set up that you would want to be included with any new tenant sites that you create. The content cloning tool allows you to take the content from one tenant site, and clone it to another site. There are tick box options available in the tool to specify what you want transferred and there are rules that determine if it's possible.

Options and rules:

	
- Clone Content Setting - this will clone all of the configuration defined in the 'Content Settings' administration page from the source to the destination site. This option has no rules that restrict it's use.
	
- Clone Pages - this will clone all of the simple content pages from the source site to the destination site, *but* only if the destination site does not already have any pages defined. The pages' parent/child relationship will be maintained.
	
- Clone Blog Posts - this will clone all of the blog posts from the source site to the destination site, *but* only if the destination site does not already have any blog posts.

Some things to note:

The tools does not attempt to rewrite any fully qualified URLs that may be embedded within the simple content pages (or posts) that may point to other pages - i.e. if you are using folder based tenants, if you clone a page from site /site1 to /site2, any links in the new pages on /site2 will still point to /site1. A way to mitigate this is to make sure that any links within your simple content pages are relative links that don't tie them to a specific folder based tenant. Similarly for hostname based tenants, make sure the URLs are relative and not fully qualified with a domain name.

The tool does not move any uploaded content or assets (such as images) between tenant sites, however in practice, any embedded content in your page should continue to work even if they are being referenced from the source site.
