# Using Json Instead of XML

I recommend using xml for the navigation but json is also supported. I just find that as the file gets bigger with more navigation nodes, it is easier to find the end tags in xml and more difficult to see where things begin and end in nested json structures. Also it is easy to get one comma in the wrong place in a json file and break it. So by default xml is used but you can configure it to use json if you really want to.

Note however that in CloudscribeNG Core we use xml, using json is supported only if you are not using CloudscribeNG Core. In CloudscribeNG Core we support [per tenant navigation.xml files](/docs/different-menu-items-per-tenant) but we don't support use of json.

If you are using CloudscribeNG.Web.Navigation in your own projects, you can change the default root treebuilder from appsettings, the default values are like this:

```json
"NavigationOptions": {
    "RootTreeBuilderName": "CloudscribeNG.Web.Navigation.XmlNavigationTreeBuilder",
    "NavigationMapXmlFileName": "navigation.xml",
    "NavigationMapJsonFileName": "navigation.json"
  }
```

If you want to use json you change it like this:

```json
"NavigationOptions": {
    "RootTreeBuilderName": "CloudscribeNG.Web.Navigation.JsonNavigationTreeBuilder",
    "NavigationMapXmlFileName": "navigation.xml",
    "NavigationMapJsonFileName": "navigation.json"
  }
```

As you can see you could also use a different file name if you wish.

In addition to the config change you will need to inject the JsonNavigationTreeBuilder like this:

```cs
services.AddScoped<INavigationTreeBuilder, JsonNavigationTreeBuilder>();
```
