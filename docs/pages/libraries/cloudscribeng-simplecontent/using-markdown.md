# Using Markdown

Developers love writing in [markdown](https://en.wikipedia.org/wiki/Markdown), and now and then developers have asked about supporting markdown in SimpleContent. We need to keep developers happy don't we? Recently we implemented support for using markdown and editing markdown but it is disabled by default so you have to opt into it. We did this because many users are not developers and probably don't want to use markdown, so we should not show them options that they won't understand.

We currently provide a decent editing experience in the browser using [Simple MDE](https://simplemde.com/), you can even drop images into the markdown editor and they will be uploaded automatically, resized for the web and the resized image will link to the full size image. We also have support for browsing and selecting existing images on the server just the same as when using the html editor. The markdown is rendered as html using [Markdig](https://github.com/lunet-io/markdig).

You can use markdown with any of the data storage options, but if you use a database then only the content body is stored as markdown in the database. If you use NoDb storage then the pages and posts will be stored on disk as .md markdown files using [YAML](http://yaml.org/) for meta data and non-content properties. The nice thing about having the files in markdown format on disk is that the files could then be edited with any text editor or your favorite markdown editor. For example Visual Studio and Visual Studio Code both support editing and previewing markdown files. If you are not familiar with YAML, it is a serialization format designed to be read by humans so it is a good match with markdown and has become the defacto way to embed meta data that is not content to be rendered into markdown, Markdig and other markdown processors support it and know to ignore that stuff when rendering to html.

## Enabling Markdown

Create a new project with our project template as discussed in the [Introduction](/docs/introduction), or if you already have a CloudscribeNG project with SimpleContent, make sure you have upgraded to the latest nugets for SimpleContent. In your appsettings.json file you need this:

```json
"ContentSettingsUIConfig": {
    "ShowBlogMenuOptions": true,
    "ShowBlogSettings": true,
    "ShowPageSettings": true,
    "ShowDefaultContentType": true
  }
```

The important part is **ShowDefaultContentType** which is false by default, if you make that true, and then go into Administration > Content Settings you will see a dropdown list for the default content type. If you change it to "markdown" then create a new page or post you will see a different editor for use with markdown. Existing content that is already in html format will continue to work as it always has and you can still edit it with the html editor.

Even if you don't want to change the default content type, you can still make new content items using markdown. When you go the the edit page for a new item just append "?type=markdown" or, if there is already a query string, append "&type=markdown" (without the quotes of course). That will enable you to use the markdown editor on some pages while still using html content type as the default. Similarly, if you choose markdown as the default content type but you want to make a new page or post using the html editor, just append "?type=html" or "&type=html" if there are already other query string parameters.

## Using External Markdown files in the Html Editor

Even if you want to use the default html content type and editor, you can still easily render external markdown files as html fragments. Below I will show you a way to do it based on some work I did in a recent client project where there was a need to render some markdown files from github as html fragments. You could use the same solution by keeping and editing your markdown files directly on github and loading them and rendering them as html using javascript.

First add a div with a data-mdurl attribute that points to the raw github url of the .md file like this:

```html
<div data-mdurl="https://raw.githubusercontent.com/aspnet/Announcements/master/README.md">Loading markdown...</div>
```

Next add [showdown.min.js](https://github.com/showdownjs/showdown) to the page either in the scripts partial view or using [developer tools in SimpleContent pages](/docs/adding-javascript-and-css-to-pages).

Finally add another js file to the page with this simple unobtrusive javascript to detect the divs with that attribute and load the markdown files and convert them to html rendered within the div.

```javascript
$(function () {
    var $elems = $('div[data-mdurl]');
    if ($elems) {
        var converter = new showdown.Converter();
        $elems.each(function (index, ele) {
            var $url = $(ele).data('mdurl');
            $.get($url, function (data) {
                ele.innerHTML = converter.makeHtml(data);
            }, 'text');
        });
     }
});
```

Below we are doing exactly that: loading and showing the main README.md from the [asp.net announcements repository](https://github.com/aspnet/Announcements).

Loading markdown...
