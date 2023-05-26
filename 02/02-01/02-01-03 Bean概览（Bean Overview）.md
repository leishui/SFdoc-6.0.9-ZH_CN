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

### 在Bean的定义之外对Bean取别名（Aliasing a Bean outside the Bean Definition）

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
>   
>   @Bean(name = {"subsystemA-dataSource", "myApp-dataSource"})
>   public DataSource dataSource() {
>     // Create and configure the DataSource
>     // ...
>     return dataSource;
>   }
>   
> }
> ```
>
> 在上述示例中，`dataSource()`方法使用`@Bean`注解定义了一个Bean，并通过`name`属性为该Bean指定了两个别名，即`subsystemA-dataSource`和`myApp-dataSource`。
>
> 通过使用`@Bean`注解的`name`属性，可以为Bean定义多个别名，让不同的组件或模块可以使用自己的命名约定来引用该Bean。