---
layout: post
title: "SpringBoot的application.properties"
date: 2018-01-07
description: "SpringBoot的application.properties"
tag: SpringBoot
---

###前言：

Spring Boot使用“习惯优于配置”（项目中存在大量的配置，此外还内置了一个习惯性的配置，让你无需手动进行配置）的理念让你的项目快速运行起来。所以，我们要想把Spring Boot玩的溜，就要懂得如何开启各个功能模块的默认配置，这就需要了解Spring Boot的配置文件application.properties。

###正文

Spring Boot使用了一个全局的配置文件application.properties，放在src/main/resources目录下或者类路径的/config下。Sping Boot的全局配置文件的作用是对一些默认配置的配置值进行修改。

接下来，让我们一起来解开配置文件的面纱。

注:如果你工程没有这个application.properties，那就在src/main/java/resources目录下新建一个。

####一.自定义属性


application.properties提供自定义属性的支持，这样我们就可以把一些常量配置在这里：
com.dudu.name="嘟嘟MD"
com.dudu.want="祝大家鸡年大吉吧"

然后直接在要使用的地方通过注解@Value(value="${config.name}")就可以绑定到你想要的属性上面

```
@Value("${com.dudu.want}")
private  String want;
```


我们启动工程输入http://localhost:8080 就可以看到打印了"嘟嘟MD祝大家鸡年大吉吧"。

有时候属性太多了，一个个绑定到属性字段上太累，官方提倡绑定一个对象的bean，这里我们建一个ConfigBean.java类，顶部需要使用注解@ConfigurationProperties(prefix = "com.dudu")来指明使用哪个。

```
@ConfigurationProperties(prefix = "com.dudu")

public class ConfigBean {
    
}
```

这里配置完还需要在spring Boot入口类加上@EnableConfigurationProperties并指明要加载哪个bean，如果不写ConfigBean.class，在bean类那边添加

```
@SpringBootApplication

@EnableConfigurationProperties({ConfigBean.class})

public class Chapter2Application {
    public static void main(String[] args) {
        SpringApplication.run(Chapter2Application.class, args);
    }
}
```


最后在Controller中引入ConfigBean使用即可，如下：

```
@Autowired
ConfigBean configBean;

@RequestMapping("/")
public String hexo(){
	return configBean.getName()+configBean.getWant();
}
```


####二.参数间引用

在application.properties中的各个参数之间也可以直接引用来使用，就像下面的设置：

com.dudu.name="嘟嘟MD"
com.dudu.want="祝大家鸡年大吉吧"
com.dudu.yearhope=${com.dudu.name}在此${com.dudu.want}

这样我们就可以只是用yearhope这个属性就好


####三.使用自定义配置文件

有时候我们不希望把所有配置都放在application.properties里面，这时候我们可以另外定义一个，这里我取名为test.properties,路径跟也放在src/main/resources下面。

```
com.md.name="哟西~"
com.md.want="祝大家鸡年,大吉吧"

@Configuration

@ConfigurationProperties(prefix = "com.md") 

@PropertySource("classpath:test.properties")

public class ConfigTestBean {
    private String name;
    private String want;
    // 省略getter和setter
}
```

####四.配置文件的优先级

* 外置，在相对于应用程序运行目录的/congfig子目录里.<br>
* 外置，在应用程序运行的目录里.<br>
* 内置，在config包内.<br>
* 内置，在Classpath根目录.<br>








