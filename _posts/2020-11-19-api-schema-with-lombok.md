---
layout: post
title:  "Creating POJOS with Lombok and Javax.Validate"
date:   2020-11-19
categories: guides java
tags:
- java
- lombok
- api
---

This is how to create an API schema with Lombok.
Lombok addresses the main ergonomic issue with Java, which is writing
code that is necessary, but not necessarily worth the time to write it.
I'm referring to setters, getters, builders, and logging utilities.
Lombok reduces the number of lines you have to write and lets
you focus on better problems. Lombok is like the poor man's Kotlin.

The next big target is validation. It's one thing to have a POJO structure
and populate it with data, but you still need to guarantee that data is in
the correct format. Traditionally, we would write a util method with 
a lot of ifs and elses, making sure no strings are null and all dates
fall within this century. Just like with getters and setters, this is
a crucial aspect of any project, but one that is routine - almost
standardised - and again we lose time by having to write these rules 
by hand.

Before we get to the code, let me stress that this is not the best
way to do things. In fact, there is no best way to do anything.
There's only what works for you, your team, and your project. These
techniques will get you 90% of the way, but no library saves us
from dealing with special cases.

## Constraints and Validation

Validation is easy to add to existing projects, so we will start with
that.

Let's write an `Article`. Our requirements are simple: We need the text
body, creation date, author, and tags.

```java

public class Article {
    private final String body;
    private final String author;
    private final LocalDateTime createdAt;
    private final List<String> tags;
}
```

Our validation rules are:

1. Fields must not be null
1. String fields must not be empty
1. Creation date must not be in the future

Simple, right? Let's introduce `javax.utils.validation`:

```java
import javax.validation.constraints.NotBlank;
import javax.validation.constraints.NotNull;
import javax.validation.constraints.PastOrPresent;


public class Article {
    @NotBlank
    private final String body;

    @NotBlank
    private final String author;

    @PastOrPresent
    private final LocalDateTime createdAt;

    @NotNull
    private final List<String> tags;
}
```

We're almost there. There's one detail we haven't addressed:

```java
    @NotNull
    private final List<String> tags;
```

The `@NotNull` constraint applies to the `tags` object, but not its contents.
We can fix this like so:

```java
    @NotNull
    private final List< @NotBlank String> tags;
```

All good. We've written our POJO, added the necessary constraints, and now it's time to validate it.
We will do this in a separate `Utils` class.

```java
import javax.validation.ConstraintViolation;
import javax.validation.Validation;
import javax.validation.Validator;

public class Utils {
    public static final boolean validate(Article obj) {
        final Validator validator = Validation
            .buildDefaultValidatorFactory()
            .getValidator();

        // Validate object and print errors to stderr
        return obj != null && !validator.validate(obj)
            .stream()
            // in the code repo the error message is properly formatted
            .map(ConstraintViolation::getMessage)
            .peek(System.err::println)
            .findAny()
            .isPresent(); // No constraint violations equals a happy object
    }
}

```

The method `Validator#validate` returns a `Set<ConstraintViolation<T>>`.
This is populated with error messages if any constraints are broken. So,
if the returned set is empty, validation was successful.

### Built-In Constraints



* `@NotNull` Annotated property value is not null
* `@NotEmpty` Annotated property value is not null or empty
* `@NotBlank` Annotated String property value is not null or whitespace
* `@Email` Annotated property is a valid email address
* `@AssertTrue`, `@AssertFalse` Annotated property value is true or false
* `@Size(min="0", max="10")` Annotated property value has a size between min and max; can be applied to String, Collection, Map, and arrays
* `@Min` Annotated property has a value no smaller than min
* `@Max` Annotated property has a value no larger than max
* `@Positive`, `@PositiveOrZero` Used with numeric properties
* `@Negative`, `@NegativeOrZero` Used with numeric properties
* `@Past`, `@PastOrPresent` Used with date properties
* `@Future`, `@FutureOrPresent` Used with date properties

<sub>_[List source][ls]_</sub>

There are [many more][ac] constraints available, for example, `@DurationMin`, 
`@Currency`, `@CreditCardNumber` to name a few. You can create custom
constraints to deal with special cases, or if you're feeling particularly
lazy, [just do Javascript][bc]:

```java
public class Car {

    @ParameterScriptAssert(lang = "javascript", script = "luggage.size() <= passengers.size() * 2")
    public void load(List<Person> passengers, List<PieceOfLuggage> luggage) {
        //...
    }
}
```

That covers the validation aspect of POJOS. Let's have a look at Lombok and how it can help us.

## Lombok

Our `Article` has all the necessary fields, but no setters, getters or constructors. Let's fix that.


```java
import lombok.EqualsAndHashCode;
import lombok.Getter;

@Getter //All fields now have getters
@EqualsAndHashCode //Let Lombok generate an equals() method
public class Article {
    // ...
}
```

Now we can get the data, but we can't _set_ them. All `Article` fields
are `final` so we can't use `@Setter` here. We have to either write
a constructor, or write a builder, or both. Lombok can do this for us.

```java
import lombok.Builder;
import lombok.RequiredArgsConstructor;

@Getter //All fields now have getters
@Builder
@RequiredArgsConstructor
@EqualsAndHashCode //Let Lombok generate an equals() method
public class Article {
    // ...
}

```

If we use `@AllArgsConstructor` or `@RequiredArgsConstructor`, we have to
pay attention to the order in which we declared our fields. Arguments
in the generated constructor will be in the same order. For example:


```java
@AllArgsConstructor // Generate constructor that populates all fields
public class Foo {
    private int max;
    private Bar bar;
    private int min;

    //Constructor generated by Lombok. Argument order is the same as declaration order.
    public Foo(int max, Bar bar, int min);
}


@Builder // Generate a Builder for this class
@RequiredArgsConstructor // Generate constructor that populates required fields only
public class Bar {
    private String name;
    private int age;

    // Generated by Lombok. No fields are final, so we have no "required" arguments.
    public Bar();
}

```

Note that `Foo` uses `@AllArgsConstructor` and `Bar` uses `@RequiredArgsConstructor`.
For that reason, since `Bar` has no `final` fields, the generated constructor will
have no arguments.

Choosing between constructors and builders is largely a matter of preference. We
can even have both. For small objects I don't bother with builders at all. 

### Getting the Code Generated by Lombok

To see the de-lomboked output, you can either use the [command line][dlb] or
have Maven [do it for you][dlb-p].


[ac]: https://docs.jboss.org/hibernate/stable/validator/reference/en-US/html_single/#validator-defineconstraints-hv-constraints
[bc]: https://docs.jboss.org/hibernate/stable/validator/reference/en-US/html_single/#section-builtin-method-constraints
[ls]: https://www.baeldung.com/javax-validation#validation
[dlb]: https://projectlombok.org/features/delombok
[dlb-p]: https://github.com/awhitford/lombok.maven/blob/master/test-maven-lombok/pom.xml#L48
