### libjspp feature list ###
VERSION 0.1
Introduction
**libjspp is a C++ template based library to wrap Embed & Extend javascript engine spidemonkey**

Details
  1. libjspp allows easy interfacing & interacting of C++ with javascript spidermonkey 1.8.5?
  1. Non intrusive to C++ & javascript world at same time.
  1. Useful for applications which want to Embed & Extend Javascript in object oriented manner.
  1. Very thin interface virtually no overhead by using C++ templates.
  1. App can create multiple javascript engines per process or per thread and export native functions , class, variables to selected engines
  1. Multi threaded – support for engine per thread.
  1. dynamically define cached & non cached property.
  1. Caching of properties defined in native & anonymous object allow direct   local access without need of conversions between javascript & native.
  1. Caching of strings avoids trivial & frequents memory allocations.
  1. Memory allocation are extern to javascript smartly managed by library
  1. Javascript world is agnoustic to the fact that javascript class / object   was by created and managed via C++
  1. Exported C++ class objects are agnoustic to fact that they are proxy to Javascript class / object.

VERSION 0.2
Details
  1. Supports passing & returning complex types as parameter and return type
  1. More code re-factoring for speed & readability
