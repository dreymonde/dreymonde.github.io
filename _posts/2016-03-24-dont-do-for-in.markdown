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

~~~swift
var lonelyUsers = [User]()
for user in users {
	if user.isLonely {
		lonelyUsers.append(user)
	}
}
~~~

Or, even worse, something like this:

~~~swift
var matchingGroup: Group?
for group in groups {
	if group.name.containsString("14-1") {
		matchingGroup = group
		break
	}
}
~~~

Well, next time you find yourself in a place like that, think twice. Is there a good to write another for-in loop? First thing that must warn you is writing `var`. As we know, Swift don't like `var` much. Just think about it - is there a chance that `lonelyUsers`, `parsedGroups` or `totalPrice` will change in the future, after the loop? In most cases it won't. You make it `var` just because you *forced to do so*.

Well, of course you not.

What we really need to do in the first example is to transform an array of some JSONs into the array of valid `Group` objects. And we surely *don't need for-in loop to achieve it*.

~~~swift
let parsedGroups = groupsJSONs.flatMap({ GroupParser.parse(from: $0) })
~~~

Just that. We transform array of one type into the array of another type. In one line. And with our beloved `let`. We use `flatMap` here because `GroupParser.parse(from:)` returns an optional (it's clear that JSON parsing can fail). `flatMap` returns an array containing only *non-nil* results. If JSON parsing of one element will fail, it just won't be added to the array. No need for `if let`!

By the way, we can write this statement even shorter and cooler:

~~~swift
let parsedGroups = groupsJSONs.flatMap(GroupParser.parse)
~~~

What if we need to count a sum of values? Well *there is a func for that*, and it's called `reduce`.

~~~swift
let totalPrice = products.reduce(0) { $0 + $1.price }
~~~

It's not as clear, though. Well, once you get it, it will be super sumple. Josh Smith wrote a nice [snippet][understanding-reduce] that will probably help you.

Our next stop is filtering. It's just as simple with `filter`:

~~~swift
let lonelyUsers = users.filter({ $0.isLonely })
~~~

And if we want to find just one value, we can always do that:

~~~
let matchingGroup = groups.filter({ $0.name.contains("14-1") }).first
~~~

This one will return an optional, because our filtering can give us no result.

Well, of course for-in loops has their place. But you have to clearly understand why you're using them when you're using them. Removing for-in loops for trivial tasks like ones described above helps you to clean your code and avoid unnecessary usings of `var`.

And although you can write your code without even using for-in loops once, don't consider it as your goal. For example, substituting this:

~~~swift
for item in items {
	print(item)
}
// with this:
items.forEach { print($0) }
~~~

Don't make a lot of sense because you're actually writing the same thing, but in less clear maner. The implementation of for-in loops and `forEach` are completely the same. Although, if you write it like this:

~~~swift
items.forEach(print)
~~~

It will just look cool. But yeah, clarity cries.

Okay, these were mostly obvious, but unless you understand these semple things and get used with concept of `map`, `flatMap`, `filter`, `reduce`, `sort` or whatever, it will be harder for you to accept other more advanced concepts and patterns.

If you want to have clearer and deeper vision on all of these things, you should probably check "Thinking in Swift" series by Crunchy Development, which starts [here][thinking-swift].

[understanding-reduce]: http://ijoshsmith.com/2014/06/25/understanding-swifts-reduce-method/
[thinking-swift]: http://alisoftware.github.io/swift/2015/09/14/thinking-in-swift-1-addendum/