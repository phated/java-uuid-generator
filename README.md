#  Java Uuid Generator (JUG)

JUG is a set of Java classes for working with UUIDs: generating UUIDs using any of standard methods, outputting
efficiently, sorting and so on.
It generates UUIDs according to the [UUID specification (RFC-4122)](https://tools.ietf.org/html/rfc4122)
(also see [Wikipedia UUID page](http://en.wikipedia.org/wiki/UUID) for more explanation)

JUG was written by Tatu Saloranta (<tatu.saloranta@iki.fi>) originally in 2002 and has been updated over years.
In addition, many other individuals have helped fix bugs and implement new features: please see `release-notes/CREDITS`
for the complete list.

JUG is licensed under [Apache License 2.0](http://www.apache.org/licenses/LICENSE-2.0.html).

## Status

[![Build Status](https://travis-ci.org/cowtowncoder/java-uuid-generator.svg)](https://travis-ci.org/cowtowncoder/java-uuid-generator)
[![Javadoc](https://javadoc-emblem.rhcloud.com/doc/com.fasterxml.uuid/java-uuid-generator/badge.svg)](http://www.javadoc.io/doc/com.fasterxml.uuid/java-uuid-generator)
[![Tidelift](https://tidelift.com/badges/package/maven/com.fasterxml.uuid:java-uuid-generator)](https://tidelift.com/subscription/pkg/maven-com-fasterxml-uuid-java-uuid-generator?utm_source=maven-com-fasterxml-uuid-java-uuid-generator&utm_medium=referral&utm_campaign=readme)

## Usage

JUG can be used as a command-line tool (via class 'com.fasterxml.uuid.Jug`), or as a pluggable component.

### Via Maven

Maven coordinates are:

```xml
<dependency>
  <groupId>com.fasterxml.uuid</groupId>
  <artifactId>java-uuid-generator</artifactId>
  <version>3.2.0</version>
</dependency>
```

### JDK9+ module info

Since version `3.2.0`, JUG defines JDK9+ compatible `module-info.class`, with module name of `com.fasterxml.uuid`.

### Downloads

For direct downloads, check out [Project Wiki](../../wiki).

### Using JUG

Generation itself is done by first selecting a kind of generator to use, and then calling its `generate()` method,
for example:

```java
UUID uuid = Generators.randomBasedGenerator().generate();
UUID uuid = Generators.timeBasedGenerator().generate();
```

If you want customize generators, you may also just want to hold on to generator instance, for example:
```java
TimeBasedGenerator gen = Generators.timeBasedGenerator(EthernetAddress.fromInterface());
UUID uuid = gen.generate();
UUID anotherUuid = gen.generate();
```

Generators are fully thread-safe, so a single instance may be shared among multiple threads.

JavaDocs for project can be found from [Project Wiki](../../wiki).

## Compatibility

JUG versions 3.1 and 3.2 require JDK 1.6 to work, mostly to be able to access local Ethernet MAC address.
Earlier versions (3.0 and before) worked on 1.4 (which introduced `java.util.UUID`).

## Known Issues

JDK's `java.util.UUID` has flawed implementation of `compareTo()`, which uses naive comparison
of 64-bit values. This does NOT work as expected, given that underlying content is for all purposes
unsigned. For example two UUIDs:

```
7f905a0b-bb6e-11e3-9e8f-000000000000
8028f08c-bb6e-11e3-9e8f-000000000000
```

would be ordered with second one first, due to sign extension (second value is considered to
be negative, and hence "smaller").

Because of this, you should always use external comparator, such as
`com.fasterxml.uuid.UUIDComparator`, which implements expected sorting order that is simple
unsigned sorting, which is also same as lexicographic (alphabetic) sorting of UUIDs (when
assuming uniform capitalization).

## Support

Optional commercial support is available via [Tidelift](https://tidelift.com): see
[java-uuid-generator subscription](https://tidelift.com/subscription/pkg/maven-com-fasterxml-uuid-java-uuid-generator?utm_source=maven-com-fasterxml-uuid-java-uuid-generator&utm_medium=referral&utm_campaign=readme)
for details.

## Contributing

For simple bug reports and fixes, and feature requests, please simply use projects
[Issue Tracker](../../issues), with exception of security-related issues for which
we recommend filing
[Tidelift security contact](https://tidelift.com/security) (NOTE: you do NOT have to be
a subscriber to do this).

## Alternative JVM UUID generators

There are many other publicly available UUID generators. For example:

* [Apache Commons IO](http://commons.apache.org/sandbox/commons-id/uuid.html) has UUID generator
* [eaio-uuid](http://stephenc.github.io/eaio-uuid/)
* JDK has included `java.util.UUID` since 1.4, but omits generation methods (esp. time/location based ones), has sub-standard performance for many operations and implements comparison in useless way
* [ohannburkard.de UUID generator](http://johannburkard.de/software/uuid/)

Note that although some packages claim to be faster than others, it is not clear whether:

1. Claims have been properly verified (or, if they have, can be independently verified), AND
2. It is not likely that performance differences truly matter: JUG, for example, can generate a millions of UUID per second per core (sometimes hitting the theoretical limit of 10 million per second), and it seems unlikely that generation will be bottleneck for about any use case

so it is often best to choose based on stability of packages and API.
