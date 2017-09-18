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

Spring IoC容器可以通过多种途径来装载配置元数据，比如`本地文件系统`,比如本地文件系统，JAVA classpass等

在大多数的应用程序中，并不需要显示的代码去实例化一个或多个的Spring IoC容器实例。例如，在web应用程序中，我们只要在web.xml添加XML描述即可

Spring Ioc容器至少包含一个bean定义,但是大多数情况下会有多个bean定义,当使用基于XML的配置元数据时,将在顶层的beans中配置多个bean

bean定义与应用程序中实际使用的对象一一对应.通常情况下bean的定义包括:服务层对象,数据访问层对象,类似Struts Action的表示层对象,hibernate sessionFactory对象,JMS Queue对象等等.项目的复杂程度将决定bean定义的多少

```xml
<beans xmls="..."  
       xmls:xsi="..."
       xsi:schemalocation="...">
 <bean id="..." class="...">
  <!--collaborators and configration for this bean go here-->
  </bean>
  
  <!--more bean difinitions go here-->
</beans>

```

### 实例化容器

Spring IoC容器的实例化

```JAVA
Resource resource=new FileSystemResource("beans.xml");
BeanFactory factory=new XmlBeanFactory(resource);
```

…或...

```JAVA
ClassPathResource resource=new ClassPathResource("beans.xml");
BeanFactory factory=new XmlBeanFactory(resource);
```

…或...

```JAVA
ApplicationContext context=new ClassPathXmlApplicationContext(new String[] {"applicationContext.xml","applicationContext-part2.xml"});
//of course ,an ApplicationContext is just a BeanFactory
BeanFactory factory=(BeanFactory)context;
```

#### 组成基于XML配置元数据

将XML配置文件拆分成多个部分是非常有用的,为了加载多个XML文件生成一个ApplicationContext实例,可以将文件路径作为字符串数组传给ApplicationContext构造器.而bean factory将通过调用bean definition reader从多个文件中读取bean定义

Spring团队倾向于上诉做法,因为这样各个配置并不会察觉到它们与其他配置文件的组合.另外一种方法是使用一个或多个的<import/>元素来从另外一个或多个文件加载bean定义,所有的<import/>元素必须放在<bean/>元素之前完成bean定义的导入.

```XML
<beans >
  <import resource="service.xml"/>
  <import resource="resource/messageSource.xml"/>
  <import resource="/resource/themeSource.xml"/>
  <bean id="..." class="...">
  </bean>
</beans>
```

#### 多种bean

Spring IoC容器将管理一个或多个bean,这些bean将通过配置文件中的bean定义被创建(在XML格式中为<bean/>元素)

在容器内,这些bean定义由BeanDefinition对象来表示来表示,该定义将包含如下信息

- 全限定类名:这通常就是已定义bean的实际实现类.如果通过static factory方法来实例化bean,而不是使用常规的构造器,那么类名称实际上就是工厂类的类名
- bean行为的定义,即创建模式(prototype还是singleton),自动装配模式,依赖检查模式,初始化以及销毁方法.这些定义将决定bean在容器中的行为
- 用于创建bean实例的构造器参数及属性值.比如使用bean来定义连接池,可以通过属性或者构造参数指定连接数,以及连接池大小限制等.
- bean之间的关系,依赖

Class 实例化bean

name 命名bean

scope bean的作用域

constructor arguments 注入依赖

properties 注入依赖

Autowiring mode 自动装配协作者

dependency checking mode 依赖检查

lazy-initialization mode 延迟初始化bean

initialization method lifecycle接口

destruction method lifecycle接口

除了通过bean定义来描述要创建的指定bean的属性外,某些BeanFactory的实现也允许将那些非BeanFactory创建的,已有的用户对象注册到容器中,比如使用DefaultListableBeanFactory的registerSingleton(…).不过大多数应用还是采用元数据定义为主

### 命名bean

对bean采用统一的命名约定将会使配置更加简单易懂.而且在使用Spring AOP时,如果要发通知advice给一组名称相关的bean时,这种简单的命名方式将会使你受益匪浅

