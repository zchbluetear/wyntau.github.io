---
layout: post
title: "Spring基于注解的AOP方式"
date: 2017-05-21
description: "Spring基于注解的AOP方式"
tag: Spring
---

AspectJ允许使用注解定义切面，切入点和增强管理。
而Spring框架可以自动识别并根据这些注解生成AOP代理。<br>
Spring只是使用了和AspectJ一样的注解，但是并没有使用AspectJ的编译器或者织入器。
依然是在运行时动态生成AOP代理，并不依赖于AspectJ的编译器或者织入器。

**1.定义切面Bean**

为了启用Spring对@AspectJ切面的支持，并保证Spring中的目标Bean被一个或多个切面自动增强，需要在Spring配置文件配置如下片段：

```
<aop:aspectj-autoproxy/>
<bean class = "org.spring.framework.aop.aspectj.annotation.AnnotationAwareAspectJAutoProxyCreator"/>

<context:component-scan base-package="">
<context:include-filter type = "annotation" expression="org.aspectj.lang.annotation.Aspect"/>
</context:component-scan>
```

为了启用Spring对@Aspect的支持，还需要增加AspectJ库：aspectweaver.jar.aspectjrt.jar.
除此之外，Spring AOP还需要依赖一个aopalliance.jar.<br>
使用@Aspect标注一个Java类，Spring将不会把该Bean当做组件Bean来处理。Spring
不会对该Bean进行增强。

**2.@Before增强处理**

使用@Before修饰一个方法时候，该方法将作为Before增强处理。

@Before通常需要指定一个value值，该属性值可以指定一个切入点表达式（既可以是已有的切入点,也可以直接定义切入点表达式）,用于指定该增强处理将被作用于哪些切入点。

使用Before增强处理只能在目标方法执行之前织入增强。如果before增强处理没有特殊处理，目标方法总会自动执行。如果Before处理需要阻止目标方法的执行.可以通过抛出一个异常来实现。使用before增强处理时，目标方法还未获得执行的机会，所以Before增强处理无法获得目标方法的返回值。

**3.@AfterReturning增强处理**

@AfterReturning可以指定两个属性<br>
* pointcut/value:用于指定该切入点对应的表达式。<br>
* returning:该属性用于指定一个形参名。用于表示Advice方法中可以定义同名的形参。
该形参用于访问目标方法的返回值。同时该参数对应得类型还可以限制匹配返回对应类型的方法。
例如形参类型为Object。则可以返回Object类型。或者不返回任何类型。


**4.@AfterThrowing增强处理**

用于处理程序中未处理的异常。如果该异常被方法处理，则该增强处理异常失效。

它有两个属性：<br>
pointcut/value属性：用于定义切入点表达式.<br>
throwing：指定一个形参名。用于访问目标方法的抛出的异常。除此之外，还可以限制目标方法返回的异常类型.

**5.@After增强处理**

和AfterReturning有点类似。<br>
@AfterReturning只有在目标方法成功执行后才会被织入。<br>
@After不管目标方法如何结束(包括成功执行或者遇到异常)。它都会被织入。<br>
因此Aftert通常用于准备正常返回和异常返回。这种增强处理通常用于释放资源。


**6.@Around增强处理**

它可以理解为@Before增强处理和@AfterReturning增强处理的总和。<br>
与@AfterReturning不同的是，Around可以决定目标方法可以什么时候执行。怎么执行，甚至阻止目标方法的执行。通常需要在线程安全的环境下使用。<br>
有一个value属性，指定切入点。

当定义一个Around增强处理方法时，该方法的第一个形参必须是ProceedingJointPoint类型。
在增强处理方法内部，通过调用ProceedingJointPoint的proceed()显示的执行目标方法，否则目标方法不会被执行。
还可以在proceed方法加入Object[]类型的参数，该参数将会作为目标方法执行的参数。

**7.访问目标方法的参数**

如果proceed()传入的参数类型和个数与实际的目标方法的类型和个数不匹配，那么程序将会抛出异常。
为了或许目标方法的参数个数和类型，JointPoint包含了几个方法。

* Signature getSignature() 返回被增强方法的相关信息。
* Object getTarget() 返回被增强处理的目标对象。
* Object[] getArgs()返回执行目标方法的参数。
* Object getThis()返回为目标对象生成的代理对象。
 


**8.增强处理的优先级**

Spring AOP采用和AspectJ一样的优先顺序来织入增强处理。
在处理目标方法之前：@Around>@Before
在退出方法之后：@Around>@After>@AfterReturning|@AfterThrowing

如果要将不同切面的两个增强织入到同一连接点。需要通过Spring AOP指定不同切面增强的优先级。

* 将切面实现org.springframework.core.Orderd接口。重写它的int getOrder()方法。该方法的返回值越小，优先级越高。
* 直接使用@Order注解。该注解可以指定一个int 类型的 value。值越小，优先级越高。


同一个切面类相同类型的增强处理，Spring AOP将会以随机的顺序执行这两个增强处理。
程序没办法控制。可以将两个增强合二为一，或者分开到两个切面，在切面级别做处理。

**9.定义切入点**

切入点，实际就是为切入点表达式起的别名。
切入点包括两部分：

* 一个切入点表达式和一个包含任意名字和参数的方法签名。
* 切入点表达式通过@Pointcut来注解备注。

如果要在程序中使用定义大好的Pointcut。需要加指定的包名。格式如（包名.类名.方法()）


**10.切入点指示符**

定义切入点遇到的表达式关键字。<br>
Spring AOP不仅支持AspectJ的切入点指示符。额外支持bean切入点指示符。<br>
Spring目前只支持AspectJ的几种切入点指示符。不是所有。

支持的切入点指示符：

* execution:用于匹配执行方法的连接点。
* within:用于限定特定类型的连接点。
* this:用于限制AOP代理必须是指定类型的实例，匹配该对象的所有连接点。
* target:用于限制目标对象类型是指定类型，匹配该对象的所有实例。
* args:用于对连接点的参数类型进行限制。
* bean:用于限制只匹配指定Bean实例类型的连接点。
* 组合切入点表达式

"&&": 同时满足条件的切入点 <br>
"或": 只要满足一个条件的切入点（由于macdown的语法原因，暂用汉字代替）<br>
"!": 要求连接点不匹配指定的切入点表达式。<br>
























