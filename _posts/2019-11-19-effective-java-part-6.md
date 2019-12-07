---
layout: post
title: "Effective Java - 3rd Edition - Lambdas and Streams"
date: 2019-11-12
excerpt: "What are the best practices for Java developer ?"
tags: Java Effective-Java Best-Practices
feature: /images/effective-java/effective-java-book.jpg
comments: false
---

The following post is a resumé of the best practices I understand when reading "Effective Java 3" of __Joshua BLOCH__.

# Item 42 : Prefer lambdas to anonymous classes
Historically, interfaces with single abstract method were used as *functional types*.

Here is a code snippet using an anonymous class :
```java
    List<String> elements = List.of("A","B");
    Collections.sort(elements, new Comparator<>() {
        @Override
        public int compare(String o1, String o2) {
            return Integer.compare(o1.length(),o2.length()) ;
        }
    }) ;
```

Lambda expression as function replace anonymous classes :
```java
    Collections.sort(elements, (o1, o2) -> Integer.compare(o1.length(),o2.length())) ;
```

The comparator can be made even more succinct :
```java
    // using Comparator.comparingInt
    Collections.sort(elements, Comparator.comparingInt(String::length)) ;
    // using list sort method
    elements.sort(Comparator.comparingInt(String::length));
```

For the item 34, we can use `DoubleBinaryOperator` rather than `Function`.
```java
    public enum Operation {

        PLUS ((a,b) -> a + b),
        MINUS ((a,b) -> a - b),
        TIMES ((a,b) -> a * b),
        DIVIDE ((a,b) -> a / b);

        private final DoubleBinaryOperator operation ;

        Operation(DoubleBinaryOperator operation) {
            this.operation = operation;
        }

        public Double compute(Double x, Double y) {
            return operation.applyAsDouble(x,y);
        }
    }
```

## Lambda properties
- If a computation isn't self-explanatory, or exceeds a few lines (3 lines), don't put it in a lambda. One line is ideal for lambdas.
- Lambdas are limited to functional interfaces. If you want to create an abstract class, you can do it with anonymous class, but not in lambda.
- A lambda cannot obtain a reference to itself. the keyword `this` refers to the enclosing instance.
- Lambda share with anonymous classes the property that you can't reliably serialize and deserialize them across implementations. If you have a function object, use an instance of a private static nested class.


# Item 43 : Prefer method reference to lambdas
Java provides a way to generate objects even more succinct than lambdas : __method references__.

Let's take the following example that increments each map value with `1`. The output is `{1=2, 2=3, 3=4}`
```java
    Map<Integer,Integer> map = Maps.newHashMap();
    // Init map
    IntStream.rangeClosed(1,3).forEach(i -> map.put(i,i));
    // Increment each map value with 1
    IntStream.rangeClosed(1,3).forEach(i -> map.merge(i,1, (initialValue,incrementToAdd) -> initialValue + incrementToAdd));
    System.out.println(map);
```

We can simple pass a method reference to the `map.merge` using :
```java
    map.merge(i,1, Integer::sum);
```

The code is shorter and cleaner. However, in lambda, the parameter names you choose can provide useful documentation.

If you are programming with IDE, it will offer to replace a lambda with a method reference wherever it can.

The `Function` interface provides a static factory for identity method `Function.identity()`, which is equivalent to `x -> x`.

## Method type reference
- Static type : `Integer::parseInt` == `str -> Integer.parseInt(str)`
- In bound reference, the receiving object is specified in the method reference : `Instant.now()::isAfter` == `t -> Instant.now().isAfter(t)`.
- In unbound references, the receiving object is specified when the function is applied : `String::toLowerCase` == `s -> s.toLowerCase()`
- Class Constructor : `TreeMap<K,V>::new` == `() -> new TreeMap<K,V>`
- Array Constructor : `int[]::new` == `len -> new int[len]`.

# Item 44 : Favor the use of standard functional interfaces
The `java.util.function` package provides 43 standard functional interfaces. If one of the standard functional interface does the job, you should generally use it in preference to a purpose-built functional interface.

The basic interfaces operate on object reference types :
- `Operator` interface represents function whose result and argument types are same : `T apply(T t)`
- `Predicate` interface represents a function that takes an argument and return a `boolean` : `boolean test(T t)`
- `Function` interface represents a function whose argument and return types differ : `R apply(T t)`
- `Supplier` interface represents a function that takes no arguments and returns (supplies) a value : `T get()`
- `Consumer` represents a function that takes an argument and return nothing : `void accept(T t)`

