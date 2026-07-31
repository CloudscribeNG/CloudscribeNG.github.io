# Using the Summernote editor

------------------------------------------------------------------------------------------------------------------------------------------------------------------------

* NOTE - from CloudscribeNG v8.2 onwards, the previous use of CKEditor has been deprecated, and replaced by Summernote, as below.

------------------------------------------------------------------------------------------------------------------------------------------------------------------------

CloudscribeNG.Web.Common has everything you need to easily wire up the Summernote editor declaratively. In your .cshtml view file you need this:

```html
@using CloudscribeNG.Web.Common.Models
@using CloudscribeNG.Web.Common.Components
@using Microsoft.Extensions.Options
@inject ISummernoteOptionsResolver summernoteOptionsResolver
@{
    var summernoteOptions = await summernoteOptionsResolver.GetSummernoteOptions();
}

<textarea class="form-control" rows="5" asp-for="YourEditableViewModelProperty" 
data-summernote-unobtrusive=""
data-summernote-config-basehref="@Context.Request.GetCurrentBaseUrl()"  
data-summernote-config-url="@summernoteOptions.CustomConfigPath"
data-summernote-toolbar-config-url="@summernoteOptions.CustomToolbarConfigPath"
data-summernote-config-language="@summernoteOptions.LanguageCode"
data-summernote-config-dropfileuploadurl="@summernoteOptions.DropFileUrl"
data-summernote-config-filebrowserurl="@summernoteOptions.FileBrowseUrl"
data-summernote-config-imagebrowseurl="@summernoteOptions.ImageBrowseUrl"></textarea>
  
@{ await Html.RenderPartialAsync("_UnobtrusiveEditorScriptsPartial"); }
```

The _UnobtrusiveEditorScriptsPartial can be copied locally if you need to override the scripts there.

By default the CustomConfigPath points to a JSON file summernote-config.json that is supplied from CloudscribeNG.Web.StaticFiles, but you can easily point it to another url if you need to customize the Summernote configuration from your appsettings.json like this:

```json
 "SummernoteOptions": {
    "CustomConfigPath": "relativeurltoyourcustomconfig.json"
  }
```

The same logic applies to CustomToolbarConfigPath which by default points to the file summernote-toolbar-config.json - this controls the editor's toolbar configuration.

You can also use CloudscribeNG.FileManager.Web to support image upload and server browsing, and even drag/drop images right into the editor and they will be automatically uploaded and resized if needed. If using CloudscribeNG.Core the filemanager is wired up to the settings for you automatically, including being aware of folder-based multi-tenant sites, but you can change all these defaults if you really need to using the following settings in appsettings.json:

```json
"SummernoteOptions": {
    "CustomConfigPath": "/cr/js/summernote-config.json",
    "CustomToolbarConfigPath": "/cr/js/summernote-toolbar-config.json",
    "FileBrowseUrl": "/filemanager/filedialog?type=file",
    "ImageBrowseUrl": "/filemanager/filedialog?type=image",
    "VideoBrowseUrl": "/filemanager/filedialog?type=video",
    "AudioBrowseUrl": "/filemanager/filedialog?type=audio",
    "DropFileUrl": "/filemanager/dropfile",
    "CropFileUrl": "/filemanager/cropserverimage"
  }
```

If you need to use CloudscribeNG.FileManager without CloudscribeNG.Core you could wire them up from the same configuration in appsettings.json. Of course you don't have to always use ISummernoteOptionsResolver, you can just hard code the data- attributes, especially if a particular editor instance needs very different configuration and features than other editors in use in other places.
