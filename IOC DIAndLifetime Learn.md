# 一、  IOC是什么
在计算机编程中，IOC（Inversion of Control，控制反转）的生命周期通常指的是由IOC容器负责创建、管理和销毁对象的过程。在这个过程中，IOC容器根据配置文件或注解等方式，自动创建与管理对象的实例，以满足应用程序的需求。

首先根据名称，我们来思考一下，控制了什么？  
传统程序当中，我们定义的一个A类，需要另一个B类时，直接就在A类的内部通过new进行创建依赖的b对象了，是我们的程序主动去创建依赖对象。    
而IoC它的核心思想就是有一个容器专门来创建这些依赖的对象，即由IoC容器来控制依赖的对象的创建。  
谁控制了谁？那当然是IoC容器控制了对象。
#  二、  IOC的作用
举个传统方法的例子：
例如，我们可能会在一个类的构造函数中创建一个依赖对象并将其赋值给一个私有字段，然后在类的其他方法中使用这个依赖对象：
```
public class UserService
{
    private readonly UserDataProvider _userDataProvider;

    public UserService()
    {
        _userDataProvider = new UserDataProvider();
    }

    public User GetUserById(int userId)
    {
        return _userDataProvider.GetUserById(userId);
    }
}
```
在这个示例中，UserService 类在其构造函数中创建了一个 UserDataProvider 类的实例并将其赋值给私有字段 _userDataProvider。
在类的 GetUserById 方法中，我们使用 _userDataProvider 对象来获取用户信息。此时，UserService 类依赖于 UserDataProvider 类。   
如果我们想要更改依赖关系，例如使用不同的 UserDataProvider 实现，我们需要修改 UserService 类的代码。

那反转又是什么意思呢？  
反转则是由容器来帮忙去创建对象及注入依赖对象，对象只是被动的接收依赖对象，所以就是依赖对象的获取方式被反转了，从之前的主动出击，实例化依赖对象，到现在注入依赖对象，被动接受。
#  三、  如何实现
上面所说IoC 是一种设计模式，那如何具体实现呢？这时候我们就要涉及到 依赖注入（Dependency Injection）；
## 1.  DI
DI是 IoC 的一种具体实现方式，它通过将依赖对象动态地注入到组件中，来解耦组件之间的依赖关系。它可以通过构造函数、属性、方法参数等方式实现，将依赖对象作为参数传递给组件，从而使得组件不再负责创建和管理依赖对象。通过依赖注入机制，我们有时候只需要简单地配置，无需修改代码即可完成自身逻辑，而不必去关心依赖的具体资源，姓甚名谁，由何处来，又去往何处。

以下是使用 IOC 容器和依赖注入技术的代码示例：
首先，我们需要定义一个接口 IUserDataProvider，表示用户仓库的功能：
```
public interface IUserDataProvider
{
    User GetUserById(int userId);
}
```
然后，我们实现这个接口：
```
public class UserDataProvider : IUserDataProvider
{
    public User GetUserById(int userId)
    {
        // 从数据库中获取用户信息
    }
}
```
接下来，我们使用 IOC 容器来创建和管理依赖关系：(容器我们可以用Autofac)
```
var builder = new ContainerBuilder();

builder.RegisterType<UserService>();
builder.RegisterType<UserDataProvider>().As<IUserDataProvider>();

var container = builder.Build();
```
上面代码中，我们注册了 UserService 类和 UserDataProvider 类，并将 UserDataProvider类注册为 IUserDataProvider 接口的实现类。这样，当我们从容器中获取 UserService 类的实例时，容器会自动创建 UserDataProvider 类的实例并将其注入到 UserService 类中

使用 IOC 容器和依赖注入技术的代码示例：
```
public class UserService
{
    private readonly IUserDataProvider _userDataProvider;

    public UserService(IUserDataProvider userDataProvider)
    {
        _userDataProvider = userDataProvider;
    }

    public User GetUserById(int userId)
    {
        return _userDataProvider.GetUserById(userId);
    }
}
```
在这个示例中，UserService 类的构造函数接受一个 IUserDataProvider 对象作为参数，并将其存储在私有字段 _userDataProvider 中。在类的 GetUserById 方法中，我们使用 _userDataProvider 对象来获取用户信息。