There are also 3 variants of each of the six basic interfaces to operate on the primitive types : `int`, `double` and `long`. their names are derived form the basic interfaces : `IntPredicate`, `LongBinaryOperator`, `LongFunction`...
```java
    LongFunction longFunction = l -> l -1 ;
    System.out.println(longFunction.apply(10l));
```

There are 9 additional variants if the `Function` interface, for use when the result type is primitive, eg `LongToIntFunction`
```java
    LongToIntFunction function = l -> (int) l * 100;
    System.out.println(function.applyAsInt(1000000000000000l));
```

There are 2 arguments versions if the three basic functional interfaces : `BiPredicate<T,U>`, `BiFunction<T,U,R>`,`BiConsumer<T,U>`
```java
    BiPredicate<Integer,Integer> biPredicate = (i,j) -> i+j < 10 ;
    System.out.println(biPredicate.test(9,2));

    BiFunction<Integer,Double,String> biFunction = (i,d) -> i+" - "+d ;
    System.out.println(biFunction.apply(10,2.1d));
```

There are also `BiFunction<T,U>` variants returning the 3 relevant primitive types : `ToIntBiFunction<T,U>`,`ToLongBiFunction<T,U>` and `ToDoubleBiFunction<T,U>`
```java
    ToDoubleBiFunction<Integer,Double> toDoubleBiFunction = Math::pow;
    System.out.println(toDoubleBiFunction.applyAsDouble(2,2d));
```

There are two-argument variants of `Consumer` that take an `Object` reference and one primitive type; `ObjIntConsumer<T>` and `ObjLongconsumer<T>` :
```java
    ObjLongConsumer<Account> accountObjLongConsumer = (a,value) -> a.accountEur -= value ;
    Account account = Account.with(100) ;
    accountObjLongConsumer.accept(account,10);
    System.out.println(account.accountEur);
```

There is the `BooleanSupplier` interface, a variant of `Supplier` that returns `boolean` ;
```java
    BooleanSupplier booleanSupplier = () -> account.accountEur >= 0 ;
    System.out.println(booleanSupplier.getAsBoolean());
```

Don't be temped to use basic functional interfaces with boxed primitives instead of primitive functional interfaces. While it works, it violates advice in item 61 about "Prefer primitive types to boxed primitives".

You need to write your own functional interface if none of the standard ones does what you need :
- It will be commonly used and could benefit from a descriptive name
- It has strong contract associated with it
- It would benefit from custom default method
- Always annotated with `@FunctionalInterface`

