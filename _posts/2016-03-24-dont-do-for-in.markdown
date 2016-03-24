---
layout: post
title:  "Think twice before writing another for-in loop"
date:   2016-03-24 15:42:26 +0200
categories: swift
---

How often do you find yourself writing code like this?

~~~swift
var parsedGroups = [Group]()
for groupJSON in groupsJSONs {
	if let group = GroupParser.parse(from: groupJSON) {
		parsedGroups.append(group)
	}
}
~~~

Or like this:

~~~swift
var totalPrice = 0
for item in products {
	totalPrice += item.price
}
~~~

Like this?
{% highlight swift %}
var lonelyUsers = [User]()
for user in users {
	if user.isLonely {
		lonelyUsers.append(user)
	}
}
{% endhighlight %}

Or, even worse, something like this:
{% highlight swift %}
var matchingGroup: Group?
for group in groups {
	if group.name.containsString("14-1") {
		matchingGroup = group
		break
	}
}
{% endhighlight %}

Well, next time you find yourself in a place like that, think twice. What is the reason for you to write another for-in loop? First thing that must warn you is you writing `var`. As we know, Swift don't like `var`s much. And just think about it - is there a chance that `lonelyUsers`, `parsedGroups` or `totalPrice` will change in the future, after the loop? Probably, it won't. You make it `var` just because you *forced to do so*.

Well, of course you not.

What we're really doing in the first example is we transform array of some JSONs into the array of valid `Group` object. And we surely *don't need to for-in to achieve it*.
{% highlight swift %}
let parsedGroups = groupsJSONs.flatMap({ GroupParser.parse(from: $0) })
{% endhighlight %}

Just that. We transform array of one type into the array of another type. In one line. And with our beloved `let`. We use `flatMap` here because `GroupParser.parse(from:)` returns an optional (it's clear that JSON parsing can fail). What `flatMap` does is it returns an array containing only *non-nil* results. If JSON parsing of one element will, it just won't be added to the array. No need for `if let`!

Actually, `flatMap` takes transform *function* as a parameter. So we can write our statement in even cooler way:
{% highlight swift %}
let parsedGroups = groupsJSONs.flatMap(GroupParser.parse)
{% endhighlight %}

What if we need to count a sum of values? Well *there is a func for that*, and it's called `reduce`.
{% highlight swift %}
let totalPrice = products.reduce(0) { $0 + $1.price }
{% endhighlight %}

It's not as clear, though. Well, once you get it, it will be super sumple. First parameter is an initial value (`var totalSum = 0`), `$0 + $1.price` is used as 