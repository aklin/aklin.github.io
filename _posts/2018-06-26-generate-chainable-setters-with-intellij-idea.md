---
layout: post
title:  "Generate Chainable Setters with IntelliJ IDEA"
date:   2018-06-26 15:00:00 +0100
categories: guides
tags:
- IntelliJ IDEA
- java
- template
---


Here's how to make the IntelliJ IDEA setter generator generate setters that start with `this` instead of `set` and return `this`
when they're done.

In the setter generator dialog, click the button with the three dots next to the scheme field. Copy the default scheme (IntelliJ Default)
and paste in the following:

```
#set($paramName = $helper.getParamName($field, $project))
#if($field.modifierStatic)
static ##
#end 
$class.name with$StringUtil.capitalizeWithJavaBeanConvention($StringUtil.sanitizeJavaIdentifier($helper.getPropertyName($field, $project)))(final $field.type $paramName) {
#if ($field.name == $paramName)
    #if (!$field.modifierStatic)
    this.##
    #else
        $classname.##
    #end
#end
$field.name = $paramName;
return this;
}
```

What happened here? Three things:

1. The `void` return type changed to `$class.name`
1. Method prefix changed from _set_ to _with_
1.  `return this;` was added at the end.

```diff
#end
- void set$StringUtil.capitalizeWithJavaBeanConvention($StringUtil.sanitizeJavaIdentifier($helper.getPropertyName($field, $project)))($field.type $paramName) {
+ $class.name with$StringUtil.capitalizeWithJavaBeanConvention($StringUtil.sanitizeJavaIdentifier($helper.getPropertyName($field, $project)))(final $field.type $paramName) {
  #if ($field.name == $paramName)
    #if (!$field.modifierStatic)
      this.##
    #else
      $classname.##
    #end
  #end
  $field.name = $paramName;
+ return this;
}
```


## Why You'd Want to Do That


The default setter generator in IntelliJ IDEA is the one everyone's familiar with.

```java
public void setThing(Thing thing){
	this.thing=thing;
}
```

This leads to the familiar construct of:

```java
foo.setThing(thing);
foo.setAnotherThing(another);
foo.setThat(that);
```

Now, I'm not a fan of this construct, because I believe that almost all `void` methods should be replaced with chainable constructs
which return `this`. This is certainly the case with setters as above. Consider the following setter pattern:

```java
public Foo withThing(Thing thing){
	this.thing=thing;
	return this;
}
```

which enables us to do

```java
foo.withThing(thing).withAnotherThing(another).withThat(that);
```
