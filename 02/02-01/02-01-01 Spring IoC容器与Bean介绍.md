# <02-01-01> Spring IoC容器与Bean介绍（Introduction to the Spring IoC Container and Beans）

本章介绍了Spring 框架对控制反转（Inversion of Control，**IoC**）设计原则的实现。IoC也被称为依赖注入（Dependency Injection，**DI**）。它是一个过程，对象通过构造函数参数、工厂方法的参数或在对象实例构建后或从工厂方法返回后设置的属性来定义它们的依赖关系（即其依赖的其他对象）。当容器创建Bean时，它会注入这些依赖。这个过程本质上是与Bean自身通过类的构造方法或使用Service Locator模式等机制来控制其依赖项的实例化或位置的过程是相反的（因此得名“控制反转”）。

> 换句话说，对象不再主动管理和查找依赖对象，而是通过容器来管理和注入依赖，从而实现了对象之间的解耦和灵活性。
>

<details>
  <summary>点击折叠/展开</summary>
    <p>当我们谈论控制反转（Inversion of Control，IoC）时，我们指的是一种设计原则，其中对象不再负责直接创建或管理其依赖关系。相反，这些依赖关系由外部容器负责创建和注入到对象中。这种通过容器来管理依赖关系的方式被称为依赖注入（Dependency Injection，DI）。</p>
    <p>在IoC/DI中，对象通过构造函数参数、工厂方法参数或对象实例创建后设置的属性来声明其依赖关系。这意味着对象自身不需要了解和创建它所依赖的对象，它只需要定义它们的接口或抽象类。容器负责解析对象之间的依赖关系，并在创建对象时将依赖关系注入到对象中。</p>
    <p>通过IoC/DI，我们实现了对象之间的松耦合，使得它们更加灵活、可测试和可扩展。对象不再与特定的实现细节紧密耦合，而是依赖于抽象接口。这样，我们可以轻松地替换具体的实现，而不需要修改依赖对象的代码。此外，通过将依赖关系的创建和管理交给容器，我们可以更好地管理对象的生命周期和作用域。</p>
    <p>Spring Framework提供了强大的IoC/DI容器，它可以管理对象之间的依赖关系，并自动注入所需的依赖项。这样，我们可以专注于业务逻辑的开发，而不用担心对象的创建和依赖关系的管理。</p>
</details>

`org.springframework.beans`和`org.springframework.context`这两个包是Spring框架的IoC容器的基础。[`BeanFactory`](https://docs.spring.io/spring-framework/docs/6.0.9/javadoc-api/org/springframework/beans/factory/BeanFactory.html)接口提供了一种高级的配置机制，可以管理任何类型的对象。[`ApplicationContext`](https://docs.spring.io/spring-framework/docs/6.0.9/javadoc-api/org/springframework/context/ApplicationContext.html) 是 `BeanFactory`的一个子接口，新添加了：

- 更容易与Spring的AOP特性集成
- 消息资源处理（用于国际化）
- 事件发布
- 应用程序层特定的上下文，如`WebApplicationContext`用于Web应用程序。

简而言之，`BeanFactory`提供了配置框架和基本功能，而`ApplicationContext`则增加了更多面向企业的特定功能。`ApplicationContext`是`BeanFactory`的完整超集，并且在本章节中专门用于描述Spring的IoC容器。如果想了解更多关于如何使用`BeanFactory`而不是`ApplicationContext`的信息，请参考涵盖[`BeanFactory` API](https://docs.spring.io/spring-framework/reference/core/beans/beanfactory.html)的章节。

在Spring中，由Spring IoC容器管理的构成应用程序骨架的对象被称为Beans。一个Bean是由Spring IoC容器实例化、组装和管理的对象。除此之外，一个Bean只是你的应用程序中的众多对象之一。Beans及其之间的依赖关系反映在容器使用的配置元数据中。