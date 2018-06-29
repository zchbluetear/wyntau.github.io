---
layout: post
title: "Spring的AOP"
date: 2017-05-20
description: "Spring的AOP"
tag: Spring
---

介绍Spring AOP之前，先来了解下AspectJ.
AspectJ是java语言的一个AOP实现，主要包括两个部分，
一个部分定义了java编程中的语法规范，另一个部分是工具部分，包括编译器和调试工具。

从Spring2.0开始，Spring AOP已经引入了对AspectJ的支持，并允许直接通过AspectJ直接编程。

**一. AOP的作用：**

　　在程序员不修改源代码的情况下，为系统的业务组件的多个业务方法增加某种通用的功能。
本质是依然要去修改业务组件的多个业务方法的源代码。只是这个修改由AOP框架来完成。


**二. AOP的实现可以分为两类：**

* 1.静态的AOP实现：AOP框架在编译阶段对程序修改。即实现对目标类的增强，生成静态的AOP代理类。以AspectJ为代表。

* 2.动态的AOP实现：AOP框架在运行阶段动态生成AOP代理。（在内存中以JDK动态代理或cglib动态地生成AOP代理类）。以实现对目标类的增强，以Spring AOP为代表。

  一般说来，静态的AOP实现具有很好的性能，但是需要特殊的编译器。
动态AOP实现是纯java实现，无需特殊的编译器，但是通常性能略差。


**三. AOP的基本概念**

* 切面（Aspect）：切面用于组织多个Advice,Advice放在切面中定义。<br>
* 连接点（JointPoint）：程序执行过程中明确的点，如方法的调用还有异常的抛出。在Spring中，连接点总是方法的调用。<br>
* 增强处理（Advice）：AOP框架在特定的切入点执行的增强处理。<br>
* 切入点（Pointcut）：可以插入增强处理的连接点。该连接点也就变成了切入点。<br>
* 目标对象：被AOP框架进行增强处理的对象。<br>
* AOP代理：AOP框架常创建的对象。代理就是对目标的加强。<br>
* 织入：将增强处理增加到目标对象中.<br>


**四. AOP代理**

　　由AOP框架动态生成的一个对象，该对象可以作为目标对象使用。
AOP代理包含了目标对象的全部方法，但是AOP代理中的方法与目标对象的方法存在差异，AOP方法在特定的切入点加入了增强处理，并回调了目标对象的方法。



**五. Spring的AOP支持**

　　Spring的AOP代理由Spring AOP容器负责生成。
Spring实现AOP的方法和其他框架不一样，Spring并不是要提供最完美的AOP实现，它侧重于AOP实现和Spring AOP容器的整合。用于帮助解决企业级开发中的常见问题。

　　Spring AOP采用基于代理的AOP实现方案。而AspectJ则采用编译时增强的解决方案。

　　AOP代理的方法=增强处理+目标对象的方法







