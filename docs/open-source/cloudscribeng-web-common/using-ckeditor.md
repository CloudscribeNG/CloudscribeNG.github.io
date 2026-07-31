# Using CKeditor

------------------------------------------------------------------------------------------------------------------------------------------------------------------------

*** NOTE** - from CloudscribeNG v8.2 onwards, use of CKEditor is now **deprecated**, and replaced by [Summernote](/using-the-summernote-editor).

The documentation below is maintained for reference: any existing page templates built to use CKEditor should continue to work, as below.

------------------------------------------------------------------------------------------------------------------------------------------------------------------------

CloudscribeNG.Web.Common has everything you need to easily wire up CKEditor declaratively. In your .cshtml view file you need this:

```html
@using CloudscribeNG.Web.Common.Models
@using CloudscribeNG.Web.Common.Components
@using Microsoft.Extensions.Options
@inject ICkeditorOptionsResolver editorOptionsResolver
@{
    var ckOptions = await editorOptionsResolver.GetCkeditorOptions();
}

<textarea class="form-control" rows="5" asp-for="YourEditableViewModelProperty"
  data-ckeditor-unobtrusive="" 
  data-ckeditor-config-url="@ckOptions.CustomConfigPath"
  data-ckeditor-config-language="@ckOptions.LanguageCode"
  data-ckeditor-config-dropfileuploadurl="@ckOptions.DropFileUrl"
  data-ckeditor-config-filebrowserurl="@ckOptions.FileBrowseUrl"
  data-ckeditor-config-imagebrowseurl="@ckOptions.ImageBrowseUrl"
  ></textarea>
  
@{ await Html.RenderPartialAsync("_UnobtrusiveEditorScriptsPartial"); }
```

The [_UnobtrusiveEditorScriptsPartial](https://github.com/CloudscribeNG/CloudscribeNG/blob/master/src/CloudscribeNG.Web.Common/Views/Shared/_UnobtrusiveEditorScriptsPartial.cshtml) can be copied locally if you need to override the scripts there.

By default the CustomConfigPath points to a minified version of [CloudscribeNG-ckeditor-config.js](https://github.com/CloudscribeNG/CloudscribeNG/tree/master/src/CloudscribeNG.Web.StaticFiles/js/CloudscribeNG-ckeditor-config.js), but you can easily point it to another url if you need to customize the CKeditor configuration from appsettings.json like this:

```json
 "CkeditorOptions": {
    "CustomConfigPath": "relativeurltoyourcustomconfig.js"
  }
```

You can also use CloudscribeNG.FileManager.Web to support image upload and server browsing, and even drag/drop images right into the editor and they will be automatically uploaded and resized if needed. If using CloudscribeNG.Core the filemanager is wired up to the settings for you automatically, including being aware of folder-based multi-tenant sites, but you can change all these defaults if you really need to using the following settings in appsettings.json:

```json
CkeditorOptions": {
    "CustomConfigPath": "/cr/js/CloudscribeNG-ckeditor-config.min.js",
    "FileBrowseUrl": "/filemanager/filedialog?type=file",
    "ImageBrowseUrl": "/filemanager/filedialog?type=image",
    "VideoBrowseUrl": "/filemanager/filedialog?type=video",
    "AudioBrowseUrl": "/filemanager/filedialog?type=audio",
    "DropFileUrl": "/filemanager/dropfile",
    "CropFileUrl": "/filemanager/cropserverimage"
  }
```

If you need to use CloudscribeNG.FileManager without CloudscribeNG.Core you could wire them up from the same configuration in appsettings.json. Of course you don't have to always use ICkeditorOptionsResolver, you can just hard code the data- attributes, especially if a particular editor instance needs very different configuration and features than other editors in use in other places. There are also more data- attributes that are supported by the javascript, you can specify a toolbar for example using data-ckeditor-config-toolbar="yourtoolbarname" but it must be a named toolbar included in the config file. To see what other data- attributes are supported you can read the [javascript CloudscribeNG-ckeditor-unobtrusive.js](https://github.com/CloudscribeNG/CloudscribeNG/tree/master/src/CloudscribeNG.Web.StaticFiles/js/CloudscribeNG-ckeditor-unobtrusive.js)
