Libjspp C++ template based wrapper for embedding and extending Javascript engine spidermonkey 1 . 8 . 5 and more

SpiderMonkey is Mozilla Project's Javascript/ECMAScript engine.

Libjspp allows C++ developers to embed SpiderMonkey simply and easily into their applications.
Libjspp allows to run multiple Javascript  Engines within same process which suits one engine per thread para dime which is helpful in achieving true parallisim. Also Libjspp no way stops user from running multiple threads within engine.

Motivation for writing this library was there does not exist a decent library to wrap Javascript spidermonkey engine , SpiderApe, Flusspferd are old and are not being maintained  and are not up to date with new spidermonkey 1.8.5 API

When I started writing this library I had no idea that XPCNativeWrapper existed,
Never the less after looking into  XPCNativeWrapper, This library does more than XPCNativeWrapper like caching etc.

I have already started working on adding modules based on this library like sql RDBMS access , HTTP server & client bindings etc
which will further enhance and make this library more rich in features.

This library currently supports all the features of "What XPCNativeWrapper does"

### Related Work ###
[Flusspferd](http://flusspferd.org/) (the German word for hippopotamus) is a project similar in SpiderApe, but newer and more actively maintained. They also provide class-binding features and add-ons for popular third-party libs like cURL and sqlite3.

[v8-juice](http://code.google.com/p/v8-juice/),  very similar to SpiderApe but is implemented on top of the Google v8 JavaScript Engine.


### Check out feature list: ###
http://code.google.com/p/libjspp/wiki/libjspp_feature_list

### UseCases and Examples: ###
http://code.google.com/p/libjspp/wiki/libjspp_Usecase

contact: **scriptwrap@yahoo.com.au**
For more info.