每个bean都有一个或多个id,在当前IoC容器中必须唯一.如果一个bean有多个id,那么其他id在本质上将被认为是别名.

当使用基于XML的配置元数据时,将通过id或name属性来指定bean标识符.id属性具有唯一性,而且是一个真正的XML ID 属性,因此其他XML元素在引用该ID时,可以利用XML解析器的验证功能.通常情况下最好为bean指定一个ID.尽管XML规范规定了XML ID命名的有效字符,但是bean标识符的定义不受该限制,因为除了使用指定的XML字符来做ID,还可以为bean指定别名,要实现这一点可以在name属性中使用逗号,冒号或者空格将多个ID分隔

值得注意的是,为一个bean提供一个name并不是必须的,如果没有指定,那么容器将为其生成一个唯一的name.

#### bean的别名

在对bean进行定义时,除了使用ID属性来指定名称外,为了提供多个名称,需要通过alias属性来加以指定.而所有的这些名称都指向同一个bean,在某些情况下提供别名非常有用,比如为了让应用的每一个组件能更容易的对公共组件进行引用

然而,在定义bean时就指定所有的别名并不是总是恰当的.有时我们期望能在当前位置为那些在别处引入的bean引入别名.在XML配置文件中,可用单独的<alias/>元素来完成别名的定义.

```XML
<alias name="fromName" alias="toName"/>
```

如果在容器中存在名为fromName的bean定义,在增加别名定义之后,也可以用toName来引用.

```XML
<alias name="componentA-dataSource" alias="conponentB-dataSource"/>
<alias name-"componeteA-dataSource" alias="myApp-dataSource"/>
```

这样每个组件及主程序就可以通过唯一的名字来引用同一个数据源而互不干扰.

#### 实例化bean

当采用XML描述配置元数据时,将通过<bean/>元素的Class属性来指定实例化对象的类型.Class属性通常是必须的不过也有两种例外的情况(使用实例工厂方法实例化 bean定义的继承)  

Class的用途:

- 用来指定被实例化的类
- 用来指定实际具有静态工厂方法的类

#### 用构造器来实例化

当采用构造器来创建bean实例时,Spring对Class并没有特殊的要求,通常使用的Class都适用.不过根据IoC类型,Class可能需要一个默认的空构造器

```XML
<bean ID="exampleBean" Class="examples.ExampleBean" />
```

#### 使用工厂方法实例化

- 通过静态工厂方法来实例化

此定义并未指定返回对象的类型,仅指定该类包含的工厂方法,在此定义中,createInstance()必须是一个static方法

```XML
<bean id="exampleBean" class="examples.ExampleBean2" factory-method="createInstance"/>

```

- 使用实例工厂方法来实例化

```XML
<bean id="myFactoryBean" class="...">
</bean>

<bean id="exampleBean" factory-bean="myFactoryBean" factory-method="createInstance">
</bean>
```

虽然设置bean属性的机制在这里仍然被提及,但是隐式的做法是由工厂bean自己来管理以及通过依赖注入(DI)来进行配置.

#### 使用容器

从本质上讲,BeanFactory仅仅只是一个维护bean定义以及相互依赖关系的高级工厂接口.通过BeanFactory我们可以访问bean定义.

```JAVA
InputStream is=new FileInputSteam("beans.xml");
BeanFactory factory=new XmlBeanFactory(is);
```

BeanFactory提供了六种方法供客户代码调用:

- Boolean containsBean(String) :
- Object getBean(String):  根据bean的配置情况,如果为singleton模式将返回一个共享的实例,否则将返回一个新建的实例.如果没有找到指定的bean,该方法将会抛出NoSuchBeanDefinitionException,在对bean进行实例化和预处理时也可能抛出异常
- Class getBean(String,Class); 返回给定名称注册的bean的实例,并转换为给定的Class类型的实例,如果转换失败,将抛出BeanNotOfRequiredTypeException
- Class getType(String name);
- Boolean isSingleton(String);
- String[] getAlias(String );

### 依赖

#### 注入依赖

依赖注入背后的基本原理是对象之间的依赖关系(即一起工作的其他对象)只会通过以下几种方式来实现:构造器的参数,工厂方法的参数,或给由构造函数或者工厂方法创建的对象设置属性.