这种方法的优点是：  
依赖关系被解耦：UserService 类不再依赖于特定的 UserDataProvider 实现，而是依赖于 IUserDataProvider 接口。这使得我们可以更轻松地更改依赖关系，例如可以使用不同的 IUserDataProvider 实现。

简而言之，IOC就是一个创建对象和的工厂，要什么对象，他就给你什么对象。原先的依赖关系没有了，他们都依赖于IoC容器了，通过IoC容器来建立他们之间的关系。



##2. Lifetime Scope
Autofac 中Lifetime Scope的概念结合了控制范围和生命周期这两个概念。   
实际上，Lifetime Scope等同于应用程序中的一个工作单元。工作单元可能从开始就begin Lifetime Scope，然后从Lifetime Scope中解析该工作单元所需的服务。当您解决服务时，Autofac 会跟踪IDisposable已解决的一次性组件。在工作单元结束时，您处置关联的Lifetime Scope，Autofac 将自动清理/处置已解析的服务

### （1）Lifetime Scope控制的两个重要的事情是共享和处置

*   Lifetime Scope是可嵌套的，它们控制组件的共享方式。例如，“单例”服务可能会从根生命周期范围解析，而各个工作单元可能需要自己的其他服务实例。您可以通过在注册时设置实例范围来确定组件的共享方式。

*   Lifetime Scope跟踪一次性对象并在Lifetime Scope被处置时处置它们。例如，如果您有一个实现的组件`IDisposable`，并且您从Lifetime Scope解析它，则该scope将保留它并为您处理它。这样您的服务消费者就不必知道底层实现。

生命周期范围的部分工作是[处理您解析的组件](https://autofac.readthedocs.io/en/latest/lifetime/disposal.html)。当您解析实现 的组件时`IDisposable`，所属生命周期范围将保存对该组件的引用，以便在处置范围时可以正确处置该组件。如果您愿意，[您可以更深入地了解如何使用处置，但需要考虑一些基本事项：](https://autofac.readthedocs.io/en/latest/lifetime/disposal.html)

### （2）Scopes and Hierarchy
1.  如果您IDisposable从根生命周期范围（容器）解析项目，它们将被保留，直到容器被释放为止，这通常是在应用程序结束时。这可能会导致内存泄漏。始终尝试从子生命周期范围中解决问题，并在完成后处理这些范围。
2.  处置父生命周期范围不会自动处置孩子。如果您处置根生命周期作用域，它将不会处置子作用域。作为生命周期范围的创建者，您有责任负责任地处置它们。
3.  如果您处置父作用域但继续使用子作用域，事情将会失败。您无法从已处置的范围解析依赖项。建议您按照与创建顺序相反的顺序处置作用域。
#  四、  常见的 Autofac 生命周期示例：
##  Singleton（单例）
在应用程序生命周期内创建一个对象。这意味着所有使用该对象的组件都将共享同一个实例。这种生命周期适用于那些应用程序级别的对象，例如日志记录器等。示例代码：
```
builder.RegisterType<Logger>().As<ILogger>().SingleInstance();
```
## Scoped（范围内）
每个请求会创建一次服务实例。在每个 HTTP 请求期间创建一个对象。这意味着对于同一个 HTTP 请求，所有使用该对象的组件都会使用同一个实例。这种生命周期适用于那些具有状态的对象，例如数据库上下文等。示例代码：我们可以使用 AddScoped 方法注册 Scoped 服务，如下所示：
```
builder.RegisterType<DbContext>().As<IDbContext>().InstancePerLifetimeScope();
```
## Transient（临时）
在每次请求时创建一个新的对象。这意味着每次请求时都会创建一个新的对象实例。这种生命周期适用于那些无状态的对象，例如服务或者工具类等。可以使用 AddSingleton 方法注册 Singleton 服务，如下所示
```
builder.RegisterType<CustomerService>().As<ICustomerService>().InstancePerDependency();
```
以上示例中：builder 是 Autofac 中的一个 DI 容器注册器，ICustomerService、IDbContext 和 ILogger 是接口，而 CustomerService、DbContext 和 Logger 是对应的实现类。通过在容器中注册这些服务，并指定它们的生命周期，我们可以控制对象的创建和销毁行为，以达到最优的性能和内存占用效果。
