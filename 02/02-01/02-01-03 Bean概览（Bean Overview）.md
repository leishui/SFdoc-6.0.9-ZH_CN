## <02-01-03> Bean概览（Bean Overview）

一个Spring IoC容器管理一个或多个Bean。这些Bean是根据您提供给容器的配置元数据创建的（例如，以XML `<bean/> `定义的形式）。

在容器内部，这些Bean定义被表示为`BeanDefinition`对象，其中包含以下元数据（以及其他信息）：

1. 类名：通常是定义的Bean的实际实现类。

2. Bean行为配置元素，用于声明Bean在容器中的行为方式（作用域、生命周期回调等）。

3. 对于Bean执行其工作所需的其他Bean的引用。这些引用也称为协作对象或依赖项。

4. 设置新创建对象的其他配置设置，例如连接池的大小限制或管理连接池的Bean中要使用的连接数量。

这些元数据转化为组成每个Bean定义的一组属性。下表描述了这些属性：

| 属性                     | 说明                                                         |
| :----------------------- | :----------------------------------------------------------- |
| Class                    | [实例化Bean](https://docs.spring.io/spring-framework/reference/core/beans/definition.html#beans-factory-class) |
| Name                     | [命名Bean](https://docs.spring.io/spring-framework/reference/core/beans/definition.html#beans-beanname) |
| Scope                    | [Bean作用域](https://docs.spring.io/spring-framework/reference/core/beans/factory-scopes.html) |
| Constructor arguments    | [依赖注入](https://docs.spring.io/spring-framework/reference/core/beans/dependencies/factory-collaborators.html) |
| Properties               | [依赖注入](https://docs.spring.io/spring-framework/reference/core/beans/dependencies/factory-collaborators.html) |
| Autowiring mode          | [自动装配协作者](https://docs.spring.io/spring-framework/reference/core/beans/dependencies/factory-autowire.html) |
| Lazy initialization mode | [延迟初始化的Bean](https://docs.spring.io/spring-framework/reference/core/beans/dependencies/factory-lazy-init.html) |
| Initialization method    | [初始化回调](https://docs.spring.io/spring-framework/reference/core/beans/factory-nature.html#beans-factory-lifecycle-initializingbean) |
| Destruction method       | [销毁回调](https://docs.spring.io/spring-framework/reference/core/beans/factory-nature.html#beans-factory-lifecycle-disposablebean) |

除了包含有关如何创建特定Bean的信息的Bean定义之外，`ApplicationContext`还允许注册在容器外部（由用户）创建的现有对象。可以通过使用`getBeanFactory()`方法访问`ApplicationContext`的`BeanFactory`来实现。该方法返回`DefaultListableBeanFactory`。`DefaultListableBeanFactory`通过`registerSingleton(..)`和`registerBeanDefinition(..)`方法支持此注册。然而，典型的应用程序应仅使用通过常规Bean定义元数据定义的Bean。

**注意：**

* 在Spring容器中，Bean的元数据和手动提供的单例实例需要尽早注册，以便在自动装配和其他内省步骤中正确处理它们。虽然在一定程度上支持覆盖现有元数据和现有单例实例，但在运行时注册新的Bean（与对工厂的实时访问同时进行）并不被官方支持，可能会导致并发访问异常、Bean容器中的状态不一致，或其他问题。

> 因此，在应用程序的正常运行期间，通常不建议动态注册新的Bean。最好在容器启动时完成所有必要的注册操作。

### 命名Beans（Naming Beans）

每个Bean都有一个或多个标识符。这些标识符在管理Bean的容器内必须是唯一的。通常，一个Bean只有一个标识符。然而，如果一个Bean需要多个标识符，额外的标识符可以被视为别名。

在基于XML的配置元数据中，您可以使用`id`属性、`name`属性或两者都使用来指定Bean的标识符。`id`属性允许您指定一个唯一的`id`。一般情况下，这些名称是由字母和数字组成的（例如'myBean'、'someService'等），但它们也可以包含特殊字符。如果您想为Bean引入其他别名，您还可以在`name`属性中指定它们，用逗号（`,`）、分号（`;`）或空格（` `）分隔。虽然`id`属性被定义为`xsd:string`类型，但Bean的`id`唯一性是由容器强制执行的，而不是由XML解析器。

您也可不为Bean提供`name`或`id`。如果您没有显式提供`name`或id，容器将为该Bean生成一个唯一的名称。然而，如果您想通过名称引用该Bean，例如通过`ref`元素或Service Locator样式的查找，您必须提供一个名称。不提供名称的原因通常与使用[内部Bean](https://docs.spring.io/spring-framework/reference/core/beans/dependencies/factory-properties-detailed.html#beans-inner-beans)和[自动装配协作对象](https://docs.spring.io/spring-framework/reference/core/beans/dependencies/factory-autowire.html)有关。

**Bean的命名惯例：**

* 命名Bean时的惯例是使用标准的Java实例字段命名约定。也就是说，Bean名称以小写字母开头，然后采用驼峰命名法。例如，`accountManager`、`accountService`、`userDao`、`loginController`等都是这样的命名示例。
* 一致地为Bean命名可以使您的配置更易于阅读和理解。此外，如果使用了Spring AOP，这将很有利于按名称对一组相关的Bean应用进行通知（*advice*）。

**注意：**

* 在类路径中使用组件扫描时，Spring为未命名的组件生成Bean名称，遵循前面描述的规则：基本上，采用简单的类名，并将其首字母转换为小写。然而，在（不常见的）特殊情况下，如果类名不止一个字符，并且第一个和第二个字符都是大写字母时，则保留原始大小写。这些规则与`java.beans.Introspector.decapitalize`定义的规则相同（Spring使用该规则）。

####  在Bean的定义之外对Bean取别名（Aliasing a Bean outside the Bean Definition）

在Bean定义中，您可以通过使用`id`属性指定一个名称以及`name`属性指定任意数量的其他名称。这些名称可以作为等效的别名引用同一个Bean，在某些情况下非常有用。例如，让应用程序中的每个组件都使用特定于该组件自身的Bean名称来引用共享的依赖项。

> 假设我们有一个名为`userService`的Bean，它是一个用于处理用户相关逻辑的服务组件。我们可以在Bean定义中为它提供多个名称如`userManagementService`和`userManager`。这意味着无论是通过`userService`、`userManagementService`还是`userManager`这些名称来引用Bean，实际上都指向了同一个`UserService`实例。
>
> ```xml
> <!-- 使用name属性在Bean定义内部取别名 -->
> <bean id="userService" class="com.example.UserService" name="userManagementService, userManager" />
> ```
>
> ```xml
> <bean id="userService" class="com.example.UserService" />
> <!-- 在Bean定义外部取别名 -->
> <alias name="userManagementService" alias="userService" />
> <alias name="userManager" alias="userService" />
> ```
>
> 这对于不同组件或模块需要使用不同名称引用同一个Bean的情况非常有用。例如，一个组件可以使用`userService`来引用该服务，而另一个组件可以使用`userManagementService`来引用相同的服务，这样每个组件可以按照自己的逻辑命名并引用相应的Bean。这样的灵活性可以提高代码的可读性和可维护性。

在某些情况下，仅在定义Bean的地方指定所有别名可能不够。有时候需要为在其他地方定义的Bean引入别名。这在大型系统中很常见，其中配置被分割为各个子系统，每个子系统都有自己的对象定义集合。在基于XML的配置元数据中，您可以使用`<alias/>`元素来实现这一点：

```xml
<alias name="fromName" alias="toName"/>
```

在这种情况下，通过这个别名定义，在同一个容器中名为`fromName`的Bean也可以使用`toName`来引用。

举个例子，子系统 A 的配置元数据可能使用名为 `subsystemA-dataSource` 的名称引用一个数据源（*DataSource*）。子系统 B 的配置元数据可能使用名为 `subsystemB-dataSource` 的名称引用一个数据源。当主应用程序组合使用这两个子系统时，主应用程序可以通过名为 `myApp-dataSource` 的名称来引用数据源。为了让这三个名称都指向同一个对象，您可以将以下别名定义添加到配置元数据中：

```xml
<alias name="myApp-dataSource" alias="subsystemA-dataSource"/>
<alias name="myApp-dataSource" alias="subsystemB-dataSource"/>
```

现在，每个组件和主应用程序都可以通过一个唯一的名称引用数据源，这个名称不会与任何其他定义发生冲突（有效地创建了一个命名空间），但它们引用的是同一个Bean。

**使用Java配置：**

* 在使用Java配置时，可以使用`@Bean`注解来提供别名。通过在配置类的方法上使用`@Bean`注解，您可以定义Bean并使用`name`属性为其指定别名。

> 以下是一个示例：
>
> ```java
> @Configuration
> public class AppConfig {
>   	@Bean(name = {"subsystemA-dataSource", "myApp-dataSource"})
>   	public DataSource dataSource() {
>    		// Create and configure the DataSource
>     		// ...
>     		return dataSource;
>    	}
>   }
>   ```
> 
> 在上述示例中，`dataSource()`方法使用`@Bean`注解定义了一个Bean，并通过`name`属性为该Bean指定了两个别名，即`subsystemA-dataSource`和`myApp-dataSource`。
>
> 通过使用`@Bean`注解的`name`属性，可以为Bean定义多个别名，让不同的组件或模块可以使用自己的命名约定来引用该Bean。

### 实例化Bean（Instantiating Beans）

Bean定义是创建一个或多个对象的配方或描述。它包含了配置元数据，用于告诉容器如何创建或获取实际的对象。

当容器需要一个Bean时，它会查找相应的Bean定义，并根据其中的配置元数据来创建或获取对象。

如果您使用基于XML的配置元数据，您可以在`<bean/>`元素的`class`属性中指定要实例化的对象的类型*type*（或类*class*）。这个`class`属性（在`BeanDefinition`实例的内部实际上是一个`Class`成员变量）通常是必需的。（有例外情况，请参阅[使用实例工厂方法进行实例化](#通过实例工厂方法实例化（Instantiation by Using an Instance Factory Method）)和[Bean定义继承](https://docs.spring.io/spring-framework/reference/core/beans/child-bean-definitions.html)。）

`class`属性有两种常见的用法：

1. 在容器通过反射调用构造函数直接创建Bean时，用于指定要构造的Bean类。类似于使用Java代码中的`new`运算符来创建对象。
2. 在较少常见的情况下，容器调用一个类的静态工厂方法来创建Bean时，用于指定包含静态工厂方法的实际类。静态工厂方法的调用返回的对象类型可以是相同的类，也可以是完全不同的类。

**对于嵌套类：**

* 如果您想为嵌套类配置一个Bean定义，可以使用嵌套类的二进制名称（binary name）或源名称（source name）。
* 例如，假设您有一个名为`com.example`的包中的`SomeThing`类，而这个`SomeThing`类又有一个名为`OtherThing`的静态嵌套类。您可以使用美元符号（`$`）或点号（`.`）来分隔它们。因此，在Bean定义中，`class`属性的值可以是`com.example.SomeThing$OtherThing`或`com.example.SomeThing.OtherThing`。

#### 通过构造方法实例化（Instantiation with a Constructor）

通过构造函数方式创建Bean时，所有普通类都可以与Spring一起使用并兼容。也就是说，开发类时不需要实现任何特定的接口或以特定的方式编码。只需指定Bean类即可。然而，根据您对特定Bean使用的IoC类型，可能需要一个默认的（空）构造函数。

Spring IoC容器可以管理几乎任何您想要管理的类，它不仅限于管理真正的JavaBeans。大多数Spring用户更喜欢让JavaBeans只有一个默认（无参数）构造函数，并且具有与容器中的属性相匹配的设置器（setters）和访问器（getters）。您还可以在容器中拥有更多非传统的非JavaBean风格的类。例如，如果您需要使用一个绝对不符合JavaBean规范的传统连接池，Spring也可以进行管理。

使用基于XML的配置元数据，您可以按如下方式指定Bean类：

```xml
<bean id="exampleBean" class="examples.ExampleBean"/>
<bean name="anotherExample" class="examples.ExampleBeanTwo"/>
```

有关向构造函数提供参数（如果需要）以及在对象构造之后设置对象实例属性的机制的详细信息，请参阅"[注入依赖项](https://docs.spring.io/spring-framework/reference/core/beans/dependencies/factory-collaborators.html)"。

#### 通过静态工厂方法实例化（Instantiation with a Static Factory Method）

在定义使用静态工厂方法创建的Bean时，可以使用`class`属性来指定包含静态工厂方法的类，并使用名为`factory-method`的属性来指定工厂方法的名称。您应该能够调用此方法（如后面所述，可以使用可选参数），并返回一个实际对象，该对象被视为通过构造函数创建的对象。这种方法的一个用途是调用遗留代码中的静态工厂方法。

下面的Bean定义指定了通过调用工厂方法来创建Bean。该定义不指定返回对象的类型（类），而是指定包含工厂方法的类。在这个示例中，`createInstance()`方法必须是一个静态方法。以下示例演示了如何指定一个工厂方法：

```xml
<bean id="clientService"
	class="examples.ClientService"
	factory-method="createInstance"/>
```

以下是适用于上述Bean定义的Java代码例子：

```java
public class ClientService {
	private static ClientService clientService = new ClientService();
	private ClientService() {}

	public static ClientService createInstance() {
		return clientService;
	}
}
```

有关向工厂方法提供（可选）参数以及在从工厂返回对象后设置对象实例属性的详细机制，请参阅"[详细的依赖项和配置](https://docs.spring.io/spring-framework/reference/core/beans/dependencies/factory-properties-detailed.html)"。

#### 通过实例工厂方法实例化（Instantiation by Using an Instance Factory Method）

类似于使用[静态工厂方法](#通过静态工厂方法实例化（Instantiation with a Static Factory Method）)进行实例化，使用实例工厂方法进行实例化是通过调用容器中现有Bean的非静态方法来创建新的Bean。要使用这种机制，将`class`属性留空，并在`factory-bean`属性中指定当前（或父级或祖先）容器中包含要调用以创建对象的实例方法的Bean的名称。使用`factory-method`属性设置工厂方法本身的名称。下面的示例显示了如何配置这样一个Bean：

```xml
<!-- the factory bean, which contains a method called createInstance() -->
<bean id="serviceLocator" class="examples.DefaultServiceLocator">
	<!-- inject any dependencies required by this locator bean -->
</bean>

<!-- the bean to be created via the factory bean -->
<bean id="clientService"
	factory-bean="serviceLocator"
	factory-method="createClientServiceInstance"/>
```

```java
public class DefaultServiceLocator {

	private static ClientService clientService = new ClientServiceImpl();

	public ClientService createClientServiceInstance() {
		return clientService;
	}
}
```

一个工厂类也可以包含多个工厂方法：

```xml
<bean id="serviceLocator" class="examples.DefaultServiceLocator">
	<!-- inject any dependencies required by this locator bean -->
</bean>

<bean id="clientService"
	factory-bean="serviceLocator"
	factory-method="createClientServiceInstance"/>

<bean id="accountService"
	factory-bean="serviceLocator"
	factory-method="createAccountServiceInstance"/>
```

```java
public class DefaultServiceLocator {

	private static ClientService clientService = new ClientServiceImpl();

	private static AccountService accountService = new AccountServiceImpl();

	public ClientService createClientServiceInstance() {
		return clientService;
	}

	public AccountService createAccountServiceInstance() {
		return accountService;
	}
}
```

"[详细的依赖项和配置](https://docs.spring.io/spring-framework/reference/core/beans/dependencies/factory-properties-detailed.html)"展示了通过依赖注入（DI）对工厂Bean本身进行管理和配置。

**注意：**

* 在Spring文档中，"factory bean"指的是在Spring容器中配置的通过实例或静态工厂方法来创建对象的Bean。相比之下，`FactoryBean`（注意大写）是指Spring特定的[`FactoryBean`](https://docs.spring.io/spring-framework/reference/core/beans/factory-extension.html#beans-factory-extension-factorybean)实现类。

> 使用工厂Bean的方式允许您通过DI将工厂Bean自身的依赖项注入到其中，以及配置其属性和行为。这使得工厂Bean可以灵活地创建对象，并且可以在Spring容器中进行管理和配置。
>
> 另一方面，Spring的FactoryBean是一个特殊的接口，您可以实现它来创建自定义的FactoryBean实现类。这个接口提供了一些方法，允许您定义自己的逻辑来创建和管理对象。FactoryBean的实现类在Spring中也被称为FactoryBean。通过使用FactoryBean接口，您可以创建自定义的工厂Bean，将其注册到Spring容器中，并通过FactoryBean接口的方法来创建和提供对象。
>
> 请注意区分"factory bean"（指通过实例或静态工厂方法创建对象的Bean）和FactoryBean（指Spring特定的FactoryBean接口和实现类）这两个概念。

#### 确定Bean的运行时类型（Determining a Bean’s Runtime Type）

在Spring中，确定特定Bean的实际运行时类型并不是一件简单的事情。在Bean的元数据定义中指定的类只是一个初始的类引用，可能还与声明的工厂方法结合使用，或者是一个`FactoryBean`类。这可能导致Bean的实际运行时类型与指定的类不同。此外，如果存在实例级别的工厂方法（通过指定的`factory-bean`名称解析），则可能根本没有设置类。

另外，AOP代理可能会将Bean实例包装在基于接口的代理中，只暴露目标Bean实现的接口，而对于目标Bean的实际类型的暴露是有限的。

为了获取特定Bean的实际运行时类型，建议使用`BeanFactory.getType`方法，并提供指定的Bean名称作为参数。该方法考虑了上述所有情况并返回对象的类型，与使用相同Bean名称调用`BeanFactory.getBean`方法的返回对象类型相同。

> 也就是说，如果您使用BeanFactory.getType方法来获取特定bean的类型，它会考虑到以下因素：
>
> 1. 假设bean的类在元数据定义中指定了一个初始类引用，它会考虑到可能的工厂方法或FactoryBean类，以确定最终的运行时类型。
> 2. 如果bean使用了AOP代理，BeanFactory.getType方法将返回实际被代理的目标bean的类型，而不是代理本身的类型。
> 3. 对于使用实例级别的工厂方法创建的bean，BeanFactory.getType方法会根据指定的factory-bean名称来解析实际类型。