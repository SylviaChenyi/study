# Spring

## 第一章 简介

java应用是一种典型的依赖型应用，它是由一些互相适当的地协作的对象构成的。因此，这些对象间存在依赖关系。

### 1.1 概览

core封装包是框架的基础部分，提供IoC和依赖注入特性。这里的基础概念是BeanFactory，它提供对Factory模式的经典实现来消除对程序性单例模式的需要，并真正的允许你从程序逻辑中分离出依赖关系和配置。

构建于Core封装包基础上的Context封装包，提供了一种框架式的对象访问方法，有些象JNDI注册器。context封装包的特性得自于Beans封装包，并添加了对国际化（I18N）的支持，事件传播，资源装载的方式和context的透明创建，比如说ring 过servlet容器

DAO提供了JDBC的抽象层，它可以消除冗长的JDBC编码和解析数据库厂商特有的错误代码。并且，JDBC封装包海提供了一种比编程性更好的声明性事物管理方法，不仅仅实现了特定接口，而且对所有的POJO都适用

ORM封装包提供了常用的“对象／关系”映射APIs.其中包括JPA,JDO,hibernate和ibatis。利用ORM封装包，可以混合使用所有Spring提供的特性进行“对象／关系”映射，如前边提到的简单的声明性事务管理。

spring的AOP封装包提供了符合AOP Alliance规范的面相切面的编程aspect-oriented-programming 实现，让你可以定义，例如方法拦截器 method-interceptors和切点pointcuts，从逻辑上讲，从而减弱代码的功能耦合，清晰的被分离开。而且，利用source-level的元数据功能，还可以将各种行为信息合并到你的代码中，这有点像net的attribute的概念

Spring中的web包提供了基础的针对web开发的集成特性，例如多方文件上传，利用seevlet listeners进行Ioc容器初始化和针对web的application context 。当与webwork 或struts一起使用spring时，这个包使Spring可与其它框架结合

spring中的MVC封装包提供了web应用的model-view-controller mvc实现。Spring的mvc框架并不使仅仅提供一种传统的实现，它提供了一种清晰的分离模型，在领域模型代码和web form之间。并且，还可以借助Spring框架的其它特性

## 第二章spring2.0 的新特性

### 2.2控制反转（Ioc)容器

spring 2.0不仅仅提供了一些依赖于Spring部署环境的额外作用域，而且提供了hooks使spring用户可以创造自己的作用域

####可扩展的XML编写

可扩展性：可以开发自定义标签，供其他开发人员把这些标签嵌入到自己的Spring配置文件里。你可以在组件的特定配置中定义你自己的DSL domain specific language 

### 2.3面向切面编程

spring 2.0在AOP上不再那么繁琐；spring2.0集成了aspectJ切入点（point cut）语言和@AspectJ切面aspect声明类型。

### 2.4中间层

### 2.5 web层

### 2.6其他特性

动态语言支持  jmx(支持通知 notification 还可以声明式的控制Mbean在一个MBeanServer上的注册行为) 任务规划 

辅导费. 地

