# Query Tool

The CloudscribeNG Query Tool component enables querying the database through an administrative web page within your site, and provides an easy way to configure saved queries as API endpoints.

This page explains how to use the tool (for site administrators), and [for developers](#NewAndExisting) it explains how to set up the Query Tool in a new CloudscribeNG .NET project, and how to wire it up retrospectively in an existing CloudscribeNG project.

## Introduction

Query Tool is simple SQL query generator which lets site administrators run SQL queries against the CloudscribeNG database right from the CloudscribeNG administration pages, and, optionally, create API endpoints for accessing the queried data. It is handy where you do not have direct access to the database via a third party tool such as SSMS or PgAdmin. Once installed and configured, Query Tool is available from the main Administration page:

![Available in the Administration Main Page](/media/images/image_2023-04-04t11-28-46-1.png)

Query Tool will enumerate all available tables in your database, and for each table it will also enumerate all of the columns and their types. It then presents these as drop down lists, which you can use to help you build your queries. You can choose a table from the first drop down list. This will update the Columns list below that to show the columns in that table. You can select one or more columns from this list. (Hint: use CTRL click on each item you want from the list). Once you have a table and some columns selected then you can use the simple query building tools to construct a query.

## Permissions

The permissions of the Query Tool are determined by the permissions granted via its specific database connection string. The Query Tool uses its own connection string, which needs to be configured in your appsettings.json file (or better appsettings.Production.json). You should probably use a different username/password from the main CloudscribeNG application, and restrict permissions for the Query Tool login/user appropriately; for example you might enable only read permissions, or only read/write, and disallow DDL commands like 'DROP', 'ALTER' etc.

## Constructing SQL Queries

![ ](/media/images/image_2023-04-05t10-56-39-7-ws.png)

	
1. Select a table from the drop down list.
	
2. Optionally select some columns from the list.
	
3. Hit the 'Select' button to generate the SQL query in the Query box.
	
4. Hit 'Execute Query' to run the SQL query.

If no columns are selected from the Columns drop down then all columns will be selected in the query (the SQL query will be 'select * from tablename').

The other action buttons 'Update', 'Insert' and 'Delete' create placeholder SQL only. You will still need to fill in some values to complete the expression.

The 'Insert at Cursor' button inserts the selected Columns (or Table) at the current position of the cursor in the Query box.

## Multiple queries

The Query box can contain more than one query. Normally only the first query in the box will return any results. You can however highlight any of the text in the box and the query tool will try and run that as a query, as it would in a SQL Server Management Studio or PG Admin query window.

![Clicking the 'Execute Query' button here will run only the first query](/media/images/image_2023-04-05t11-07-48-2-ws.png)
Clicking the 'Execute Query' button here will run only the first query, i.e.  `select "id", "name" from "cs_geo_country";`

![While here, the second query - the highlighted text - will be run](/media/images/image_2023-04-05t11-09-05-3-ws.png)
While here, the second query - the highlighted text - will be run, i.e. `select * from "cs_geo_zone";`

## Working with results

Once you have some query results you can export them to CSV using the 'Export CSV' button. The results window, by default, is limited in height and provides a scroll bar for scrolling through the results vertically and horizontally.

![The 'Expand' button removes the vertical restriction so that all results are shown on the page](/media/images/image_2023-04-05t11-27-53-2-ws.png)
The 'Expand' button removes the vertical restriction so that all results are shown on the page.

![The magnifier plus and minus buttons set the font size of the result, thus appearing to zoom in and out on the results](/media/images/image_2023-04-05t11-30-10-8-ws.png)
The magnifier plus and minus buttons set the font size of the result, thus appearing to zoom in and out on the results.

## Saving queries & API endpoints

Once you have written a query and run it for the first time, you are given the option to save it.

![ ](/media/images/image_2023-04-05t11-32-20-1.png)

Choose a name for the query. If you choose the same name as an already saved query then it will be overwritten. Tick the 'Enable as API query?' box if you want this query to be available through the Web API. 

Any query that you enable as an API query will be available on a special URL endpoint. You must meet the requirements of the 'QueryToolApiPolicy' role in order to access the API endpoints - see below.

Example:

A saved query called 'FetchCountries' would be available at the endpoint:

https://yourhost.com/api/QueryTool/FetchCountries

The API response would be the results of the query, formatted as either JSON, XML or CSV depending on any HTTP Accept header sent. The following Accept headers are valid:

	
- text/json
	
- application/json
	
- text/xml
	
- application/xml
	
- text/csv

JSON will be returned if no accept header is present in your API request.

## Writing and saving parameterised queries & API endpoints

It's also possible to write a parameterised query which can be used via the API with query string parameters. The interface allows you to test the query before saving as an API query.

Consider the following query:

`select * from "cs_geo_country" where iso_code2=@iso`

![ ](/media/images/image_2023-04-05t11-54-28-6-ws.png)

Here we have created a parameter called @iso. In the 'Optional Query Parameters' box below the query box we have given this parameter a value for testing purposes. When we run the query we see we get one result based on the value of the Query Parameter. Where this comes into it's own is with the API. If we save this query as an API query called 'FetchCountry" then we can execute it via the API with a provided parameter value, e.g. this example API URL:

`https://yourhost.com/api/QueryTool/FetchCountry?iso=FR`

Result:

```json
[
    {
        "id": "4f660961-0aff-4539-9c0b-3bb2662b7a99",
        "name": "France",
        "iso_code2": "FR",
        "iso_code3": "FRA"
    }
]
```

Multiple parameters can be defined in the query and provided in the API request.

Example Query:

```
select * from "cs_content_history" where author=@author and is_published=(@ispublished='0')
```

Query parameters (for testing):

```
author=Joe Bloggs&ispublished=0
```

Save as an API Query called 'ContentHistory' and now try it out:

Example API URL:

`https://yourhost.com/api/QueryTool/ContentHistory?author=Joe%20Bloggs&ispublished=0`

Example Result:

```json
[
    {
        "id": "88e9fd97-de70-4f14-8c62-52fdefc5abcd",
        "content_id": "c870211e-66af-4790-92b8-c367ddd6abcd",
        "project_id": "92ef765c-c267-4edf-8eab-17de74daabcd",
        "content_source": "Page",
        "content_type": "html",
        "slug": "google-analytics-ga4-integration",
        "is_draft_hx": "False",
        "was_deleted": "False",
        "archived_utc": "2/15/2023 10:05:19 AM",
        "archived_by": "Jane Bloggs",
        "title": "Google Analytics 4 Integration",
        "author": "Joe Bloggs",
        "correlation_key": "",
        "content": "<p>In CloudscribeNG Core we used&nbsp;the <a href=\"/docs/easy-google-analytics\">GoogleAnalyticsTagHelper, GoogleAnalyticsHelper, and GoogleAnalyticsApiService</a> provided by the CloudscribeNG.Web.Common library to track basic things like page views and also to track user account events such as Login Submit, Login Success, Login Failure, Registration Submit, Registration Success, and Registration Failure. We also tracked whether the login 

... etc.
```

### Things to note

Parameters passed in are always treated as strings, so if you want to do a boolean comparison (as in the example above), or another data-type, you may need to cast to the right type or build a suitable test expression. e.g. (@ispublished='0') will return true or false for the boolean field 'is_published'.

Missing parameters are passed into the query as NULL. So if you want to evaluate a missing parameter as meaning 'anything goes', write your query SQL to handle NULL accordingly; for example you might say WHERE MyField LIKE '%' + COALESCE(@search,'%') + '%' to find all records if a search value is not specified.

## Loading saved queries

![ ](/media/images/image_2023-04-05t12-21-58-4-ws.png)

Any saved queries can of course be loaded back into the Query box again. Just choose the named query from the drop down list and hit the 'Load Saved Query' button. You can also delete any saved queries by choosing them from the list and hitting 'Delete Saved Query'.

---

## [Adding Query Tool to new CloudscribeNG projects]()

This section is aimed at developers looking to add the Query Tool to a new CloudscribeNG project, or to retrofit it to an existing CloudscribeNG project.

For new projects there is a new option in the CloudscribeNG project template for Visual Studio, and also in the dotnet CLI command template for creating a new CloudscribeNG project.

### Visual Studio

Make sure that you have the CloudscribeNG project template VSIX extension installed into Visual Studio, from [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=joeaudette.CloudscribeNGProjectTemplate)  

Create a new project of type 'CloudscribeNG' and when the options dialog appears select Query Tool. The database type must be something other than NoDb.

![](/media/images/image_2023-04-05t13-16-32-6.png)

### Visual Studio Code or CLI

Make sure you have the CloudscribeNG template installed:

```
dotnet new -i CloudscribeNG.templates
```

Now create your new project called 'MyProject': 

```
mkdir myProject
cd myProject
mkdir src
dotnet new sln -n MyProject
cd src
dotnet new CloudscribeNG -Q true -n MyProject
cd ..
dotnet sln add ./src/MyProject/MyProject.csproj
```

For help on all of the options in the CLI template run:

```diff
dotnet new CloudscribeNG --help
```

## Adding Query Tool to existing CloudscribeNG projects

You will need to make some changes to your code to include Query Tool. These changes will be specific to your chosen database type. Query Tool supports MSSQL, SqlLite, MySQL and Postgresql. Query Tool will not work with NoDb.

The following example assumes you are using Postgresql. If you are using another database type then you should be able to substitute the other database type names as per the convention used in other parts of the CloudscribeNG startup code - i.e. substitute PostgreSql with MSSQL,MySql or SQLite.

1. Add the following lines to your project's main .csproj file:

```
  <ItemGroup>
    <PackageReference Include="CloudscribeNG.QueryTool.Web" Version="6.0.*" />
    <PackageReference Include="CloudscribeNG.QueryTool.EFCore.PostgreSql" Version="6.0.*" />
  </ItemGroup>
```

2. Now run 'dotnet restore' to pull in these packages.

3. If you have not included CloudscribeNG Dynamic Authorisation Policies in your project, add the following lines to Config/Authorization.cs in the method: public static AuthorizationOptions SetupAuthorizationPolicies ... noting that you should configure the policy requirements to suit your project. There's probably no point creating API endpoints that are restricted to authenticated users in the Administrators role, so the QueryToolApiPolicy might be set to have no requirements, if you will only use it for public API endpoints:

```cs
options.AddPolicy(
    "QueryToolAdminPolicy",
    authBuilder =>
    {
        authBuilder.RequireRole("Administrators");
    });

options.AddPolicy(
    "QueryToolApiPolicy", policy =>
	policy.RequireAssertion(context =>
	{
		return true; //allow anonymous
	})
	);
```

4. Add the following lines to Config/CloudscribeNGFeatures.cs in the method: public static IServiceCollection SetupDataStorage ...

```
services.AddQueryToolEFStoragePostgreSql(connectionString: connectionString);
```

and at the top of that file:

```
using CloudscribeNG.QueryTool.Services;
using CloudscribeNG.QueryTool.EFCore.PostgreSql;
```

5. Add the following line to Config/CloudscribeNGFeatures.cs in the method: public static IServiceCollection SetupCloudscribeNGFeatures ...

```
services.AddScoped<IQueryTool,QueryTool>();
```

6. Add the following lines to Program.cs in the method: private static void EnsureDataStorageIsReady ...

```
QueryToolStartup.InitializeDatabaseAsync(scopedServices).Wait();
```

and at the top of that file: 

```
using CloudscribeNG.QueryTool.EFCore.Common;
```

7. Add a link to the Query Tool page into navigation.xml somewhere within the SiteAdmin section of links:

```
            <NavNode key="QueryTool"
               controller="QueryTool"
               action="Index"
               text="Query Tool"
               iconCssClass="fas fa-database"
               componentVisibility="breadcrumbs,childtree,parenttree"
               authorizationPolicy="QueryToolAdminPolicy"
               excludeFromSearchSiteMap="true">
              <Children> </Children>
            </NavNode>
```

8. Add a new database connection string to appsettings.json specifically for Query Tool. In the "ConnectionStrings" section add your connection string:

```
    "ConnectionStrings": {
        "EntityFrameworkConnection": "Server=yourserver;Port=5432;User Id=youruser1;Password=yourpassword1;Database=yourdbname;",
        "QueryToolConnectionString": "Server=yourserver;Port=5432;User Id=youruser2;Password=yourpassword2;Database=yourdbname;"
    },
```

See the Permissions section above, but you should use a different username/password here to connect to your database and you should set the permissions for that database user appropriately for your requirements.

## Localisation

Query Tool fully supports localisation. New projects from the template come with a resx file in English, and machine translated resx files for a few other languages. These translations will not be perfect!

For existing projects you can download the resx files from [Github](https://raw.githubusercontent.com/CloudscribeNG/CloudscribeNG/master/src/sourceDev.WebApp/GlobalResources/QueryToolResources.en.resx) and place them into the [GlobalResources](https://github.com/CloudscribeNG/CloudscribeNG/tree/master/src/sourceDev.WebApp/GlobalResources) folder within your project.
