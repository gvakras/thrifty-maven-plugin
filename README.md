# thrifty-maven-plugin

[![Build Status](https://www.travis-ci.org/timvlaer/thrifty-maven-plugin.svg?branch=master)](https://www.travis-ci.org/timvlaer/thrifty-maven-plugin)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square)](http://makeapullrequest.com)
[![Maven Central](https://maven-badges.herokuapp.com/maven-central/com.github.timvlaer/thrifty-maven-plugin/badge.svg)](https://maven-badges.herokuapp.com/maven-central/com.github.timvlaer/thrifty-maven-plugin)

Thin wrapper around the [thrifty-compiler](https://github.com/microsoft/thrifty/tree/master/thrifty-compiler).
This maven plugin generates the placeholder code for a thrift IDL file
using the [thrifty](https://github.com/microsoft/thrifty) library.

Advantages of using Thrifty over the regular Thrift distribution for Java code generation:
* All compilation happens on the JVM, no need to install Thrift binaries.
* Thrifty generates better java code: immutable objects with builders.
* Thrifty has a smaller footprint, the generated byte code is perfectly compatible.

## Usage

Add the following plugin to the `<build>` part of your Maven pom.xml file
```xml
<plugin>
    <groupId>com.github.timvlaer</groupId>
    <artifactId>thrifty-maven-plugin</artifactId>
    <version>0.4.0</version>
    <configuration>
        <thriftFiles>
            <file>thrift-schema/internal.thrift</file>
        </thriftFiles>
        <enableConvenienceMethods>true</enableConvenienceMethods>
    </configuration>
    <executions>
        <execution>
            <goals>
                <goal>thrifty-compiler</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

The generated code depends on `thrifty-runtime`, so add the following to your dependency list. 
```xml
<dependency>
    <groupId>com.microsoft.thrifty</groupId>
    <artifactId>thrifty-runtime</artifactId>
    <version>1.0.0</version>
</dependency>
```

## Generated convenience methods
This Maven plugin generates extra methods if you set `<enableConvenienceMethods>` to `true` (default is `false`). 

On all classes:
* `public static Builder builder()`, as a shortcut to `new StructName.Builder()` 
* `public static Builder builder(StructName prototype)`, as a shortcut to `new StructName.Builder(struct)` 
* `public Builder toBuilder()`, as a shortcut to `new StructName.Builder(struct)` 

On classes that are based on Thrift `union` types:
* `public String tag()` which returns the name of the filled field
* `public Object value()` which returns the value of the filled field (untyped)