因此,容器的工作就是创建bean时注入那些依赖关系.相对于由bean自己来控制实例化,直接在构造器中指定依赖关系或则类似服务定位器(service locator)模式这3种自主控制依赖关系注入的方法来说,控制从根本上发生了倒转,inversion of control

应用DI原则后,代码将更加清晰,而且当bean自己不再担心对象之间的依赖关系之后,就可实现更高层次的松耦合.

##### setter注入

#####构造器注入

如何在构造器注入和setter注入之间进行选择

由于大量的构造器参数可能使程序变得笨拙,特别是当某些属性是可选的时候.因此通常情况下,Spring开发团队提倡使用Setter注入.而且Setter DI在以后的某个时候还可以将实例重新配置

尽管如此,构造器注入依旧有自己的优势.一次性将所有的依赖注入的做法意味着,在未完全初始化的状态下,此对象不会返回给客户代码(或被调用),此外对象也不可能在此被重新配置.

对于那些没有源码的第三方类,或者没有提供setter方法的遗留代码,则构造器注入是唯一的选择

BeanFactory对于它所管理的bean提供两种注入依赖方式.需要注入的依赖将保存在BeanDefinition中,它能根据指定的PropertyEditor实现将属性从一种格式转化为另一种格式.大部分的Spring用户并不需要直接以编程的方式处理这些类,而是采用XML的方式来进行定义,在内部这些定义将被转换成相应的实例类,并终将得到一个Spring IoC容器实例

处理bean依赖关系通常按以下步奏进行:

1. 根据定义bean的配置文件创建并初始化BeanFactory实例,大部分的Spring用户使用支持XML格式配置文件的BeanFactory或ApplicationContext实现
2. 每个Bean的依赖将以属性,构造参数,或静态工厂方法参数的形式出现.当这些bean被实际创建时,这些依赖也将会提供给该bean
3. 每个属性或构造器参数既可以是一个实际的值,也可以是对该容器中另一个bean的引用.
4. 每个指定的属性或构造器参数值必须能够被转换成属性或构造参数所需的类型.默认情况下,Spring会能够以String类型提供值转换成各种内置类型,比如int,long,String,boolean等

Spring会在容器被创建时验证容器中每个bean的配置,包括验证那些bean所引用的属性是否指向一个有效的bean.然而在bean被实际创建之前,bean的属性并不会被设置.对于那些singleton类型和被设置为提前实例化的bean而言,bean实例将与容器同时被创建.而另外一些bean则会在需要的时候被创建,伴随着bean被实际创建,作为该bean的依赖bean,以及依赖bean的依赖bean也将被创建.

循环依赖  抛出BeanCurrentlyInCreantionException

通常情况下,你可以信赖Spring,它会在容器加载时发现配置错误(比如对无效bean的引用以及循环依赖).Spring会在bean创建的时候采取设置属性和依赖关系(只在需要时创建所依赖的其他对象).Spring容器被正确加载之后,当获取一个bean实例时,如果在创建bean或者设置依赖时出现问题,那么将抛出一个异常.因缺少或设置了一个无效属性而导致抛出一个异常的情况确实存在.因为一些配置问题而导致潜在的可见性被延迟,所以在默认情况下,ApplicationContext实现中的bean采用提前实例化的singleton模式.在实际需要之前创建这些bean将带来时间与内存的开销.不过用户也可以根据需要采用延迟实例化来代替默认的singleton模式.

######Setter注入

```xml
<bean id="exampleBean" class="examples.ExampleBean">
  <!--setter injection useing the nested <ref/> element-->
  <property name="beanOne" >
    <ref bean="anotherExampleBean"/>
  </property>
  <!--setter injection using the neater attribute-->
  <property name="beanTow" ref="yetAnotherBean" />
  
</bean>

<bean id="anotherBean" class="examples.AnnotherBean"/>

<bean id="yetAnotherBean" class="examples.YetAnotherBean"/>

```

