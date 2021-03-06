---
layout: post
title: "Effective Java - 3rd Edition - Serialization"
date: 2019-12-25
excerpt: "What are the best practices for Java developer ?"
tags: Java Effective-Java Best-Practices
feature: /images/effective-java/effective-java-book.jpg
comments: false
---

The following post is a resumé of the best practices I understand when reading "Effective Java 3" of __Joshua BLOCH__.

# Item 85 : Prefer alternatives to java serialization
A fundamental problem with serialization is that its attack surface is too big to protect : Object graphs are deserialized by invoking the `readObject` method on an `ObjectInputStream`. Even if you adhere to all relevant best practices and succeeded in writing serializable classes that are invulnerable to attack, your application may still be vulnerable.

Java Serialization is widely used :
- RMI (Remote Method Invocation)
- JMX (Java Management Extension)
- JMS (Java Messaging System)

Deserialization of untrusted stream can cause
- RCE (Remote code Execution)
- DoS (Denial of Service)
- ...

The best way to avoid serialization exploits is never to deserialize anything. There's no reason to use java serialization in any new system you write.

There are other mechanisms for translating between objects and byte sequences that avoid many of the dangers of java serialization. The leading cross-platform structured data representations are
- JSON : designed for browser-server communication (text based and human readable, data representation)
- Protobuf : protocol buffers for storing and interchanging structured data (binary and substantially efficient , offers schemas to document and enforce appropriate usage)

If you work on a legacy context with serialization, avoid to deserialize untrusted data. In particulaur, never accept RMI traffic from untrusted sources.

You can use the object deserialization filtering added in Java 9 (`java.io.ObjectInputFilter`). It lets you accept or reject centrain classes. Prefer whitelisting to blacklisting.

# Item 86 : Implement `Serializable` with great caution
- A major cost of implementing `Serializable` is that it decreases the flexibility to change a class's implementation once it has been released. A simple example of the constraints on evolution imposed by serializability concerns stream unique identifier, known as serial version UIDs. If you do not declare `serialVersionUID`, the system automatically generates it at runtime by applying a cryptographic hash function to the structure of the class.
- A second cost is that it increases the likelihood of bugs and security holes
- A third cost is that it increases the testing burden associated with releasing new version of a class

Implementing `Serializable` is not a decision to be undertaken lightly. Each time you design a class, weigh the costs against the benefits.

Classes designed for inheritance should rarely implement `Serializable` and interfaces rarely extend it.

Inner classes should not implement `Serializable`. They use compiler generated synthetic fields to store references to enclosing instances and to store values of local variables from enclosing scopes. A static member class can implement `Serializable`.

# Item 87 : Consider using a custom serialized form
Do not accept the default serialized form without first considering wether it is appropriate. Accepting default serialization form should be a conscious decision that this encoding is reasonable from standpoint of flexibility, performance and correctness.

The default serialized form of an object is a reasonably efficient encoding, describing data contained in the object and in every reached object, and the topology by which all of this objects are interlinked. The ideal serialized form of an object contains only logical data.

The default serialized form is likely to be appropriate if an object's physical representation is identical to its lofical content.
```java
    class GoodCandidate implements Serializable {
      /** Must be non-null
       * @serial
       */
      private final String lastName ;
      /**
       * @serial
       */
      private final String firstName ;
      /**
       * @serial
       */
      private final String middleName ;
    }
```

The instance fields in `GoodCandidate` precisely mirror this logical content.

Even if you decide that the default serialized form is appropriate, you often must provide a `readObject` method to ensure invariants and security. In this case, the `readObject` must ensure that `lastName` is not null.

This is an awful candidate for serialization :
```java
    class AwfulCandidate implements Serializable {

      private int size = 0 ;
      private Entry head = null ;

      private static class Entry implements Serializable {
          String data ;
          Entry next ;
          Entry previous ;
      }

    }
```

Logically, this class represents a sequence of strings. Physically, it represents the sequence as doubly linked list. Accepting default serialized form, the serialized form will painstakingly mirror every entry in the linked list and all the links between the entries, in both directions.

Using the default serialized form when an object's physical representation differs substantially from its logical data content has 4 disavantages :
- It permanently ties the exported API to the current internal representation
- It can consume excessive space
- It can consume excessive time
- It can cause stack overflows

Here's a reasonable serialized form of `AwfulCandidate` :
```java
    class AwfulCandidate implements Serializable {

        private int size = 0 ;
        private Entry head = null ;

        // No more Serializable
        private static class Entry {
            String data ;
            Entry next ;
            Entry previous ;
        }

        private void writeObject(ObjectOutputStream s) throws IOException {
            s.defaultWriteObject();
            s.writeInt(size);

            // write all elements
            for (Entry e = head ; e!= null ; e= e.next) {
                s.writeObject(e.data);
            }
        }

        private void readObject(ObjectInputStream s) throws IOException, ClassNotFoundException {
            s.defaultReadObject();
            int  numElements = s.readInt() ;

            // Read All elements
            for (int i= 0 ; i< numElements; i++) {
                add((String) s.readObject()) ;
            }

        }

        // Appends the specific string ti the list
        private void add(String s) { }

    }
```