[Here](https://docs.oracle.com/javase/8/docs/api/java/util/function/package-summary.html) are the full list of functional interfaces.

# Item 45 : Use streams judiciously
The streams API was added to java 8 to ease the task of performing bulk operations, sequentially or in parallel.

A Stream pipeline consists of a source stream followed by zero or more *intermediate operations* and one *terminal operation*. Each intermediate operation transforms the stream in some way eg `map`, `filter`... The terminal operation performs a final computation on the stream resulting from the last intermediate operation eg `forEach`, `collect` ...

## Stream properties
- Stream operation are evaluated __lazily__ : Evaluation doesn't start until the terminal operation is invoked. this lazy evaluation is what makes it possible to work with infinite streams.
- The stream API is __fluent__ : Allows all the calls that comprise a pipeline to be chained
- By default, stream pipelines run sequentially. invoking `parallel` method make pipeline execute in parallel.
- Streams can make program shorter and cleaner

## Stream advices
- Overusing streams makes program hard to read and maintain. Prefer using helper method to improve readability
- Name carefully lambda parameters to improve readability of stream pipelines

When you start using Streams, you may feel the urge to convert all your loops into streams, but resist the urge. As rule, complex tasks are best accomplished using some combinations of streams and iterations. Use streams only where it make sense to do so.

## Difference streams vs blocks
- Stream pipelines express repeated computation using function objects, while iterative code expresses repeated computation using code blocks
- You can read or modify any local variable in scope using code block. You can only read final or effectively final variables, and you can't modify  from a lambda
- From a block code only, you can `return` from the enclosing method, `break` or `continue` enclosing loop

Streams make easy :
- Uniformly transform sequences of elements
- Filter sequences of elements
- Combine sequences of elements using operations
- Accumulate sequences of elements into a collection
- Search a sequence of elements

One thing is hard to do with stream is to access corresponding elements from multiple stages of a pipeline. Once you map a value to some other value, the original value is lost. One workaround is to map each value to a pair Object containing the original value and the new value.

There are plenty of tasks where it is not obvious to use streams. Let's take the following example
```java
    List<String> list1 = Lists.newArrayList() ;
    List<String> list2 = Lists.newArrayList() ;
    List<StrPair> result = Lists.newArrayList();
    for (String param1 : list1) {
        for (String param2 : list2) {
            result.add(new StrPair(param1,param2)) ;
        }
    }
```

Using streams:
```java
    List<StrPair> pairs = list1.stream().flatMap(param1 -> list2.stream().map(param2 -> new StrPair(param1,param2))).collect(Collectors.toList()) ;
```

Which is better ? It boils down to personal preference !

# Item 46 : Prefer side-effect-free functions
A pure function is one whose result depends only on its inputs. In order to achieve this, any function object that you pass into stream operations should be free of side effects.

Let's see the following example :
```java
    // Bad code -- Do not do that
    Map<String,Long> wordFrequency = Maps.newHashMap() ;
    try(Stream<String> words = new Scanner(file).tokens()) {
        words.forEach(word -> wordFrequency.merge(word,1L,Long::sum));
    }
```

This code is doing all its work in the terminal `forEach` operation. A `forEach` operation that does anything more than present the result is a __bad smell in code__. The `forEach` should not be to perform computation.

```java
    try(Stream<String> words = new Scanner(file).tokens()) {
        Map<String, Long> result = words.collect(Collectors.groupingBy(Function.identity(), Collectors.counting()));
    }
```

It is wise to statically import members of `Collectors` to improve readability of the code :
```java
    words.collect(Collectors.groupingBy(Function.identity(), Collectors.counting()));
    words.collect(groupingBy(Function.identity(), counting()));
```

The following code is using `comparing` method, to get top-10 list of words from the frequency table
```java
    List<String> top10Freq = wordFrequency.keySet().stream()
             .sorted(Comparator.comparing(wordFrequency::get).reversed())
             .limit(10)
             .collect(toList());
```

The `toMap` method is the easiest way to make a map from String. It takes 2 `Function` : `Function<? super T, ? extends K> keyMapper` and `Function<? super T, ? extends U> valueMapper` :
```java
    Map<String, String> map = listOfString.stream().collect(toMap(Object::toString, e -> e));
```

If multiple stream elements map to the same key, the pipeline will terminate with an `IllegalStateException`, with message `Duplicate key ... (attempted merging values ... and ...)`

To avoid this issue, it is possible to impose last-write-wins policy :
```java
    map = listOfString.stream().collect(toMap(Object::toString, e -> e,(oldValue,newValue) -> newValue));
```

The method `joining` operates only on streams of `CharSequence` instances such `String` :
```java
    String join = listOfString.stream().collect(joining(";"));
```

In order to use Streams properly, you should know about `Collectors` and the most important collector factories : `toList`, `ToSet`, `toMap`, `grouppingBy` and `joining`.

# Item 47 : Prefer Collection to Stream as a return type
Writing good code requires combining streams and iteration judiciously. If an API returns only a stream, and some users want to iterate over the returned sequence with a for-each, those users will be justifiably upset. The stream interface contains the sole abstract method in the `Iterable` interface, and stream's specification for this method is compataible with `Iterable`'s.

Given the following method
```java
    static Stream<Integer> getValues() {
        return IntStream.rangeClosed(1,5).boxed() ;
    }
```

The following code does not compile
```java
    Stream<Integer> values = getValues() ;
    for (Integer i : values::iterator) { // Not compile
    }
```

The better workaround is to use an adapter method which map `Stream<Integer>` to `Iterator<Integer>`:
```java
    static <E> Iterable<E> fromStream(Stream<E> stream) {
        return stream::iterator ;
    }
```

It is then possible to iterate :
```java
    Stream<Integer> values = getValues() ;
    for (Integer i : fromStream(values)) {
        // Some code ...
    }
```

This method map a `Iterable<E>` to a `Stream<E>` :
```java
    static <E> Stream<E> toStream(Iterable<E> iterable) {
        boolean parallel = false;
        return StreamSupport.stream(iterable.spliterator(),parallel) ;
    }
```

The `Collection` interface is subtype of `Iterable` and has `stream` method. Therefore, `Collection` or an appropriate subtype is generally the best return type for a public, sequence returning method. If you are writing a method that returns a sequence of objects and you know that it will only be used in `stream` pipeline, then, you should feel free to return a stream.

# Item 48 : Use caution when making streams parallel
Let's take the following example :
```java
    Stream.iterate(BigInteger.TWO,BigInteger::nextProbablePrime).limit(20).forEach(System.out::println);
```

Try to add `parallel()` method to this pipeline ? what happens is that the code will not print anything ! The system is in a *liveness failure*.

Therefore, parallelizing a pipeline is unlikely to increase its performance if the source is from `Stream.iterate`, or the intermediate operation `limit` is used. Do not parallelize stream pipeline indiscriminately.

As a rule, performance gains from parallelism are best on streams over `ArraysList`, `HashMap`, and `ConcurrentHashMap` instances; `arrays`; `int` ranges and `long` ranges. These data can be accurately and cheaply split into subranges of any desired sizes, which makes it easy to divide the work among parallel threads. The abstraction used by the streams library to perform this task is `splitrator` (splitting and Iterator).

These data have in common that they provide good-to-excellent *locality of reference* when proceeded sequentially : sequential element references are stored together in memory. The objects referred to by those references may not be close to one another in memory, which reduces locality-of-reference. Without locality-of-reference, threads spend much of their time idle, waiting for data to be transferred from memory into the processor's cache. Primtive arrays are the best data with locality-of-reference, because the data itself is stored contiguously in memory.

The nature of terminal operation affects also the effectiveness of parallel execution. Parallelizing the pipeline will have limited effectiveness if a significant amount of work is done in the terminal operation.

The best terminal operations for parallelism are *reductions* such as `count`, `min`, `max` and `sum`. The *short-circuitting* operation `anyMath`, `allMathc` and `noneMath` are also amenable to parallelism.

```java
    // Safe parallel reduce
    int sum = numbers.parallelStream().reduce(0, Integer::sum);
```

The operations performed by Stream's `collect` method, which are known as *mutable reduction* are not good candidate for parallelism because the overhead of combining collections is costly.

Not only can parallelizing a stream lead to poor performance, including  liveness failures; it can lead to incorrect results and unpredictable behavior (*safety failure*). Safety failure may result from parallelizing a pipeline that uses `map`, `filter` and other programmer-supplied function objects that fail to adhere to their specifications. the Stream specification places stringent requirements on these function objects. For example, the accumulator and combiner functions passed to Stream's `reduce` operation must be :
- associative `(a op b) op c == a op (b op c)`
- non-interfering
- stateless

See chapter [Reduction operations](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html)

To preserve the order displayed by the sequential version, you'd have to replace the `forEach` with `forEachOrdered` which is guaranteed to traverse parallel streams in *encounter order*.
```java
    System.out.println("Any Order");
    IntStream.rangeClosed(1,5).parallel().forEach(System.out::println);
    System.out.println("Ordered :");
    IntStream.rangeClosed(1,5).parallel().forEachOrdered(System.out::println);
```

The output is :
```
Any Order
3
4
1
5
2
Ordered :
1
2
3
4
5
```

You won't get a good speedup from parallelization unless the pipeline is doing enough real work to offset the cost associated with parallelism. As a very rough estimate, the number of elements in the stream times the number of lines of code executed per element should be at least a hundred thousand.

It is important to remember that parallelizing is performance optimization. You must test the performance before and after the change to ensure that is worth doing. Ideally, you should perform the test in a realistic system setting.

Let's take the following example where parallelism is effective :

``` java
static long compute(long n) {
    return LongStream.range(2,n)
            .mapToObj(BigInteger::valueOf)
            .filter(i -> i.isProbablePrime(50))
            .count() ;
}
```

For `n=10000000L`,
- it takes 19,211 seconds
- it take 5,373 with `parallel()` stream

If you are going to parallelize a stream of random numbers, start with a `SplittableRandom` instance rather than a `ThreadLocalRandom`. `SplittableRandom` is designed for precisely this use, and has the potential for linear speedup.
```java
    SplittableRandom random  = new SplittableRandom();
    random.ints().limit(20).forEachOrdered(System.out::println);
```