```java
public class ExampleBean{
    private AnotherBean beanOne;
  	private YetAnotherBean beanTow;
  
  public void setBeanOne(AnotherBean beanOne){
      this.beanOne=beanOne;
  }
  
  public void setBeanTow(YetAnotherBean beanTow){
      this.beanTow=beanTow;
  }
}
```

###### 构造器注入

```xml
<bean id="exampleBean" class="examples.ExampleBean">
  <!--constructor injection using the nested <ref/> element-->
  <constructor-arg>
    <ref bean="anotherExample"/>
  </constructor-arg>
  
  <!--constructor injection using the neater 'ref' attribute-->
  <constructor-arg ref="yetAnotherExample"/>
  
</bean>

<bean id="anotherExample" class="examples.AnotherExample"/>

<bean id="yetAnotherExample" class="examples.YetAnotherExample"/>
```

```java
public class ExampleBean{
    private AnotherBean beanOne;
 	private YetAnotherBean beanTow;
  
  public ExampleBean(AnotherBean beanOne,YetAnotherBean beanTow){
      this.beanOne=beanOne;
      this.beanTow=beanTow;
  }
}
```

###### 静态工厂方法注入

```xml
<bean id="exampleBean" class="examples.ExampleBean" factory-method="createInstance">
  <constructor-arg ref="anotherExample"/>
  <constructor-arg ref="yetAnotherExample"/>
</bean>

<bean id="anthorExample" class="examples.AnotherBean"/>
<bean id="yetAnotherExample" class="examples.YetAnotherBean"/>
```

```java
public class Example{
    private Example(...){
        ...
    }
  
  public static ExampleBean createInstance(AnotherBean anotherBean,YetAnotherBean yetAnotherbean){
      ExampleBean eb=new ExampleBean(...);
    return eb;
  }
}
```

传递给静态工厂方法的参数由constructor-arg元素提供,这与构造器注入时完全一样.而且,重要的是,工厂方法所返回的实例的类型并不一定要与包含static工厂方法的类类型一致.

#### 构造器参数的解析

构造器参数将根据类型来进行匹配,如果bean定义中的构造器参数类型明确,那么bean定义中的参数顺序就是对应构造器参数的顺序

```java
public class Foo{
    public Foo(Bar bar,Baz baz){
        ......
    }
}
```

这里的参数类型明确,无需显式的指定构造器参数索引以及类型

```xml
<beans>
  <bean name="foo" class="x.y.Foo">
    <constructor-arg>
      <bean class="x.y.Bar"/>
    </constructor-arg>
    
    <constructor-arg>
      <bean class="x.y.Baz"/>
    </constructor-arg>
  </bean>
</beans>
```

当引用的bean类型已知,则匹配没有问题.但是当使用简单类型时,Spring将无法确定该值得类型,因而仅仅根据类型是无法进行匹配的.例如

```java
public class ExampleBean{
    private int years;
  	private String ultimateAnswer;
  	public ExampleBean(int years,String ultimateAnswer){
        this.year=years;
        this.ultimateAnswer;
    }
}
```

#### 构造器参数类型匹配

```xml
<bean id="exampleBean" class="examples.ExampleBean">
<constructor-arg type="int" value="75000000"/>
<constructor-arg type="java.lang.String" value="42" >
</bean>
```

#### 构造器参数的索引

通过使用index属性可以显式的指定构造器参数出现顺序

```Xml
<bean id="exampleBean" class="examples.ExampleBean">
  	<constructor-arg index="0" value="7500000"/>
  	<constructor-arg index="1" value="2"/>
</bean>
```

使用index属性除了可以解决多个简单类型构造参数造成的棱模两可的问题之外,还可以用来解决两个构造参数类型相同造成的麻烦.index属性从0开始



### bean属性及构造器参数详解

正如前面提到的,bean的属性及构造器参数既可以引用容容器中的其他bean,也可以时内联(inline,在Spring的XML配置中使用<property/>和<constructor-arg/>元素定义)bean

###### 直接量,基本类型

<value/>元素通过字符串来指定属性或构造器参数的值.正如前面所提到的,JavaBean PropertyEditor将用于把字符串从java.lang.String类型转化为实际的属性或参数类型

