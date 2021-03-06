---
layout: post
title:  "Generate Chainable Setters with IntelliJ IDEA"
date:   2018-06-26 15:00:00 +0100
categories: guides
header:
 external: https://images.unsplash.com/photo-1531449862596-16e4c62e2b10?ixlib=rb-0.3.5
tags:
- IntelliJ IDEA
- java
- template
---

I prefer my setteers to be chainable so I changed the default IntelliJ
IDEA template to get what I want. Here's how I did it

The rules: Our setters must start with `with` (instead of `set`)
and `return this` at the end.

Open up the code generator (<kbd>Alt</kbd>+<kbd>Insert</kbd>)

In the setter generator dialog, click the button with the three dots <kbd>⋮</kbd> next to the scheme field. Copy the default scheme (IntelliJ Default)
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

1. Return type changed from `void` to `$class.name`
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


The canonical setter form, generated by IntelliJ IDEA, is the following

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

I believe that almost all `void` methods can (and should) return the same object instead. This is certainly the case with setters, which are well-known constructs with no special logic to them.

Consider the following setter pattern:

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
