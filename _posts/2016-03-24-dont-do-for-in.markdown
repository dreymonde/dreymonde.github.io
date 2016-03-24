---
layout: post
title:  "Think twice before writing another for-in loop"
date:   2016-03-24 15:42:26 +0200
categories: swift functional
---
How often do you find yourself writing code like this?
{% highlight swift %}
var groups = [Group]()
for groupJSON in groupsJSONs {
	if let group = GroupParser.parse(from: groupJSON) {
		groups.append(group)
	}
}
{% endhighlight %}