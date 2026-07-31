# CloudscribeNG.Web.Pagination

CloudscribeNG.Web.Pagination is an ASP.NET Core MVC TagHelper for paginated lists. We use it extensively in CloudscribeNG Core and CloudscribeNG SimpleContent, but it has no other "CloudscribeNG" dependencies itself, and you can use it in your projects even if you are not using other CloudscribeNG components. This pager was based heavily on the work of Martijn Boland in the older [MVCPaging project](https://github.com/martijnboland/MvcPaging).

## Installation

Note that if you are already using CloudscribeNG Core or CloudscribeNG SimpleContent, you don't need to install CloudscribeNG.Web.Pagination separately, it is already included as a dependency. If you are not using either of those, you can install CloudscribeNG.Web.Pagination by editing your .csproj file, and adding a package reference like this:

```xml
<PackageReference Include="CloudscribeNG.Web.Pagination" Version="2.1.*" />
```

Also if you are not already using the "big" CloudscribeNG features then you need to add a line in the ConfigureServices method of your Startup.cs file like this:

```cs
services.AddCloudscribeNGPagination();
```

and in your _ViewImports.cshtml file you need this:

```html
@addTagHelper "*, CloudscribeNG.Web.Pagination"
```

That is all that is needed for the UI installation. However for convenience there is also available a generic [PagedResult of T](https://github.com/joeaudette/CloudscribeNG.Web.Pagination/blob/master/src/CloudscribeNG.Pagination.Models/PagedResult.cs) class that you can use in your data retrieval to return a list of any type along with the needed pagination info such as total items, page size, and current page. Naturally you would not want a dependency in your data storage layer to a UI component like the PagerTagHelper, so the PagedResult of T is actually in a separate nuget package named CloudscribeNG.Pagination.Models. You can add a package reference in your data storage project without brigning in any unwanted web stuff, like this:

```xhtml
<PackageReference Include="CloudscribeNG.Pagination.Models" Version="1.0.*" />
```

Some example code showing how to return a PagedResult of T using EntityFramework Core is shown below. This example returns a PagedResult of an EmailList class in one of my custom projects.

```cs
public async Task<PagedResult<EmailList>> GetLists(
	string projectId,
	int pageNumber,
	int pageSize,
	CancellationToken cancellationToken = default(CancellationToken)
	)
{
	cancellationToken.ThrowIfCancellationRequested();

	int offset = (pageSize * pageNumber) - pageSize;

	var query = db.EmailLists.OrderBy(x => x.Title)
		.Select(p => p)
		.Skip(offset)
		.Take(pageSize)
		;

	var result = new PagedResult<EmailList>();
	result.Data = await query.AsNoTracking().ToListAsync(cancellationToken);
	result.TotalItems = await db.EmailLists.CountAsync();
	result.PageNumber = pageNumber;
	result.PageSize = pageSize;

	return result;

}
```

Note that you must populate the total items, which requires a second query since the first query is using skip and take and therefore not getting all rows, so you can't just use the count of the list it returns. The above is just example code to give you an idea how you can use it in your own projects. In some cases you could even use the PagedResult of T as the viewmodel for your view, but often you will need other things in your viewmodel so you can just add the PagedResult of T as a property on your viewmodel and then also have other properties. Use of PagedResult of T makes it easier and more consistent to pass the needed info to the PagerTagHelper as shown below.

## Using The PagerTagHelper

Now that everything is installed, you can use the PagerTagHelper in your views like this:

```html
<cs-pager cs-paging-pagesize="@Model.PageSize"
  cs-paging-pagenumber="@Model.PageNumber"
  cs-paging-totalitems="@Model.TotalItemCount"
  cs-pagenumber-param="page"
  asp-controller="YourController"
  asp-action="YourAction"></cs-pager>
```

Note that you need to pass in the PageSize, current PageNumber, and TotalItems. The pager links will only be rendered if needed, ie if there exists more than one page of data as determined by the page size and total items. Note that the above example uses separate model properties for each of the needed parameters PageSize, PageNumber, and TotalItems. If you are using the PagedResult of T mentioned above as a property on your viewmodel you would use syntax like this:

```html
<cs-pager asp-action="ListSubscribers"
  asp-controller="EmailList"
  asp-route-pagesize="@Model.Subscribers.PageSize"
  cs-paging-pagenumber="@Model.Subscribers.PageNumber"
  cs-paging-totalitems="@Model.Subscribers.TotalItems"
  asp-route-listId="@Model.EmailList.Id"
  asp-route-q="@Model.Q"
  asp-route-sortmode="@Model.SortMode"
  ></cs-pager>
```

In the above example there is a little more complexity in that we have several other route parameters, one for an email list id, one for a search term "Q", and one for a sort mode parameter. The Subscribers property of the view model is a PagedResult of EmailListSubscribers. For more clarity, the viewmodel used for the above sample looks like this:

```cs
using CloudscribeNG.Pagination.Models;

public class EmailSubscriberListViewModel
{
	public EmailSubscriberListViewModel()
	{
		Subscribers = new PagedResult<EmailListSubscription>();
	}

	public string Q { get; set; }

	public int SortMode { get; set; } = 0;

	public EmailList EmailList { get; set; }

	public PagedResult<EmailListSubscription> Subscribers { get; set; }
}
```

## Additional PagerTagHelper attributes

The PagerTagHelper supports a number of configurable properties, shown below with their default values:

	
		
			Property
			Default
			Notes
		
		
			cs-paging-pagesize
			10
			
		
		
			cs-paging-pagenumber
			1
			
		
		
			cs-paging-totalitems
			1
			
		
		
			cs-preserve-ambient-querystring
			true
			when true will preserve all query string parameters in the current url without having to explicitly add asp-route-[parametername]. Set to false if you don't want this behavior.
		
		
			cs-paging-maxpageritems
			10
			
		
		
			cs-pagenumber-param
			pageNumber
			
		
		
			cs-show-first-last
			false
			
		
		
			cs-first-page-text
			<
			
		
		
			cs-first-page-title
			First Page
			
		
		
			cs-last-page-text
			>
			
		
		
			cs-last-page-title
			Last Page
			
		
		
			cs-previous-page-text
			«
			
		
		
			cs-previous-page-title
			Previous page
			
		
		
			cs-next-page-text
			»
			
		
		
			cs-next-page-title
			Next page
			
		
		
			cs-previous-page-html
			
			pass in raw html as shown in the example above, you must provide the title and your html must have a link with href='#'
		
		
			cs-next-page-html
			
			same as above
		
		
			cs-pager-ul-class
			pagination
			
		
		
			cs-pager-li-current-class
			active
			
		
		
			cs-pager-li-non-active-class
			disabled
			
		
		
			cs-ajax-target
			empty
			should be the id of the html element to target for ajax updates
		
		
			cs-ajax-mode
			replace
			
		
		
			cs-ajax-success
			empty
			a callback
		
		
			cs-ajax-failure
			empty
			a callback
		
		
			cs-ajax-loading
			empty
			the #id of an image ie animated gif to show while loading
		
		
			cs-ajax-loading-duration
			empty
			corresponds to data-ajax-loading-duration
		
	

For the next page and previous page you can optionally pass in html fragments. An example below shows how to use glyphicons for the next and previous pager links:

```html
<cs-pager cs-paging-pagesize="@Model.PageSize"
  cs-paging-pagenumber="@Model.PageNumber"
  cs-paging-totalitems="@Model.TotalItemCount"
  cs-pagenumber-param="page"
  cs-previous-page-html="<a href='#' title='Previous page'><i class='glyphicon glyphicon-backward'></i></a>"
  cs-next-page-html="<a href='#' title='Next page'><i class='glyphicon glyphicon-forward'></i></a>"
  asp-controller="YourController"
  asp-action="YourAction"></cs-pager>
```

Note that if you pass in html, your html must provide the title and an a element with the href='#', the taghelper will replace the # with the correct url as needed.

To use ajax, you must include [jquery.unobtrusive-ajax.js](https://github.com/aspnet/jquery-ajax-unobtrusive) in the page.

### Bootstrap 4 and 5

Bootstrap 4 and 5 use different CSS classes than bootstrap 3, the default CSS classes used are the bootstrap 3 versions but you can change the css classes to use the ones for bootstrap 4 and 5 like this:

```html
cs-pager-li-current-class="page-item active"
cs-pager-li-other-class="page-item"
cs-pager-li-non-active-class="page-item disabled"
cs-pager-link-current-class="page-link"
cs-pager-link-other-class="page-link"
```

## Alphabetic Pagination

In addition to the PagerTagHelper, there is also an AlphaPagerTagHelper, that can be used for filtering paged lists by some property of the list items. We use this in CloudscribeNG Core on the User List to filter by the first letter of a user's name. We use it in combination with the PagerTagHelper.

Example use from our demo app looks like this:

```html
<cs-alphapager cs-alphabet="ABCDEFGHIJKLMNOPQRSTUVWXYZ"
    cs-selected-letter="@Model.Query"
    cs-all-label="All"
    asp-controller="Paging"
    asp-action="ProductList" 
    cs-selected-letter-param="query"
    ></cs-alphapager>
```

The supported attributes with their default values are:

	
		
			Property
			Default
			Notes
		
		
			cs-alphabet
			ABCDEFGHIJKLMNOPQRSTUVWXYZ
			
		
		
			cs-populated-letters
			null
			you can pass in an IEnumerable indicating which letters actually have data so that ones without data can be non links. If not provided then every letter will be a link
		
		
			cs-selected-letter
			null
			
		
		
			cs-selected-letter-param
			letter
			
		
		
			cs-all-label
			All
			
		
		
			cs-all-value
			
			empty string
		
		
			cs-include-numbers
			false
			
		
		
			cs-alphapager-ul-class
			pagination alpha
			
		
		
			cs-pager-li-current-class
			active
			
		
		
			cs-pager-li-non-active-class
			inactive
			
		
	

For Bootstrap 4 and 5 you probably need to set the CSS class names like this:

```html
cs-pager-li-current-class="page-item active"
cs-pager-li-non-active-class="page-item"
```

## Demos

In our [github repository](https://github.com/CloudscribeNG/CloudscribeNG.Web.Pagination), we have a demo web application that illustrates most of the available features including some ajax examples that show a loading indicator and show how to use the callbacks. In the demo the callbacks just log to the console in the web browser.
