## <02-01-02> 容器概览（Container Overview）

`org.springframework.context.ApplicationContext`接口是Spring框架中表示IoC容器的核心接口。它负责实例化、配置和组装Bean对象。通过ApplicationContext，我们可以通过读取配置元数据（如XML、Java注解或Java代码）来告诉容器应该实例化哪些对象、如何配置这些对象以及如何组装它们。

> 配置元数据(configuration metadata)描述了应用程序中的对象及其之间的关系，包括依赖关系、注入方式等。通过配置元数据，我们可以清晰地表达应用程序的组成部分和对象之间的丰富相互依赖关系。

Spring提供了多个`ApplicationContext`接口的实现。在独立应用程序中，通常可以创建`ClassPathXmlApplicationContext`或`FileSystemXmlApplicationContext`的实例。尽管XML一直是定义配置元数据的传统格式，但您可以通过提供少量的XML配置来声明性地启用对Java注解或代码作为元数据格式的支持。这样，您就可以指示容器使用Java注解或代码作为配置元数据的格式。

> ClassPathXmlApplicationContext和FileSystemXmlApplicationContext是两种常用的实现方式。ClassPathXmlApplicationContext从类路径中加载XML配置文件，而FileSystemXmlApplicationContext从文件系统中加载XML配置文件。通过这些实现，您可以轻松地创建并配置Spring IoC容器，以便启动和管理应用程序的bean对象。
>
> 通过配置少量的XML，您还可以指示容器使用Java注解或代码来定义和配置Bean。这种方式可以使您在开发过程中更加灵活地使用注解或代码来定义bean，而无需依赖传统的XML配置文件。

