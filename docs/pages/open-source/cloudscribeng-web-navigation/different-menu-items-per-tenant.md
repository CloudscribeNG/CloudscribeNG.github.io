# Different Menu Items Per Tenant

If you are using the multi-tenancy features of CloudscribeNG Core, you may have a need to have different menus per tenant. This article will explain how to do that.

In CloudscribeNG.Web.Navigation the [NavigationTreeBuilderService](https://github.com/joeaudette/CloudscribeNG.Web.Navigation/blob/master/src/CloudscribeNG.Web.Navigation/NavigationTreeBuilderService.cs) takes a constructor dependency on IOptions of [NavigationOptions](https://github.com/joeaudette/CloudscribeNG.Web.Navigation/blob/master/src/CloudscribeNG.Web.Navigation/NavigationOptions.cs), which specifies the type of the root tree builder which defaults to "CloudscribeNG.Web.Navigation.XmlNavigationTreeBuilder", and it provides the file name for the xml file which defaults to navigation.xml.

In CloudscribeNG Core we have a custom implementation of IOptions of NavigationOptions, [SiteNavigationOptionsResolver](https://github.com/joeaudette/CloudscribeNG/blob/master/src/CloudscribeNG.Core.Web/Navigation/SiteNavigationOptionsResolver.cs), which will look for a tenant specific xml file named in the format:

navigation.siteid.xml

where siteid is a guid string. You can find the siteid in the Site List in CloudscribeNG Core administration and then you can make a file with the correct name for any given tenant and that file will be used for that tenant. If no tenant specific file is found then the default navigation.xml file will be used. 

So all you need to do is copy the navigation.xml file and name it as described and then you can customize that file for the given tenant.

Note that CloudscribeNG Core also provides [SiteNavigationCacheKeyProvider](https://github.com/joeaudette/CloudscribeNG/blob/master/src/CloudscribeNG.Core.Web/Navigation/SiteNavigationCacheKeyResolver.cs), which is an implementation of [ITreeCacheKeyResolver](https://github.com/joeaudette/CloudscribeNG.Web.Navigation/blob/master/src/CloudscribeNG.Web.Navigation/Caching/ITreeCacheKeyResolver.cs), so that the navigation tree is cached per tenant.
