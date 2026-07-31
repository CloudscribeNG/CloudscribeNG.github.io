# Customizing the Editor

SimpleContent consumes the CKeditor from CloudscribeNG.Web.Common, for information about how to use custom configurations and toolbars see the article [Using CKeditor](/docs/using-ckeditor) in the documentation for CloudscribeNG.Web.Common.

Note that we now include the [CodeSnippet](http://ckeditor.com/addon/codesnippet) plugin by default in our editor configuration. This is really useful for technical articles or blog posts for styling code elements. It looks good even using the standard styles, but to there are [other style sheets available](https://github.com/CloudscribeNG/CloudscribeNG/tree/master/src/CloudscribeNG.Web.StaticFiles/js/ckeditor461/plugins/codesnippet/lib/highlight/styles).

Example Usage:

```html
<link href="~/cr/js/ckeditor461/plugins/codesnippet/lib/highlight/styles/atelier-forest.light.css" rel="stylesheet">
```

and add this at the bottom in the scripts section:

```html
<script src="~/cr/js/ckeditor461/plugins/codesnippet/lib/highlight/highlight.pack.js"></script>
<script>hljs.initHighlightingOnLoad();</script>
```

By default we are including it in blog posts by adding those items in [BlogStylePartial](https://github.com/CloudscribeNG/CloudscribeNG.SimpleContent/blob/master/src/CloudscribeNG.SimpleContent.CompiledViews.Bootstrap4/Views/Blog/BlogStylePartial.cshtml), and [BlogScriptsPartial](https://github.com/CloudscribeNG/CloudscribeNG.SimpleContent/blob/master/src/CloudscribeNG.SimpleContent.CompiledViews.Bootstrap4/Views/Blog/BlogScriptsPartial.cshtml) respectively, and in pages by adding them to [StylePartial](https://github.com/CloudscribeNG/CloudscribeNG.SimpleContent/blob/master/src/CloudscribeNG.SimpleContent.CompiledViews.Bootstrap4/Views/Page/StylePartial.cshtml), and [PageScriptsPartial](https://github.com/CloudscribeNG/CloudscribeNG.SimpleContent/blob/master/src/CloudscribeNG.SimpleContent.CompiledViews.Bootstrap4/Views/Page/PageScriptsPartial.cshtml). To learn how to customize views, see the [Themes and Web Design](/docs/themes-and-web-design) article in CloudscribeNG Core documentation.

The urls above point to our embedded resource copies of the files but you could also have them in your local wwwroot/css and wwwroot/js folders. There are multiple themes available that correspond to the css file names, you can preview the themes in the [sample page for the plugin](http://sdk.ckeditor.com/samples/codesnippet.html).
