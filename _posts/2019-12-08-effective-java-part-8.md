---
layout: post
title: "Effective Java - 3rd Edition - General Programming"
date: 2019-12-15
excerpt: "What are the best practices for Java developer ?"
tags: Java Effective-Java Best-Practices
feature: /images/effective-java/effective-java-book.jpg
comments: false
---

The following post is a resumé of the best practices I understand when reading "Effective Java 3" of __Joshua BLOCH__.

# Item 57 : Minimize the scope of local variables
- The most powerful technique of minimizing the scope of a local variable is to declare it where it is first used. Declaring a local variable prematurely can cause its scope not only to begin too early, but also to end too late.
- Nearly every local variable declaration should contain an initializer. If you don't yet have enough information to initialize a variable sensibly, you should postpone the declaration until you do. In case of `try-catch`, it is possible to declare variable before `try-catch` block
- Keep method small and focused. If you combine two activities in the same method, local variable relevant to one activity may be in the scope of code performing the second activity.
- Prefer `for` loops to `while` loops
```java
    // Avoid
    Iterator<String> iterator = elements.iterator() ;
    while (iterator.hasNext()) {
        String element = iterator.next() ;
    }

    // Prefer
    for (Iterator<String> it = elements.iterator();it.hasNext();) {
        String element = it.next() ;
    }
```

# Item 58 : Prefer `for-each` to traditional `for` loops
The preferred idiom for iterating over a collections and arrays is the for-each loop officially known as *enhanced for statement* :
```java
    for (String element : elements) {
      System.out.println(element);
    }
```

Unfortunately, there are three common situations where you can't use-for-each :
- __Destructive filtering__
```java
    List<String> elements = Lists.newArrayList() ;

    // Iterator remove method
    for(Iterator<String> it = elements.iterator();it.hasNext();){
        String element = it.next() ;
        if (element.isEmpty()) {
            it.remove();
        }
    }

    // can be replaced by
    elements.removeIf(String::isEmpty);
```
- __Transforming__ : You need the list iterator or array index in order to replace the value of an element
- __Parallel iteration__ You need explicit control over the iterator

# Item 59 : Know and use libraries
By using standard libraries :
- you take advantage of the knowledge experts who wrote it and the experience of those who used it before you.
- you don't waste your time writing ad hoc solution
- Standard libraries performance tends to improve over time
- Standard libraries tend to gain functionality over time
- you place your code in the mainstream

Numerous features are added to the libraries in every major release and it pays to keep abreast of the additions. Programmers should be familiar with the basic of java packages :
- `java.lang`
- `java.util`
- `java.io`

knowledge of other libraries can be acquired on an as-needed basis.

If you can't find what you need in java platform, your next choice should be to look in high quality third party libraries, such as Google's excellent open source Guava library.

# Item 60 : Avoid `float` and `double` if exact answers are required
The `float` and `double` types are particularly ill-suited for monetary calculations because it is impossible to represent 0.1(or any negative power of 10) as a `float` or `double` exactly. The following code `System.out.println(1.03-0.42);` prints `0.6100000000000001` as result.

Let's take the floowing example :
```java
    double funds = 1.00 ;
    int itemBought = 0 ;
    for (double price = 0.10; funds >=price; price+=0.10){
        funds-= price ;
        itemBought++ ;
    }
    System.out.println("Item count "+itemBought);
    System.out.println("Funds " + funds);
```

This code prints :
```
Item count 3
Funds 0.3999999999999999
```


The right way to  solve this problem is to use `BigDecimal`, `int` or `long` for monetary calculations.
```java
    BigDecimal funds = new BigDecimal(1.00);
    int itemBought = 0 ;
    for (BigDecimal price = TEN_CENTS; funds.compareTo(price) >=0; price.add(TEN_CENTS)){
        funds = funds.subtract(price) ;
        itemBought++ ;
    }
```

