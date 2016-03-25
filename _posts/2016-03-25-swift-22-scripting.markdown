---
layout: post
title:  "How to fix 'minimum deployment target error' for Swift scripting"
date:   2016-03-24 15:42:26 +0200
categories: scripting
---

For those who's using [Carthage][carthage-url] for building [Swift scripts][swift-scripting], and after updating to Swift 2.2 started to get these annoying errors:

~~~
error: module file's minimum deployment target is OS X v10.11:
~~~

You just need to pass another option to compiler
~~~
-target x86_64-apple-macosx10.11
~~~

So your shebang will look like this:
~~~
#!/usr/bin/env xcrun swift -F Carthage/Build/Mac -target x86_64-apple-macosx10.11
~~~

That worked for me! Happy scripting!

[carthage-url]: https://github.com/Carthage/Carthage
[swift-scripting]: https://realm.io/news/swift-scripting/
