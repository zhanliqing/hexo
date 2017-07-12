---
title: java Instrument 使用
date: 2017-03-08 16:57:25
tags: java
categories: java
---

简介
---

Java在Java SE 5中提供了Instrumentation ，Instrumentation提供了通过Java方法来跟虚拟机交互的接口 JVM Tool interface，实际上是提供了一套代理机制，通过独立于JVM应用程序之外的程序来扩展修改应用程序，通过它可以对现有程序做一些扩展，比如系统监控，通过修改字节码可以实现接口相应时间，方法耗时，代码覆盖率等等功能，这样的特性实际上提供了一种虚拟机级别支持的AOP实现方式，使得开发者无需对JDK做任何升级和改动，就可以实现某些AOP的功能了。在Java SE 5中，代理程序需要在启动应用程序的时候执行代理jar包，在应用程序启动之前启动代理程序，这就限制了这个方法的应用，因为在线上系统，一旦一个系统出问题，不可能重启后在加上代理程序来定位问题，或者在应用程序启动时就加上代理程序，但代理程序通常会修改class字节码来扩展功能，可能会对线上系统性能有影响。在Java SE 6里面instrumentation 被赋予了更强大的功能：启动后的 instrument、本地代码（native code）instrument，以及动态改变classpath 等等。


Java SE 5方式
-------------
[JVM TI](http://docs.oracle.com/javase/8/docs/platform/jvmti/jvmti.html)是一个编程接口，通过它可以监控和控制应用程序在JVM中的执行，提供了一些接口，包括调试，监控，线程分析，覆盖率分析等工具，Java 的Instrumentation就是JVM TI的Java实现，在Java SE 5中，使用代理程序需要在类里边有一个premain方法，有两种定义

```java
public static void premain(String agentArgs, Instrumentation inst);
public static void premain(String agentArgs);
```
其中上边的有更高的优先级    

在应用启动时启动代理程序，需要用如下的命令

```java 
java -javaagent:jarpath option java_main_class
```

并且jar包有要求，需要在manifest文件中加上Premain-Class属性，值为代理类的全路径

下边通过一个简单的例子来演示应用，通过它来演示JVM加载了哪些类

* 首先定义代理类

```java
package com.zlq;

import java.lang.instrument.ClassFileTransformer;
import java.lang.instrument.IllegalClassFormatException;
import java.lang.instrument.Instrumentation;
import java.security.ProtectionDomain;

/**
 * Created by liqing.zhan on 2017/3/9.
 */
public class LoadClassInst {
    public static void premain(String agentArgs, Instrumentation inst) {
        System.out.println("LoadClassInst premain start");
        inst.addTransformer(new LoadClassTransformer());
        System.out.println("LoadClassInst premain end");
    }
}

class LoadClassTransformer implements ClassFileTransformer {

    public byte[] transform(ClassLoader loader, String className, Class<?> classBeingRedefined,
                            ProtectionDomain protectionDomain, byte[] classfileBuffer)
            throws IllegalClassFormatException {
        System.out.println(String.format("load class %s", className));
        return classfileBuffer;
    }
}

```

* 定义应用程序

```java
package com.zlq;

/**
 * Created by liqing.zhan on 2017/3/9.
 */
public class Main {
    public static void main(String[] args) {
        System.out.println(Main.class.getSimpleName());
    }
}
```

* 将代理类打成jar包，pom文件如下

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.zlq</groupId>
    <artifactId>test</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>jar</packaging>

    <name>test</name>
    <url>http://maven.apache.org</url>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-jar-plugin</artifactId>
                <version>2.3.1</version>
                <configuration>
                    <archive>
                        <manifest>
                            <addClasspath>true</addClasspath>
                        </manifest>
                        <manifestEntries>
                            <Premain-Class>
                                com.zlq.LoadClassInst
                            </Premain-Class>
                        </manifestEntries>
                    </archive>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

* 执行main方法

```java
$ java  -javaagent:test-1.0-SNAPSHOT.jar com.zlq.Main
LoadClassInst premain start
LoadClassInst premain end
load class java/lang/invoke/MethodHandleImpl
load class java/lang/invoke/MemberName$Factory
load class java/lang/invoke/LambdaForm$NamedFunction
load class java/lang/invoke/MethodType$ConcurrentWeakInternSet
load class java/lang/invoke/MethodHandleStatics
load class java/lang/invoke/MethodHandleStatics$1
load class java/lang/invoke/MethodTypeForm
load class java/lang/invoke/Invokers
load class java/lang/invoke/MethodType$ConcurrentWeakInternSet$WeakEntry
load class java/lang/Void
load class java/lang/IllegalAccessException
load class sun/misc/PostVMInitHook
load class sun/launcher/LauncherHelper
load class com/zlq/Main
load class sun/launcher/LauncherHelper$FXHelper
load class java/lang/Class$MethodArray
Main
load class java/lang/Shutdown
load class java/lang/Shutdown$Lock
```

Java Attach API
--------------

使用[Java Attach API](https://blogs.oracle.com/CoreJavaTechTips/entry/the_attach_api) 
可以获取目标JVM的一个引用，通过这个引用，就可以操纵JVM。 在两个包下边，com.sun.tools.attach和com.sun.tools.attach.spi，VirtualMachine这个类标示一个JVM实例，需要通过进程ID来关联，拿到JVM的实例，就可以调用loadAgent(age)来扩展功能，例如：

```java
VirtualMachine vm = VirtualMachine.attach (processid);
String agent = ...
vm.loadAgent(agent);
```

VirtualMachine也提供了一个遍历所有JVM实例的方法,例如

```java
String name = ...
List vms = VirtualMachine.list();
for (VirtualMachineDescriptor vmd: vms) {
    if (vmd.displayName().equals(name)) {
        VirtualMachine vm = VirtualMachine.attach(vmd.id());
        String agent = ...
        vm.loadAgent(agent);
        // ...
    }
}
```
在调用完loadAgent后需要调用deatch来取消附着，[文档](https://blogs.oracle.com/CoreJavaTechTips/entry/the_attach_api)有一些例子可以学习

JAVA SE 6 方式
-------------
通过上边提到的Attach技术，可以在运行时动态的运行代理程序，就可以达到不用重启应用程序来扩展功能，具体方法跟Java SE 5差不多，也是需要一个代理jar包，需要写一个代理类，只不过这个类需要有一个agentmain方法，方法的定义如下：

```java
 public static void agentmain (String agentArgs, Instrumentation inst);
 public static void agentmain (String agentArgs); 
```

其中，上边的优先级更高

在manifest中定义一个Agent-Class属性，通常在代理jar保重，Agent-Class和Premain-Class都有且都是一个类，这样在两种情况下都可以用，下面还是用一个简单的例子来说明

* 编写代理程序

```java
package com.zlq;

import java.lang.instrument.ClassFileTransformer;
import java.lang.instrument.IllegalClassFormatException;
import java.lang.instrument.Instrumentation;
import java.security.ProtectionDomain;

/**
 * Created by liqing.zhan on 2017/3/9.
 */
public class LoadClassInst {
    public static void premain(String agentArgs, Instrumentation inst) {
        System.out.println("LoadClassInst premain start");
        inst.addTransformer(new LoadClassTransformer());
        System.out.println("LoadClassInst premain end");
    }

    public static void agentmain(String agentArgs, Instrumentation inst) {
        Class[] classes = inst.getAllLoadedClasses();
        for (Class cls : classes) {
            System.out.println(cls.getName());
        }
    }
}

class LoadClassTransformer implements ClassFileTransformer {

    public byte[] transform(ClassLoader loader, String className, Class<?> classBeingRedefined,
                            ProtectionDomain protectionDomain, byte[] classfileBuffer)
            throws IllegalClassFormatException {
        System.out.println(String.format("load class %s", className));
        return classfileBuffer;
    }
}
```

* 编写应用程序

```java
package com.zlq;

import java.util.concurrent.TimeUnit;

/**
 * Created by liqing.zhan on 2017/3/9.
 */
public class Main {
    public static void main(String[] args) throws InterruptedException {
        System.out.println(Main.class.getSimpleName());
        while (true) {
            TimeUnit.SECONDS.sleep(4);
        }
    }
}
```

* 编写attach程序

```java
package com.zlq;

import com.sun.tools.attach.AgentInitializationException;
import com.sun.tools.attach.AgentLoadException;
import com.sun.tools.attach.AttachNotSupportedException;
import com.sun.tools.attach.VirtualMachine;

import java.io.IOException;

/**
 * Created by liqing.zhan on 2017/3/10.
 */
public class AttachJVM {
    public static void main(String[] args) throws IOException, AttachNotSupportedException,
            AgentLoadException, AgentInitializationException {
        String processId = args[0];
        String agentJarPath = args[1];
        VirtualMachine virtualMachine = VirtualMachine.attach(processId);
        virtualMachine.loadAgent(agentJarPath);
        virtualMachine.detach();
    }
}
```

先执行应用程序，拿到pid，再运行attach程序

应用
----
像btrace  greps 等工具都是应用Instrument技术，Google的dapper论文提到的分布式追踪技术也可以应用，动态的生成链路代码，打印日志，后台日志收集汇总，前台展示

参考文献
-------

* [Java SE 6 新特性: Instrumentation 新功能](https://www.ibm.com/developerworks/cn/java/j-lo-jse61/index.html)
* [Java 5 特性 Instrumentation 实践](https://www.ibm.com/developerworks/cn/java/j-lo-instrumentation/)
* [java.lang.instrument笔记](http://jiangbo.me/blog/2012/02/21/java-lang-instrument/)
* [JVM源码分析之javaagent原理完全解读](http://www.infoq.com/cn/articles/javaagent-illustrated)