```xml
<bean id="myDataSource" destory-method="close" calss="org.apache.commons.dbcp.BasicDataSource">
	<property name="driverClassName">
      	<value>com.mysql.jdbc.Driver</value>
  	</property>
  
  	<property name="url">
      <value>jdbc:mysql//localhost:3306/mydb</value>
    </property>
</bean>
```

##### idref元素

idref元素用来将容器内其它bean的id传给<constructor-arg>或<property/>元素,同时提供错误验证功能

```xml
<bean id="theTargetBean" class="..."/>
<bean id="client" class="...">
  <property name="targetName">
    <idref bean="theTargetBean"/>
  </property>
</bean>
```

上述bean定义片段完全等同于以下片段

```xml
<bean id="theTargetBean" class="...">
	<property name="targetBean">
      <value>theTargetBean</value>
  	</property>
</bean>
```

第一种形式比第二种更可取的主要原因是,使用idref标记允许容器在部署时验证所被引用的bean是否存在.而第二种方式中,传给client bean的targetName属性值并没有被验证.任何的输入错误仅在client bean实际实例化时才会被发现(可能伴随致命错误).如果client bean是propertytype类型的bean,则此输入错误(及由此导致的异常)可能在容器部署很久以后发现.

此外,如果被引用的bean在同一XML文件内,且bean名字就是bean id,那么可以使用local属性,此属性允许XML解析器在解析XML文件时来对引用的bean进行验证.

```xml
<property name="targetBean">
  <!--a bean whith an id of 'theTargetBean' must exist,else an xml exception will be thrown-->
  <idref local="theTargetBean"/>
</property>
```

如果使用<idref/>元素指定拦截器名字,可以避免因一时疏忽导致的拦截器ID拼写错误.

#### 引用其他的bean(协作者)

在<constructor-arg/>或<property/>元素的内部还可以使用ref元素.该元素用来将bean中指定属性的值设置为对容器中的另外一个bean的引用.如前所述,该引用bean将被作为依赖注入,而且会在注入之前被初始化,如果是singleton bean 则已被容器初始化.尽管都是对另外一个对象的引用,但是通过id/name指向另一个对象却有三种不同的形式,不同的形式将决定如何处理作用域以及验证.

第一种形式是最常见的形式,通过ref元素指定bean属性,通过标签可以引用同一容器或父容器内的任何bean.XML'bean'元素既可以指定bean的id值也可以是其name值.

```xml
<ref bean="someBean"/>
```

第二种形式时是哟个local属性指定目标bean,可以利用XML解析器来验证所引用的bean是否在同一文件中,local属性值必须是目标bean的id属性值,如果在同一配置文件中没有找到引用的bean,XML解析器将抛出一个异常.如果目标bean是在同一文件内,使用local方式就是最好的选择.

第三种方式是通过使用ref标签的parent属性来引用当前容器的父容器中的bean.parent属性值既可以是目标bean的id值,也可以是name属性值.而且目标bean必须在当前容器的父容器中.使用parent属性的主要用途是为了用某个与父容器中的bean同名的代理来包装父容器中的一个bean

#### 内部bean

所谓的内部bean(inner bean)是指在一个bean的<property/>或<constructor-arg/>元素中使用<bean/>元素定义的bean.内部bean定义并不需要有id或name属性,即使指定id或name属性值也会被容器忽略

```xml
<bean id="outer" class="...">
<!--instead of using a reference to a target bean ,simply define the target inline-->
  <property name="target">
  	<bean class="com.mycompany.Person">
    	<property name="name" value="Finoa Apple"/>
      	<property name="age" value="25"/>
    </bean>
  </property>
</bean>
```

内部bean中的singleton标记及id或name属性将被忽略.内部bean总是匿名的且它们总是prototype模式的.同时将内部bean注入到包含该内部bean之外的bean是不可能的.

#### 集合

通过<list/>,<set/>,<map/>及<props/>元素可以定义和设置与java collection类型对应的list ,set,map,及properties的值.

#### 集合合并

从2.0开始.Spring IoC容器将支持集合的合并.这样我们可以定义parent-style和child-style的<list/>,<map/>,<set/>或<props>元素,子集合的值