The code prints
```
Fund=1 price=0.1000000000000000055511151231257827021181583404541015625
Fund=0.8999999999999999944488848768742172978818416595458984375 price=0.1000000000000000055511151231257827021181583404541015625
Fund=0.7999999999999999888977697537484345957636833190917968750 price=0.1000000000000000055511151231257827021181583404541015625
Fund=0.6999999999999999833466546306226518936455249786376953125 price=0.1000000000000000055511151231257827021181583404541015625
Fund=0.5999999999999999777955395074968691915273666381835937500 price=0.1000000000000000055511151231257827021181583404541015625
Fund=0.4999999999999999722444243843710864894092082977294921875 price=0.1000000000000000055511151231257827021181583404541015625
Fund=0.3999999999999999666933092612453037872910499572753906250 price=0.1000000000000000055511151231257827021181583404541015625
Fund=0.2999999999999999611421941381195210851728916168212890625 price=0.1000000000000000055511151231257827021181583404541015625
Fund=0.1999999999999999555910790149937383830547332763671875000 price=0.1000000000000000055511151231257827021181583404541015625
Item count 9
Funds 0.0999999999999999500399638918679556809365749359130859375
```

The are two main disadvantages to using `BigDecimal` :
- it's a lot less convenient than using a primitive arithmetic type
- it is a lot slower

# Item 61 : Prefer primitive types to boxed primitives
Java has two part type system, consisting of
- *primitives* : `long`, `int`,`double` and `boolean`
- *reference type* : `String` and `List`

Let's take the following example of comparator :
```java
    Comparator<Integer> naturalOrder = (i, j) -> (i < j) ? -1 : ((i == j) ? 0 : 1);
```

The comparison of `Integer` instances `i==j` will return false even if `i` and `j` refers to the same value.

This code `System.out.println(naturalOrder.compare(new Integer(1),new Integer(1)));` prints `1`.

One way to fix this code in java 11 is to use `Integer.ValueOf()` method :
```java
    System.out.println(naturalOrder.compare(Integer.valueOf(1),Integer.valueOf(1)));
```

Auto-boxing and auto-unboxing causes many performances issues when implementing comparators. You should simply call `Comparator.naturalOrder` or the static comparator on primitive types `Integer.compare`.

When you mix primitives and boxed primitives in an operation, the boxed primitive is auto-unboxed. If a null object reference is auto-unboxed, you get a `NullPointerException`.

```java
    static Integer input ;

    public static void main(String[] args) {

        if (input == 1) { // throws NullPointerException
            System.out.println("Eq 1");
        }

    }
```

> Be careful ! Auto-boxing reduces the verbosity, but not the danger of using boxed primitives

# Item 62 : Avoid strings where other types are more appropriate
- Strings are designed to represent text, and they do fine job of it.
- Strings are poor substitutes for other value types. If there's an appropriate value type whether primitive or object reference, you should use it.
- String are poor substitutes for enum types.
- String are poor substitutes for aggregate types. If an entity has multiple components, it is usually a bad idea to represent it as a single string.
- String are poor substitutes for capabilities. Occasionally, strings are used to grant access to some functionality.

# Item 63 : Beware the performance of `String` concatenation
The String concatenation operator `+` is a convenient way to combine a few strings into one. Using the String concatenation operator repeatedly to concatenate *n*  strings requires time quadratic *n*. This is an unfortunate consequence of the fact that strings are *immutable* : when two strings are concatenated, the contents of both are copied.

To achieve acceptable performance, use a `StringBuilder` in place of `String` to store the statement under construction.

The following example takes 42 ms
```java
    String result = "" ;
    for (int i=0; i < 1000 ; i++) {
        result+=names[i] ;
    }
    System.out.println(result);
```

Using `StringBuilder`, the following code takes 1ms
```java
    StringBuilder builder = new StringBuilder() ;
    for (int i=0; i < 1000 ; i++) {
        builder.append(names[i]) ;
    }
    System.out.println(builder.toString());
```

# Item 64 : Refer to objects by their interfaces
If appropriate interface type exist, then parameters, return values variables and fields should all be declared using interface types. The program will be much more flexible.

```java
    // Good use of interface
    Set<String> elements = new HashSet<>() ;

    // Bad use
    HashSet<String> badType = new HashSet<>() ;
```

It is entirely appropriate to refer to an object by a class rather than an interface if no appropriate interface exists :
- Final classes such as `String` and `BigInteger`
- Value classes with rarely multiple implementation
- Frameworks whose fundamental types are classes rather than interfaces
- Classes that implements interface but also provide extra methods not found in the interface, for example `PriorityQueue`.

