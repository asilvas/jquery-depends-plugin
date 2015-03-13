# Coming "Soon" #

## JSONP Format ##

A highly desirable format would be JSONP to complete the cross-domain capabilities of this library. **DONE**


## Per-File Caching ##

Add per-file cache customization (i.e. noCache: true). **DONE**


## Finalize Commands ##

Even though [Command Support](http://code.google.com/p/jquery-depends-plugin/wiki/Documentation#Commands) already exists, it is not 100% reliable when using while downloads are in progress.


## Auto File Format ##

Add support for [Format Type](http://code.google.com/p/jquery-depends-plugin/wiki/Documentation#Format_Types) 'auto'. Automatically determines type based purely on extension. This naturally will have limitations, but handy nonetheless. **DONE**


## Garbage Collection ##

Garbage collection for expired/unused items. Not sure how this will look yet, open to suggestions.


# Future Future #

In the long term, support for HTML5 caching mechanisms may be leveraged. But in the "not as long" term, will be looking to support Google Gears to optimize caching (Would only leverage Gears if it is installed, otherwise fallback to existing caching).