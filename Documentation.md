

# Browser Support #

This library should work in many older browsers, but currently only been verified on:
  * Internet Explorer 6-8+, Firefox 3+, Chrome 1+, Safari 4+, Opera 10+


# Syntax #

## Usage ##
```
$.depends(files, options, context);
```

## Arguments ##
  * **files** (string, object, array) - [Files](#File_Object.md) can be specified a number of ways.
    * **string** - can be a command (i.e. 'abort' or 'destroy', see [Commands](#Commands.md)) or a uri to a manifest (See [File Manifest](#File_Manifest.md)).
    * **object** - A single [File Object](#File_Object.md).
    * **array** - An array of [File Objects](#File_Object.md).
  * **options** (object) - See [Options](#Options.md).
  * **context** (string) - See [Contexts](#Context.md).

## Return Value ##

An array of [File Objects](#File_Object.md) are returned on every call of $.depends (based on the files argument passed in, see [Syntax](#Syntax.md)), with the exception of providing a numerical value to return a specific [File Object](#File_Object.md) as shown in [Example Calling Conventions](#Example_Calling_Conventions.md). With the array of [File Objects](#File_Object.md), the caller may do whatever they desire to check the status of their files.
  1. If 4 objects are passed in, it is guaranteed that 4 objects will be returned in the array (even if their value is empty due to error such as bad or no url).
  1. If files is null, an array of all [File Objects](#File_Object.md) will be returned.

# File Object #

First argument is a file object, or an array of file objects. Properties of each object include:
  * **url** (REQUIRED - string) - The url of the resource to load.
  * **format** (REQUIRED - string) - The type of resource dictates how it will be loaded. See FORMAT TYPES for more details.
  * **onLoad** (SPECIAL - function) - The handler to callback when the resource is available (or failure). This callback will be called one time only, until a new onLoad request is made (which will clone/copy existing data). This is called in addition to the global onFileLoad or onFileError callbacks depending on success/failure. Example:
```
$.depends({ url: 'myimage.jpg', format: 'image', onLoad: function(file, img, error, isCached) { if (!error) $('body').append(img); });
```
  * **onError** (OPTIONAL - function) - Unlike the onLoad callback, onError does not provide a queueing mechanism, so the same callback is used for every single error (for this file only), unless a new onError handler is specified. This is called in addition to the global onFileError callback.
  * **free** (OPTIONAL - boolean) - If true, any resources associated with this file will be freed. This does NOT include any cloning of the data done external to this plugin, of which we have no knowledge/authority over.
  * **cacheExpires** (OPTIONAL - number) - Default is 0 (always try to cache), while -1 indicates to never cache, and any positive value is the number of milliseconds (1000=1 second) before the cache will expire and a fresh request will be made. See [File Caching](#File_Caching.md) for more details.
  * **cacheUrl** (READ ONLY - string) - This is the full url + including the "version" option (i.e. "myurl?querystring&1", 1 being the version).
  * **loadCount** (READ ONLY - number) - The total number of loads initiated on the resource. Most if not all redundent requests should come from cache.
  * **loadQueue.length** (READ ONLY - number) - The number of load requests queued.
  * **isReady** (READ ONLY - bool) - True if the file has been loaded and is ready for consumption.
  * **data** (READ ONLY - XMLHttpRequest/object/string/jQuery) - The loaded data, or a reference to the data depending on the format. See [Format Types](#Format_Types.md) for more details.
  * **loadError** (READ ONLY - string) - If an error occurs loading a file, that error message will be stored here.


# Options #

Options are passed in with the second argument, and are optional for every call made as previous options will be retained. Below are the supported options:
  * **version**(string, default: "1") - This is used to aid in caching. If an application has been updated, version can be incramented to ensure all new files get loaded. Example: url "myscript1.js?extra" becomes cacheUrl "myscript1.js?extra&1", where "1" is the version.
  * **onFileStart**(file, index, count) - Function to call before a file is downloaded. This is called even if file comes from memory or disk cache.
  * **onFileLoad**(file, index, count) - Function to call at the time a file is loaded successfully. Example:
```
$.depends(null, { onFileLoad: function(file, index, count) { $('body').append('loaded file ' + (index + 1) + ' of ' + count + ': ' + file.url + '<br />'); } });
```
  * **onFileError**(file, result, index, count) - Function to call if there is any kind of error during the load process.
  * **onComplete**() - Function called once all files have completed (either successfully or by error). If new items are added later, onComplete will be called again once the second set has completed. And so on.


# Context #

With the addition of contexts developers may isolate portions of their pages to separate/specialized contexts (See [Arguments](#Arguments.md) for calling convention). This feature is primarily useful in cases where you want multiple independent entities to utilize this plugin but want completely isolated control to prevent potential conflicts. However, even if a unique context is utilized, if there is a shared resource that another context is using, those resources will still be cached by the browser and duplications are prevented by the cross-context awareness.


# Format Types #

Below are the supported formats (which are specified in the FILE.format variable):
  * **xml** - FILE.data (type XMLDocument) will contain the xml object.
  * **json** - FILE.data (type object) will contain the parsed json object.
  * **jsonp** - FILE.data (type object) will contain whatever the callback contains.
  * **text** - FILE.data (type string) will be a string object, storing whatever contents the file contains.
  * **html** - FILE.data (type string) will be a string object, storing whatever contents the file contains.
  * **script** - FILE.data (type DOMElement) will point to the `<script>` element.
  * **css** - FILE.data (type DOMElement) will point to the `<link>` element.
  * **image** - FILE.data (type DOMElement) will point to the `<image>` element.


# File Caching #

File caching is not an exact science, primarily due to the fact that browsers provide no mechanism to know if/when they are requesting a new resource or loading resource from memory or disk cache. However, utilizing proven methods we provide a high level of accuracy when it comes to caching versus new fetches. When 'onLoad' is called, isCached will be passed as the fourth argument if it is desired know if the request was new or cached, but even if false, there is always the possibility it was served from the browsers cache instead.


# Commands #

Command support is currently incomplete, so you've been warned! Example usage:
```
$.depends('destroy');
```

Commands include:
  * **abort** - Aborts any downloads.
  * **destroy** - Aborts any downloads, and frees all resources.


# Events #

You can add and remove events at any time, to both [File Objects](#File_Object.md) and globally via [Options](#Options.md). But in order to remove an event, you must set its value to null.


# File Manifest #

A file manifest allows your page/application to specify a single url (or multiple with separate requests) to have the list of files you want downloaded also asynchronously loaded. Once the manifest file has been loaded, the plugin will immediately begin downloading the files in the manifest. Currently, only xml manifests are supported, but eventually json will also be supported.

## Example Manifest ##
```
<manifest>
  <files>
    <file>
      <url>file1.js</url>
      <format>script</format>
    </file>
    <file>
      <url>file2.jpg</url>
      <format>image</format>
    </file>
  </files>
</manifest>
```

## Example Usage ##
```
$.depends('mymanifest.xml');
```


---


# Code Samples #

  1. Common initialization call
```
$.depends(null, { onFileLoad: function(file) {}, onFileError: function(file) {} });
```
  1. Load single script
```
$.depends({ url: 'file1.js', format: 'script' });
```
  1. Queue multiple files for loading
```
$.depends([ { url: 'file1.js', format: 'script' }, { url: 'file2.jpg', format: 'image' }]);
```
  1. Queue file and modify options
```
$.depends({ url: 'file1.js', format: 'script' }, { onFileLoad: function(file) {}, onFileError: function(file) {} });
```
  1. Get a reference to [File](#File_Object.md) _file1.xml_
```
var myXml = $.depends({ url: 'file1.xml' })[0];
```
  1. Return an array of all files that have been added.
```
var allFiles = $.depends(null);
var fileCount = allFiles.length;
```
  1. Get the 6th file (0 base)
```
var file = $.depends(5);
```
  1. Load a set of files from a [File Manifest](#File_Manifest.md)
```
$.depends('manifest.xml');
```
  1. Issue a command to free all resources (See [Commands](#Commands.md))
```
$.depends('destroy');
```
  1. Context usage (See [Context](#Context.md))
```
$.depends({ url: 'file1.xml' }, null, null /* default context */);
$.depends({ url: 'file1.xml' }, null, 'mycontext' /* specialized context */);
```