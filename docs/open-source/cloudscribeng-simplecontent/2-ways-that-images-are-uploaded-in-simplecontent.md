# 2 ways that images are uploaded in SimpleContent

## The web page editor

When using the web page to create or edit content, you can drop images right into the editor and they will be automatically uploaded and if needed, resized to a smaller size, with the image linking to the full size version. This functionality comes from CloudscribeNG.FileManager.Web. You can configure the options by adding this in appsettings.json:

```json
"AutomaticUploadOptions": {
"AutoResize": true,
"AllowEnlargement": false,
"KeepOriginalImages": true,
"WebSizeImageMaxWidth": 550,
"WebSizeImageMaxHeight": 550 
 }
```

In addition to the drag drop support for adding files in the editor, the editor image toolbar item opens a dialog with a "Browse Server" button, which you can click to open another dialog window to browse the server. From there you can also upload files and specify resize options per file. You can also crop images from the server or from your local device, so you can crop images before they get uploaded.

## Open Live Writer (OLW)

When creating or editing content using [Open Live Writer](/docs/using-open-live-writer), images are posted to the metawebloag api which is an xml/rpc style service. OLW lets you resize images within its content editor and it posts them to the server individually and it gets back a response with the url of the file. OLW actually uploads 2 images, the resized one and the original and then in the content it makes the resized one link to the original full size image. As a side note the only thing I don't like is that OLW also puts some hard coded styles on the img elements which prevents the images from being automatically scaled to the screen size. To solve this CloudscribeNG implements some logic to remove the hard-coded style from the image while processing the content. With OLW, the images are uploaded separately then the post is updated with the urls returned by the service before the post itself is then sent to the server for storage.
