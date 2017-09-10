# Spring 核心部分

## 控制反转容器 inversion of control 

Org.springframework.beans 以及 org.springframework.context 包是Spring Ioc容器的基础。Bean Factory提供的高级配置机制，使得管理任何性质的对象成为可能。ApplicationContext是BeanFactory的扩展，功能得到进一步的增强，比如更容易与Spring AOP 集成，消息资源处理，事件传递以及各种不同应用层的Context实现

BeanFactory提供了配置框架及基本功能，而ApplicationContext则增加了更多支持企业核心内容的功能。ApplicationContext完全由Bean Factory扩展而来，因而BeanFactory所具备的能力和行为也适用于ApplicationContext

### 容器和bean的基本原理

在Spring中，那些组成应用的主体（backbone）以及Spring IOC容器所管理的对象被称之为bean,简单的讲，bean就是由Spring容器初始化，装配以及被管理的对象，除此之外，bean就没有什么特别之处了。而bean定义以及bean相互间的依赖关系将通过配置元数据来描述

#### 容器

org.sprigframework.beans.factory.BeanFactory是Spring IOC 容器的实际代表者，IoC容器负责容纳bean，并对bean进行管理

在spring中，BeanFactory是IoC容器的核心接口。它的职责包括：实例化，定位，配置应用程序中的对象及建立这些对象之间的依赖。

Spring为我们提供了许多易用的BeanFactory实现，XMLBeanFactory就是最常用的一个。该实现将以XML方式描述组成应用的对象以及对象之间的依赖关系。XMLBeanFactory类将持有此XML配置元数据，并用它来构建一个完全可以配置的系统或应用。

#####配置元数据

Spring Ioc容器将读取配置元数据；并通过它对应用中各个对象进行实例化，配置以及组装。通常情况下我们使用简单直观的XML作为配置元数据的描述格式。在XML配置元数据中我们可以对那些我们希望通过Spring IoC容器管理的bean 进行定义

Spring支持三种配置元数据格式：`XML格式`，`Java属性文件`格式或使用`Spring公共API`编程实现。

Spring IoC容器可以通过多种途径来装载配置元数据，比如`本地文件系统`