The serialization specification requires to invoke `defaultWriteObject` and `defaultReadObject` even if all fields are `transient`.

Consider the case of hash table : The physical representation is a sequence of hash buckets containing key-value entries, which is not in general guaranteed to be the same from implementation to implementation. Therefore, accepting the default serialized form for a hash table would constitute a serious bug !

Every instance field that isn't labelled `transient` will be (de)serialized. Therefore, every instance field that can be declared `transient` should be. This includes derivate fields (values computed from primary data). Before deciding to make an object non transient, convince yourself that its value is part of the logical state of the object.

Remember that `transient` fields will be initialized to their default values. You can provide o `readObject` method that restores transient fields to acceptable values, or alternatively, lazily initialize those fields first time they are used.

You must impose any synchronization on object serialization that you would impose on any other method that reads the entire state of the object. If you have thread-safe object that achieves thread safety by synchronizing every method, and you elect to use default form, use this :
```java
    private synchronized void readObject(ObjectInputStream s) throws IOException {
      s.defaultReadObject();
    }
```

Regardless of what serialized form you choose, declare an explicit serial version UID in every serializable class you write. Do not change the serial version UID unless you want to break compatibility with existing serialized instances of a class.

# Item 88 : Write `readObject` methods defensively
- Provide a `readObject` method that checks the validity of deserialized object, to prevent from violating class invariants.
- When an object is deserialized, it is critical to defensively copy any field containing an object reference that a client must not process. This practive prevents from *rogue object references*. Note that defensive copying is not possible for `final` fields.
- `readObject` method (and constructor) should not invoke an overridable method

## Take away
Anytime you write a `readObject` method, adopt the mind-set that you are writing a public constructor that must produce a valid instance :
- For classes with object reference fields that must remain private, copy each object in such a field. Mutable component of immutable classes fall into this category
- Check any invariants and throw an `InvalidObjectException` if a check fails
- If an entire object graph must be validated after it sis deserialized, use the `ObjectInputValidationInterface`
- Do not invoke any overridable methods in the class directly or indirectly

> It is highly recommended to use *serialization proxy pattern* of safe deserialization.

# Item 89 : For instance control, prefer enum types to `readResolve`
The `readResolve` feature allows you ti substitute another instance for the one created by `readObject`. If the class of an object defines a `readResolve` method with the proper declaration, this method is invoked on the newly created object after it is deserialized. The object reference returned by this method is then returned in place of the newly created object.

If you depend on `readResolve` for instance control, all instance fields with object reference types must be declared `transient`.

If you write your serializable instance-controlled class as an enum, Java guarantees you that there can be no instances besides the declared constants.

If you have to write a serializable instance-controlled class whose instances are not known at compile time, you will not be able to represent the class as an enum type.

The accessibility of `readResolve` is significant. If you place a `readResolve` in a `final` class, it should be `private`. If you place `readResolve` on a non final class, you must carefully consider its accessibility for subclasses. If it is `private`, deserializing a subclass will produce a superclass instance, which is likely a `ClassCastException`.

# Item 90 : Consider serialization proxies instead of serialized instances
The serialization proxy pattern is reasonably straightforward :
- Design a private static nested class (serialization proxy). It should have a single constructor whose parameter type is the enclosing class
- Proxy and enclosing class must implement `Serializable`

```java
    class Period implements Serializable{


        private static class SerializationProxy implements Serializable {
            private final Date start ;
            private final Date end ;
            SerializationProxy(Period p) {
                this.end = p.end ;
                this.start=p.start ;
            }
            private static final long serialVersionUID = 123123L ;

            private Object readResolve() {
                return new Period(start,end) ;
            }

        }


        private final Date start ;
        private final Date end ;

        public Period(Date start, Date end) {
            this.start = new Date(start.getTime());
            this.end = new Date(end.getTime());
        }

        // write replace method for the serialization proxy pattern
        private Object writeReplace() {
            return new SerializationProxy(this) ;
        }

        // Prevent attacks
        private void readObject(ObjectInputStream s) throws IOException {
            throw new InvalidObjectException("Proxy required !") ;
        }
    }
```

With the `writeReplace` method, the serialization system will never generate a serialized instance of  the enclosing class. To guarantee that no attack of enclosing type, the `readObject` throws exception.

The `readResolve` on proxy creates an instance of the enclosing class using only its public API.


This proxy stops the bogus byte-stream attack and the internal theft attack dead in their tracks.

The serialization proxy pattern is more powerful than defensive copying in `readObject`.

The serialization proxy pattern has two limitations :
- it is not compatible with classes that are extendable by their users
- it is not compatible with classes whose object graphs contain circularities

If you attempt to invoke proxy's `readResolve` method, you'll get a `ClassCastException` because you don't have the object yet, only its serialization proxy.

The add of proxy is not free, it is around 14% more expensive to serialize and deserialize.
