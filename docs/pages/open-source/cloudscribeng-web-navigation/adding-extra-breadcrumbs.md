# Adding Extra Breadcrumbs

You may find that sometimes you need to add breadcrumbs for urls that are conceptually child items, like a detail page for an item in a list, but the detail page is not in the tree of navigation nodes. For example in SimpleContent Blog feature we wanted to show breadcrumbs with the blog post title, but we don't want to add every blog post to the menu tree, after all over time we might have thousands of posts and the tree would get very big, and that would be a lot of overhead just to add a breadcrumb. To solve this problem we introduced the [TailCrumbUtility](https://github.com/joeaudette/CloudscribeNG.Web.Navigation/blob/master/src/CloudscribeNG.Web.Navigation/TailCrumbUtility.cs) in CloudscribeNG.Web.Navigation, which enables us to add the breadcrumb for a blog post without adding the blog post to the tree.

You can see how it is used, from this code example from the blog controller Post action method in CloudscribeNG.SimpleContent

```cs
var breadCrumbHelper = new TailCrumbUtility(HttpContext);
breadCrumbHelper.AddTailCrumb(result.Post.Id, result.Post.Title, currentUrl);
```

This works by adding the needed breadcrumb info to the HttpContext items collection, and the NavigationViewModel detects it and adds the breadcrumb.

But there is one more problem to solve to make it work. The problem is that the navigation will never show breadcrumbs if it does not find a menu item for the current url, What we really want is a breadcrumb with a parent that links to the blog like this:

[![ ](/media/images/blog-tailcrumb-ws.jpg)](/media/images/blog-tailcrumb.jpg)

So what we need is for the Blog Index page (which is conceptually the parent of the post) to be found as the current node, even though the post url is not the url for the blog index. So by default the current node is null because it was not found, and we just need to help it find the blog index node (which is in the menu tree) as the current node. To make that possible the [IFindCurrentNode](https://github.com/joeaudette/CloudscribeNG.Web.Navigation/blob/master/src/CloudscribeNG.Web.Navigation/IFindCurrentNode.cs) interface was added to CloudscribeNG.Web.Navigation. You can implement and inject as many of those as you need, and in the case where the current node is null (ie not found by the default logic) the NavigationViewModel will invoke each available implementation of IFindCurrentNode until one finds and returns a node. There are no provided implementations of that included in CloudscribeNG.Web.Navigation because that would be a project specific solution. We implemented one in CloudscribeNG.SimpleContent as [NavigationBlogNodeFinder](https://github.com/joeaudette/CloudscribeNG.SimpleContent/blob/master/src/CloudscribeNG.SimpleContent.Web/Services/NavigationBlogNodeFinder.cs), which you can study if you need to implement your own. Basically it inspects the current url and determines if it is an url for a blog post, if so it creates a matching function that can find the blog index node, and passes the function into the Find method of the tree root node, so it will iterate down the tree recursively applying the match function until it returns true or runs out of nodes to compare.

The NavigationViewModel makes the TailCrumbs available to the view, you can see it in the [BootstrapBreadcrumbs.cshtml](https://github.com/joeaudette/CloudscribeNG.Web.Navigation/blob/master/src/CloudscribeNG.Web.Navigation/Views/Shared/Components/Navigation/BootstrapBreadcrumbs.cshtml) view:

```html
@using CloudscribeNG.Web.Navigation
@model NavigationViewModel
@if (Model.CurrentNode != null && Model.ParentChain.Count > 1)
{
    <ul class="breadcrumb">
    @foreach (var node in Model.ParentChain)
    {
        if (!Model.ShouldAllowView(node)) { continue; }
        if (node.EqualsNode(Model.CurrentNode))
        {
            if (Model.TailCrumbs != null)
            {
                <li><a href="@Url.Content(Model.AdjustUrl(node))">@Model.AdjustText(node)</a><span class="divider"></span></li>
            }
            else
            {
                <li class="active">@Model.AdjustText(node)</li>
            }
        }
        else
        {
            <li><a href="@Url.Content(Model.AdjustUrl(node))">@Model.AdjustText(node)</a><span class="divider"></span></li>
        }
    }
    @if (Model.TailCrumbs != null)
    {
        foreach (var n in Model.TailCrumbs)
        {
            <li class="active">@n.Text</li>
        }
    }
    </ul>
}
```

TailCrumbs is implemented as a list but we only needed to add one for the given post, and that is all that is expected. You can see the breadcrumbs view is adding the active css class because it is expected that there will be one and only one tail crumb and that it corresponds to the current url. If you for some reason need to add more than one tail crumb per request, you would probably need a custom view. As noted above the tail crumb info is added to the HttpContext items collection which only exists for the lifetime of the request.

In this example I describe how we solved this problem for the blog, but the same solution would work for any kind of list detail scenario like a product list that links to product detail pages, could do the same thing and find the product list node for the current node.