在大多数应用场景中，不需要显式的用户代码来实例化一个或多个Spring IoC容器。例如，在Web应用程序场景中，通常只需在应用程序的`web.xml`文件中添加简单的约八行的样板web描述符XML即可（参见[方便的Web应用程序的ApplicationContext实例化](https://docs.spring.io/spring-framework/reference/core/beans/context-introduction.html#context-create)）。如果您使用[Spring Tools for Eclipse](https://spring.io/tools)（基于Eclipse的开发环境），您可以通过几次鼠标点击或按键来轻松创建这个样板配置。

> 在Web应用程序中，可以通过在web.xml文件中声明Spring的DispatcherServlet和上下文配置文件的位置来自动创建和初始化Spring IoC容器。这样，容器就可以在应用程序启动时自动被实例化和配置，而不需要手动编写大量的代码。Spring Tools for Eclipse提供了简化这个过程的工具和操作界面，使您可以更轻松地创建和配置Spring IoC容器。

下面的图表展示了Spring工作的高层视图。您的应用程序类与配置元数据结合在一起，这样，在创建和初始化ApplicationContext之后，您就拥有一个完全配置和可执行的系统或应用程序。

![container magic](pic/container-magic.png)

### 配置元数据（Configuration Metadata）

如前面的图表所示，Spring IoC容器需要使用一种配置元数据。这个配置元数据代表了您作为应用程序开发者告诉Spring容器如何实例化、配置和组装应用程序中的对象。

传统上，配置元数据通常以简单直观的XML格式提供，这也是本章大部分内容用来传达Spring IoC容器的关键概念和特性的方式。

> XML格式的配置文件具有良好的可读性和可理解性，它允许您明确地定义对象的结构、依赖关系和属性值。通过使用XML配置文件，您可以将应用程序的组件和配置信息集中管理，使其更易于维护和修改。

**注意：**

* XML格式的配置元数据并不是唯一允许的配置方式。Spring IoC容器本身与配置元数据的实际编写格式完全解耦。现如今，许多开发者选择使用基于Java的配置来构建他们的Spring应用程序。

<details><summary>更多</summary>
<p>Java-based configuration，也称为Java Config，允许您使用纯Java代码来定义和配置应用程序的对象和依赖关系，而无需使用XML或注解。通过使用Java Config，您可以利用编程语言的特性来实现更灵活、可重用和类型安全的配置。</p>
<p>在Java Config中，您可以使用特定的注解（如@Configuration、@Bean等）来标识配置类和对象定义。通过编写方法和注解配置，您可以明确地定义bean的创建和组装逻辑，以及它们之间的依赖关系。</p>
<p>相比于XML配置，Java Config提供了更直观、类型安全和可维护的配置方式。它允许您使用IDE的自动完成和重构工具来提高开发效率，并使配置更容易被检查和维护。</p>
<p>总之，虽然XML是传统的配置元数据格式，但如今许多开发者更倾向于使用基于Java的配置。Java Config提供了更灵活和便捷的配置方式，使得开发者可以直接使用Java代码来定义和配置对象，从而提高了可读性、可维护性和开发效率。</p></details>


关于在Spring容器中使用其他形式的元数据，请参考以下内容：

* 基于注解的配置[(Annotation-based configuration)](https://docs.spring.io/spring-framework/reference/core/beans/annotation-config.html)：使用基于注解的配置元数据来定义bean。

* 基于Java的配置[(Java-based configuration)](https://docs.spring.io/spring-framework/reference/core/beans/java.html))：使用Java文件而不是XML文件，在应用程序类之外定义bean。要使用这些特性，请参考[@Configuration](https://docs.spring.io/spring-framework/docs/6.0.9/javadoc-api/org/springframework/context/annotation/Configuration.html)、[@Bean](https://docs.spring.io/spring-framework/docs/6.0.9/javadoc-api/org/springframework/context/annotation/Bean.html)、[@Import](https://docs.spring.io/spring-framework/docs/6.0.9/javadoc-api/org/springframework/context/annotation/Import.html)和[@DependsOn](https://docs.spring.io/spring-framework/docs/6.0.9/javadoc-api/org/springframework/context/annotation/DependsOn.html)等注解。

Spring的配置至少包含一个或通常多个需要容器管理的Bean定义。基于XML的配置元数据通过在顶级`<beans/>`元素内部将这些bean配置为`<bean/>`元素来完成。而基于Java的配置通常使用在`@Configuration`类中`@Bean`注解的方法。

> XML配置和Java配置是两种常见的配置方式，它们在表达和配置bean时具有不同的语法和风格。XML配置通常更适合于简单的配置场景和可读性要求较高的情况，而Java配置提供了更灵活、类型安全和可维护的配置方式。

这些Bean定义对应于构成您应用程序的实际对象。通常，您会定义服务层对象、持久层对象（如存储库或数据访问对象（Data Access Objects，DAOs））、表示层对象（如Web控制器<u>Web controllers</u>）、基础设施对象（如JPA `EntityManagerFactory`、JMS队列）等。通常，不会在容器中配置细粒度（fine-grained）的领域（domain）对象，因为创建和加载领域对象通常是存储库和业务逻辑的责任。

> 在Spring中，您可以将一些核心的应用程序组件配置为bean，这些组件负责处理特定的业务功能或提供特定的服务。这样的组件通常是应用程序的核心部分，它们封装了业务逻辑和数据访问，并与其他组件进行交互。
>
> 而对于细粒度的领域对象，通常是由存储库和业务逻辑来创建和加载。这些对象表示应用程序的业务实体，它们包含了特定的业务逻辑和数据。存储库负责与数据库交互，将领域对象持久化或从数据库中检索出来，并将其传递给业务逻辑层进行处理。

下面的示例展示了基于XML的配置元数据的基本结构：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
		https://www.springframework.org/schema/beans/spring-beans.xsd">

	<bean id="..." class="..."> ① ②
		<!-- collaborators and configuration for this bean go here -->
	</bean>

	<bean id="..." class="...">
		<!-- 协作对象和该Bean的配置放在此处 -->
	</bean>

	<!-- more bean definitions go here -->
    <!-- 在此处定义更多的Bean -->

</beans>
```

①`id`属性是一个字符串，用于标识独立的bean定义。 ②`class`属性定义了bean的类型，并使用全限定类名。

在基于XML的配置中，当定义一个bean时，你可以使用`id`属性给它一个唯一的标识符。然后可以在配置文件的其他部分中使用这个标识符来引用该bean。这样可以建立bean之间的依赖关系，并指定它们之间的协作方式。

> 例如，如果你定义了两个bean，分别使用了`userService`和`userRepository`作为它们的ID，你可以使用`ref`属性将`userRepository`作为`userService`的协作对象进行引用。以下是一个示例：
>
> ```xml
> <bean id="userService" class="com.example.UserService">
>     <property name="userRepository" ref="userRepository"/>
> </bean>
> 
> <bean id="userRepository" class="com.example.UserRepository"/>
> ```
>
> 在这个示例中，`userService` bean引用了`userRepository` bean作为它的协作对象。通过设置元素的`name`属性为`userRepository`，并使用`ref`属性将其引用为`userRepository` bean的ID。
>
> 通过这样的配置，`userService` bean可以与`userRepository` bean进行协作，实现业务逻辑的处理。

### 实例化容器（Instantiating a Container）

`ApplicationContext`构造函数可以以字符串的形式接收单个或多个XML文件路径，容器可以从各种外部资源加载配置元数据，例如本地文件系统、Java类路径（`CLASSPATH`）等等。

```java
ApplicationContext context = new ClassPathXmlApplicationContext("services.xml", "daos.xml");
```

**注意：**

* 在了解了Spring的IoC容器之后，您可能想要了解更多关于Spring的资源抽象*`Resource` abstraction*（如[Resources](https://docs.spring.io/spring-framework/reference/web/webflux-webclient/client-builder.html#webflux-client-builder-reactor-resources)中所述）的信息，它提供了一种方便的机制，用于从URI语法中定义的位置读取*InputStream*输入流。具体而言，资源路径用于构建应用程序上下文，如[Application Contexts and Resource Paths](https://docs.spring.io/spring-framework/reference/core/resources.html#resources-app-ctx)中所描述的那样。

> 资源抽象允许您通过URI指定的位置加载各种类型的资源，包括文件系统上的文件、类路径上的资源、网络上的资源等等。通过使用资源路径，您可以在应用程序上下文的构造函数或配置文件中指定要加载的资源，从而灵活地管理和配置应用程序的依赖关系和配置元数据。
>
> 了解Spring的资源抽象有助于您更好地利用Spring框架的功能和特性，从而实现更灵活、可扩展的应用程序开发。

以下示例显示了服务层对象（services.xml）的配置文件：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
		https://www.springframework.org/schema/beans/spring-beans.xsd">

	<!-- services -->

	<bean id="petStore" class="org.springframework.samples.jpetstore.services.PetStoreServiceImpl">
		<property name="accountDao" ref="accountDao"/>
		<property name="itemDao" ref="itemDao"/>
		<!-- additional collaborators and configuration for this bean go here -->
	</bean>

	<!-- more bean definitions for services go here -->

</beans>
```

以下示例显示了数据访问对象（daos.xml）的配置文件：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
		https://www.springframework.org/schema/beans/spring-beans.xsd">

	<bean id="accountDao"
		class="org.springframework.samples.jpetstore.dao.jpa.JpaAccountDao">
		<!-- additional collaborators and configuration for this bean go here -->
	</bean>

	<bean id="itemDao" class="org.springframework.samples.jpetstore.dao.jpa.JpaItemDao">
		<!-- additional collaborators and configuration for this bean go here -->
	</bean>

	<!-- more bean definitions for data access objects go here -->

</beans>
```

在上述示例中，通过XML配置文件定义了服务层和数据访问对象之间的依赖关系。其中，服务层由`PetStoreServiceImpl`类实现，而数据访问对象则是`JpaAccountDao`和`JpaItemDao`，它们是基于JPA对象关系映射标准的实现。

通过在XML配置文件中使用`property`元素（标签），可以将属性名`name`和对bean的引用`ref`关联起来，从而表达对象之间的依赖关系。这种链接机制通过将`id`元素（标签）和`ref`元素（标签）进行匹配，实现了协作对象之间的依赖注入。

通过这种方式，我们可以很方便地配置对象之间的依赖关系，实现松耦合的组件协作。详细的依赖配置可以参考Spring的[Dependencies](https://docs.spring.io/spring-framework/reference/core/beans/dependencies.html)文档。

### 构建基于XML的配置元数据（Composing XML-based Configuration Metadata）

创建跨多个XML文件的bean定义是非常实用的。通常，每个单独的XML配置文件代表了架构中的一个逻辑层或模块。

您可以使用应用程序上下文的构造函数从所有这些XML片段加载bean定义，就像在[前面的部分](#实例化容器（Instantiating a Container）)中展示的那样。该构造函数接受多个资源位置作为参数。另外，您还可以使用一个或多个`<import/>`元素来从其他文件中加载bean定义。以下示例展示了如何进行操作：

```xml
<beans>
	<import resource="services.xml"/>
	<import resource="resources/messageSource.xml"/>
	<import resource="/resources/themeSource.xml"/>

	<bean id="bean1" class="..."/>
	<bean id="bean2" class="..."/>
</beans>
```

在上述示例中，外部的bean定义从三个文件中进行加载：`services.xml`、`messageSource.xml`和`themeSource.xml`。所有导入的文件路径都是相对于当前文件路径进行解析的，所以`services.xml`必须与当前文件在同一目录或类路径位置，而`messageSource.xml`和`themeSource.xml`必须在当前文件的位置下的路径中。如您所见，前导斜杠`/`会被忽略。然而，为了更好的形式，最好完全不用斜杠。被导入的文件中，包括顶层的`<beans/>`元素在内，必须是符合Spring Schema的有效XML bean定义。这意味着这些文件中的内容必须遵循Spring框架定义的XML结构和规范，以确保正确解析和加载bean定义。

> 通过使用`<import/>`元素或多个资源位置参数，可以将多个XML配置文件中的bean定义组合起来，形成更大规模和更复杂的应用程序上下文配置。这种方式使得应用程序的配置更模块化、可扩展，同时也提高了代码的组织性和可维护性。

**注意：**

* 虽然可以使用相对的 "../" 路径引用父目录中的文件，但并不推荐这样做。这样做会创建对当前应用程序之外的文件的依赖关系。特别是对于类路径（`classpath:`）URLs，这种引用方式不推荐使用（例如，classpath:../services.xml），因为运行时解析过程会选择"最近"的类路径根目录，然后查找其父目录。类路径配置的更改可能导致选择错误的目录。
* 相比于使用相对路径，也可以使用完全限定的资源路径，例如`file:C:/config/services.xml`或`classpath:/config/services.xml`。然而，要注意的是，将应用程序的配置与特定的绝对路径耦合在一起。通常更好的做法是为这样的绝对路径做一个间接引用，例如通过在运行时根据JVM系统属性解析"${...}"占位符。

> 当您需要引用位于父目录中的文件时，可以考虑以下两种方法：
>
> 1. 使用绝对路径：您可以使用完全限定的资源位置来引用文件。例如，假设配置文件位于`C:/config/services.xml`，您可以使用以下路径引用它：`file:C:/config/services.xml`。这种方式明确指定了文件的绝对位置，但它将应用程序的配置与特定的文件系统结构绑定在一起。
> 2. 使用占位符：您可以使用占位符并在运行时解析它们，以避免直接引用特定的绝对路径。例如，您可以在配置文件中使用类似于"`${config.path}/services.xml`"的占位符，然后在运行时通过配置系统属性或环境变量为该占位符提供实际的值。这种方式使得配置更具灵活性和可移植性，因为您可以在不同的环境中使用不同的配置值。

命名空间本身提供了import指令的功能。Spring提供了一系列XML命名空间，除了普通的bean定义之外，还提供了其他的配置特性。例如，`context`命名空间和`util`命名空间。

> 通过在XML配置文件中引入相应的命名空间，您可以使用特定命名空间提供的功能和元素来配置应用程序上下文和实用工具。例如，context命名空间提供了许多与应用程序上下文相关的配置元素，如组件扫描、自动装配、属性占位符等。util命名空间则提供了一些通用的实用工具，如集合操作、数据转换等。

### 使用Groovy定义Bean（The Groovy Bean Definition DSL）

作为外部化配置元数据的进一步示例，bean定义还可以使用Spring的Groovy Bean Definition DSL来表示，这是从Grails框架中熟悉的。通常，这样的配置存储在一个以".groovy"结尾的文件中，具有以下示例中展示的结构：

```groovy
beans {
	dataSource(BasicDataSource) {
		driverClassName = "org.hsqldb.jdbcDriver"
		url = "jdbc:hsqldb:mem:grailsDB"
		username = "sa"
		password = ""
		settings = [mynew:"setting"]
	}
	sessionFactory(SessionFactory) {
		dataSource = dataSource
	}
	myService(MyService) {
		nestedBean = { AnotherBean bean ->
			dataSource = dataSource
		}
	}
}
```

这种配置样式在很大程度上等同于XML bean定义，甚至支持Spring的XML配置命名空间。它还允许通过importBeans指令导入XML bean定义文件。

> 通过使用Groovy Bean Definition DSL，您可以在配置中利用Groovy语法的灵活性和强大功能，以一种更简洁、更易于维护的方式定义和管理Spring的bean定义。这种方式与XML配置相比，可以提供更好的可读性和可编程性，同时也可以与现有的Groovy代码更好地集成。

### 使用容器（Using the Container）

ApplicationContext是一个高级工厂的接口，它能够维护不同bean及其依赖关系的注册表（*registry*）。通过使用`T getBean(String name, Class requiredType)`方法，您可以取出（*retrieve*）您的bean的实例。

ApplicationContext允许您读取bean定义并访问它们，如下例所示：

```java
// 创建和配置bean
ApplicationContext context = new ClassPathXmlApplicationContext("services.xml", "daos.xml");

// 取出配置的实例
PetStoreService service = context.getBean("petStore", PetStoreService.class);

// 使用配置的实例
List<String> userList = service.getUsernameList();
```

使用Groovy配置时，引导过程非常类似。它具有不同的上下文实现类，该类具有Groovy意识（*Groovy-aware*）（但也能理解XML bean定义）。以下示例显示了Groovy配置：

```java
ApplicationContext context = new GenericGroovyApplicationContext("services.groovy", "daos.groovy");
```

最灵活的方式是同时使用`GenericApplicationContext`和读取器代理（*reader delegates*），例如，对于XML文件，可以使用`XmlBeanDefinitionReader`，如下例所示：

```java
GenericApplicationContext context = new GenericApplicationContext();
new XmlBeanDefinitionReader(context).loadBeanDefinitions("services.xml", "daos.xml");
context.refresh();
```

您还可以使用`GroovyBeanDefinitionReader`来处理Groovy文件，如下例所示：

```java
GenericApplicationContext context = new GenericApplicationContext();
new GroovyBeanDefinitionReader(context).loadBeanDefinitions("services.groovy", "daos.groovy");
context.refresh();
```

您可以在同一个`ApplicationContext`中混合使用不同的读取器代理，从不同的配置源读取bean定义。

然后，您可以使用`getBean`方法来取出您的bean实例。ApplicationContext接口还有一些其他用于取出bean的方法，但是理想情况下，您的应用程序代码不应该使用它们。实际上，您的应用程序代码根本不应该调用`getBean()`方法，从而完全不依赖于Spring API。例如，Spring与Web框架的集成提供了各种Web框架组件（如控制器*controllers*和JSF管理的bean）的依赖注入，通过元数据（如自动装配注解*autowiring annotation*）可以声明对特定bean的依赖关系。

