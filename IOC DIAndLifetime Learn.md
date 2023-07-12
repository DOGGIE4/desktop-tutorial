# 一、  IOC是什么
在计算机编程中，IOC（Inversion of Control，控制反转）的生命周期通常指的是由IOC容器负责创建、管理和销毁对象的过程。在这个过程中，IOC容器根据配置文件或注解等方式，自动创建与管理对象的实例，以满足应用程序的需求。

首先根据名称，我们来思考一下，控制了什么？
传统程序当中，我们定义的一个A类，需要另一个B类时，直接就在A类的内部通过new进行创建依赖的b对象了，是我们的程序主动去创建依赖对象。而IoC它的核心思想就是有一个容器专门来创建这些依赖的对象，即由IoC容器来控制依赖的对象的创建。谁控制了谁？那当然是IoC容器控制了对象；
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
在这个示例中，UserService 类在其构造函数中创建了一个 UserDataProvider 类的实例并将其赋值给私有字段 _userDataProvider。在类的 GetUserById 方法中，我们使用 _userDataProvider 对象来获取用户信息。此时，UserService 类依赖于 UserDataProvider 类。如果我们想要更改依赖关系，例如使用不同的 UserDataProvider 实现，我们需要修改 UserService 类的代码。

那反转又是什么意思呢？
反转则是由容器来帮忙去创建对象及注入依赖对象，对象只是被动的接收依赖对象，所以就是依赖对象的获取方式被反转了，从之前的主动出击，实例化依赖对象，到现在注入依赖对象，被动接受。
#  三、  如何实现

上面所说IOC是一种设计模式，那如何具体实现呢？这时候我们就要涉及到 依赖注入（Dependency Injection）；
## 1.D I
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



## 2. 生命周期

IOC容器中对象的生命周期可以分为以下几个阶段：
实例化：IOC容器通过反射或其他方式创建对象的实例。
属性注入：IOC容器将依赖项注入到对象中，以满足对象的需求。
初始化：在注入完成后，可以执行一些初始化的操作，例如调用初始化方法或触发事件。
使用：对象可以被应用程序使用。
销毁：当对象不再使用时，IOC容器可以销毁对象，并释放与其相关的资源。

IOC容器可以管理对象的生命周期，以确保对象的正确创建、管理和销毁，以提高应用程序的可维护性和可扩展性。

Singleton（单例）
在应用程序生命周期内创建一个对象。这意味着所有使用该对象的组件都将共享同一个实例。这种生命周期适用于那些应用程序级别的对象，例如日志记录器等。示例代码：
```
services.AddSingleton<ILogger, Logger>();
```
Scoped（范围内）
每个请求会创建一次服务实例。在每个 HTTP 请求期间创建一个对象。这意味着对于同一个 HTTP 请求，所有使用该对象的组件都会使用同一个实例。这种生命周期适用于那些具有状态的对象，例如数据库上下文等。示例代码：我们可以使用 AddScoped 方法注册 Scoped 服务，如下所示：
```
services.AddScoped<IDbContext, DbContext>();
```
Transient（临时）
在每次请求时创建一个新的对象。这意味着每次请求时都会创建一个新的对象实例。这种生命周期适用于那些无状态的对象，例如服务或者工具类等。可以使用 AddSingleton 方法注册 Singleton 服务，如下所示
```
services.AddTransient<ICustomerService, CustomerService>();
```