# Item 65 : Prefer interfaces to reflexion
The *core reflexion facility*, `java.lang.reflect` offers programmatic access to arbitrary classes. Given a class `Object`, you can obtain `Constructor`, `Method` and field instances. You can construct instances, invokes methods and access fields.

Reflexion allows one class to use another, even if the latter class did not exist when the former was compiled, however, this power comes at a price :
- You loose all the benefits of compile-time checking
- The code is verbose
- Performance suffers (invocation is slower)

You can obtain many of the benefits of reflexion while incurring few of its costs by using it only in a very limited form. You create instances reflectively and access them normally via their interface or superclass.

# Item 66 : Use native methods judiciously
The Java Native Interface (JNI) allows programs to call *native methods*, which are methods written in native programming language such as C or C++.
It is rarely advisable to use native methods for improved performance.

The use of native methods has *serious* disadvantages :
- Because native language are not *safe*, applications using native methods are no longer immune to memory corruption errors.
- They are harder to debug

# Item 67 : Optimize judiciously
These are three aphorisms concerning optimization that everyone should know :
- More computing sins are committed in the name of efficiency (without necessarily achieving it) than for any other single reason - including blid stupidity -- William A Wulf
- We should forget about small efficiencies, say about 97% of the time : premature optimization is the root of all evil -- Donald E Knuth
- We follow two rules in the matter of optimization :
  1. Rule 1 : Don't do it
  2. Rule 2 : (for expert) Don't do it yet-that is, not util you have a perfectly clear and unoptimized solution.
-- M. A. Jackson

## How to ?
- Strive to write good programs rather than fast ones. Good programs embody the principle of *information hiding*.
- Strive to avoid design decisions that limit performance
- Consider the performance consequences of you API design decisions (making public type immutable, using inheritance rather than composition can place artificial limits on the performance)
- It is very bad idea to wrap an API to achieve good performance. The performance issue may go away in a future release, but, the wrapper will be with you forever.
- Measure performance before and after each attempted optimization. Often, attempted optimizations have no measurable effect on performance; sometimes they make it worse. Common wisdom says that program spend 90% of their time in 10% of their code
- Profiling tools can help you decide where to focus your optimization
- The *abstraction gap* between what the programmer write and what the CPU executes is great, which makes it even more difficult to reliably predict the performance consequences of optimization
- Java performance models varies from implementation to implementation. It is important that you measure the effects of your optimization on each.

# Item 68 : Adhere to generally accepted naming conventions
The Java platform has well-established set of naming conventions, which fall into two categories : typographical and grammatical :
- Components should consist of lowercase alphabetic characters and rarely digits
- The name of the package that will be used outside your organization should begin with your organization's Internet domain name with the component reversed (`net.dammak`). Acronyms are accepted.
- Class and interface names, including enums and annotation type names, should consist of one or more words, with the first letter of each word capitalized. Abbreviations are to be avoided.
- Method and field names follow the same typographical conventions as class, except that the first letter should be lowercase.
- Constant name should consist of one or more uppercase words separated by underscore.
- Type parameter names usually consist on a single letter


Grammatical naming conventions are more flexible and more controversial than typographical conventions :
- No grammatical naming convention for packages
- Instantiable classes are named with singular noun or noun phrase
- Non instantiable utility classes are often named with a plural noun (`Collectors`, `Collections`)
- Interface are named like classes or with an adjective ending in `able` or `ible` for example `Runnable`, `Iterable`, `Accessible`...
- Because annotation types have so many uses, no part of speech predomintaes
- Nouns, verbs, prepositions and adjectives are all commons (`BindingAnnotation`, `Inject`...)
- Methods that performs some actions are generally named with a verb
- Methods that return a `boolean` value have usually names that begin with `is` or `has`
- Methods that return a non-boolean function function or attribute of the object are usually named with a noun, a noun phrase or a verb phrase beginning with the verb `get` for example `size` , `getTime`, `hashCode`
- Instance methods that convert the type of an object returning an independent object of a different type are often called `toType` for example `toString`
- Methods that return a *view* whose type differs from that of the receiving object are called `asType`; for example `asList`
- Methods that return a primitive with the same value as the object called `typeValue`; for example `intValue`
- Common names of static factories include `from`, `valueOf` , `instance`, `getInstance` ...
- Grammatical conventions for field names are less well established and less important than those for classes.
- Grammatical conventions for local variables are similar to those for fields but even weaker.
