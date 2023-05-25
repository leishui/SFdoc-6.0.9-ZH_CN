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

![container](C:\Users\AXA\Desktop\Spring\02-01-02 容器概览(Container Overview).assets\container-magic.png)

### 配置元数据（Configuration Metadata）

如前面的图表所示，Spring IoC容器需要使用一种配置元数据。这个配置元数据代表了您作为应用程序开发者告诉Spring容器如何实例化、配置和组装应用程序中的对象。

传统上，配置元数据通常以简单直观的XML格式提供，这也是本章大部分内容用来传达Spring IoC容器的关键概念和特性的方式。

> XML格式的配置文件具有良好的可读性和可理解性，它允许您明确地定义对象的结构、依赖关系和属性值。通过使用XML配置文件，您可以将应用程序的组件和配置信息集中管理，使其更易于维护和修改。

**注意：**

​	XML格式的配置元数据并不是唯一允许的配置方式。Spring IoC	容器本身与配置元数据的实际编写格式完全解耦。现如今，许	多开发者选择使用基于Java的配置来构建他们的Spring应用程	序。

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
> 在这个示例中，`userService` bean引用了`userRepository` bean作为它的协作对象。通过设置``元素的`name`属性为`userRepository`，并使用`ref`属性将其引用为`userRepository` bean的ID。
>
> 通过这样的配置，`userService` bean可以与`userRepository` bean进行协作，实现业务逻辑的处理。

### 实例化容器（Instantiating a Container）