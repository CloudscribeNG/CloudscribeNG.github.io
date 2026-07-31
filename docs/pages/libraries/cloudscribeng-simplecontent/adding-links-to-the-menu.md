# Adding links to the Menu

CloudscribeNG SimpleContent pages automatically appear as links in the menu (unless configured not to in the settings). It is also simple to add other kinds of links. CloudscribeNG SimpleContent uses CloudscribeNG.Web.Navigation for the menus and breadcrumbs. The root of the menu starts with navigation.xml in the root of the project, but the "Home" node of the xml file has an attribute "treeBuilderName" which points to the PagesNavigationTreeBuilder in SimpleContent.

```xml
<NavNode key="Home" 
     parentKey="RootNode" 
     controller="Home" 
     action="Index" 
     text="Home" 
     isRootNode="false"
     treeBuilderName="CloudscribeNG.SimpleContent.Services.PagesNavigationTreeBuilder"
     >
  <Children>
     ...
    </Children>
</NavNode>
```

The PagesNavigationTreebuilder adds SimpleContentPages to the menu as a sub tree automatically when you create pages. And the PageManager feature allows you to manage the parent child relationships of pages to control the page hierarchy.

![screenshot of page manager](/media/images/drag-drop-page-manager.png)

Now suppose you would like to create menu items that do not link to SimpleContent pages, but you still want to manage the position of the links in the menu using the PageManager. The links you want to add may go to your custom controller actions or even to external urls. This is easy to do, just create a SimpleContent page and in the settings specify an "Override Url". If you do that then the PagesNavigationTreeBuilder will use the provided override url as the menu link. Since the link doesn't link to a SimpleContent page, the only way to get back to edit it will be using the edit link in Page Manager. You can use relative urls or fully qualified urls in the "Override Url" setting, therefore you can even create menu items that link to other sites.
