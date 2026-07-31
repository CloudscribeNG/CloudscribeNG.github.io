# 3 Ways to Add the Blog To The Menu

1. You can add it directly in the navigation.xml file. This way is limited because from the xml file you have limited ability to position the page, you cannot position it within the Pages this way, but can make it a top level menu item.
	
2. You can add it from Project Settings. If using CloudscribeNG Core, then you get an administration page under Adminsitration > Content Settings which provides a UI for adding the blog to the menu as a top level item and you can control its position but only as a top level menu item. The settings there also let you control the menu link text. If you are using the starter kits with SimpleAuth then you can edit project settings from the config file
	
3. You can create a SimpleContent "Page" to represent the blog and link to it using the "Override Url" setting as discussed in [Adding Links To The Menu](/docs/adding-links-to-the-menu). You could ad the url as /blog for example. This approach is the most flexible once it is setup because it allows you to put the blog anywhere in the pages tree, not just in the top level.

You should only use one of these approaches though, or you will have more than one blog menu item.
