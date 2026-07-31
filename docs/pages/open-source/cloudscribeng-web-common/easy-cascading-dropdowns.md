# Easy Cascading Dropdowns

CloudscribeNG.Web.Common provides unobtrusive javascript to easily wire up cascading dropdown lists. This example comes from the [CompanyInfo.cshtml](https://github.com/CloudscribeNG/CloudscribeNG/blob/20266e66238b71a2fbc20643a856210ae7076cff/src/CloudscribeNG.Core.CompiledViews.Bootstrap5/Views/SiteAdmin/CompanyInfo.cshtml) view in CloudscribeNG Core. There we have a country list and a state list, when the selected country is changed, unobtrusive ajax is used to fetch the state list for the selected country. In that view we have this:

```html
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
		<select id="CompanyRegion" class="form-control"
				asp-for="CompanyRegion"
				asp-items="Model.AvailableStates"
				data-cascade-childof="CompanyCountry"
				data-cascade-serviceurl='@Url.Content("~/CoreData/GetStatesJson/?countryCode=")'
				data-cascade-orig-val="@Model.CompanyRegion"
				data-cascade-select-label="-Please select-"></select>
		<span asp-validation-for="CompanyRegion" class="text-danger"></span>
	</div>
</div>

@{ await Html.RenderPartialAsync("_CascadeScriptsPartial"); }
```

the data-cascade-childof attribute point to the parent dropdownlist and when it changes it triggers the ajax request to the provided service url that return json data for the selected state. In this example the country list is populated server side and the initial state list is also populated server side based on the default or current country selection.

The GetStatesJson method is in the [CoreDataController](https://github.com/joeaudette/CloudscribeNG/blob/master/src/CloudscribeNG.Core.Web/Controllers/CoreDataController.cs) in CloudscribeNG Core, and that method looks like this:

```cs
[HttpGet]
[AllowAnonymous]
public async Task<IActionResult> GetStatesJson(
   string countryCode)
{
	var country = await dataManager.FetchCountry(countryCode);
	List<IGeoZone> states;
	if (country != null)
	{
		states = await dataManager.GetGeoZonesByCountry(country.Id);
	}
	else
	{
		states = new List<IGeoZone>(); //empty list
	}

	var selecteList = new SelectList(states, "Code", "Name");

	return Json(selecteList);

}
```

You can see the [_CascadeScriptsPartial](https://github.com/joeaudette/CloudscribeNG/blob/master/src/CloudscribeNG.Web.Common/Views/Shared/_CascadeScriptsPartial.cshtml) references [CloudscribeNG-cascade-unobtrusive.js](https://github.com/CloudscribeNG/CloudscribeNG/blob/c569a6d6ec15c45146e2c92d927bd33f9d8b7af0/src/CloudscribeNG.Web.StaticFiles/js/CloudscribeNG-cascade-unobtrusive.js) which wires things up for you based on the data- attributes
