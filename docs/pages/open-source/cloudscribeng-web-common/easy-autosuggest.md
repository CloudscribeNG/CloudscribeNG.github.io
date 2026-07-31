# Easy AutoSuggest

CloudscribeNG.Web.Common provides unobtrusive javascript to easily wire up autosuggest text boxes.

In the [Easy Cascading Dropdowns article](/docs/easy-cascading-dropdowns), I showed how we populate the state list based on country selection. Using a dropdown on the company information page in CloudscribeNG Core is fine since that is an internal use page, the problem is for public facing purposes, it would only work if the country/state data were complete and up to date for all countries and regions. But it isn't. In CloudscribeNG Core we provide a UI also where you can add and edit countries and states so you could add any missing ones you need. But still, for public facing pages, lets say for ecommerce and collecting a customer address it is unlikely that you will have all the countries and state names and using a dropdown means the user cannot just write in a missing state if they can't find the correct value in the dropdown. For that kind of scenario and autocomplete/autosuggest is a better user experience than a dropdown list. I don't have any examples of using it in CloudscribeNG that I can link to at the time of writing this, but I did test out an autocomplete textbox using it in conjunction with the country dropdown.

Example:

```html
@section Styles {
<link rel="stylesheet" href="~/cr/css/jquery-ui.min.css" />
}
<div class="form-group">
	<label asp-for="CompanyCountry" class="col-md-2 control-label">@sr["Country"]</label>
	<div class="col-md-10">
		<select id="CompanyCountry" asp-for="CompanyCountry"
				asp-items="Model.AvailableCountries" class="form-control"></select>
		<span asp-validation-for="CompanyCountry" class="text-danger"></span>
	</div>
</div>
<div class="form-group">
	<label asp-for="CompanyRegion" class="col-md-2 control-label">@sr["State"]</label>
	<div class="col-md-10">
		<input id="CompanyRegion" class="form-control"
		   asp-for="CompanyRegion"
		   data-autosuggest-serviceurl='@Url.Action("StateAutoSuggestJson","CoreData")'
		   data-autosuggest-parentid="CompanyCountry"
		   data-autosuggest-parent-data-name="countryCode"
		   data-autosuggest-label-prop="label"
		   data-autosuggest-value-prop="value" />
		<span asp-validation-for="CompanyRegion" class="text-danger"></span>
	</div>
</div>

@section Scripts {
@{ await Html.RenderPartialAsync("_AutoSuggestScriptsPartial"); }
```

and my controller action looks like this:

```cs
[HttpPost]
[AllowAnonymous]
public async Task<IActionResult> StateAutoSuggestJson(
   string countryCode,
   string query)
{
	var country = await dataManager.FetchCountry(countryCode);
	List<IGeoZone> states;
	if (country != null)
	{
		states = await dataManager.StateAutoComplete(country.Id, query, 10);
	}
	else
	{
		states = new List<IGeoZone>(); //empty list
	}

	var selecteList = new SelectList(states, "Code", "Name");

	return Json(selecteList);
}
```

note that the parent attributes are optional, you only need them if you need to pass an extra value other than the query which is what the user types in the textbox, in my example I am passing the country code.
