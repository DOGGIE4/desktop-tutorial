#  一、  Autofac的作用
Autofac是一个开源的依赖注入（DI）和控制反转（IoC）容器，用于.NET应用程序的构建和管理对象的生命周期。
#  二、  安装
打开Rider，打开你的项目。
![image.png](https://upload-images.jianshu.io/upload_images/29177961-386a165a8e6db545.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
在NuGet包管理器对话框中，搜索框中输入“Autofac”，在搜索结果中找到“Autofac”包，选择它。
![image.png](https://upload-images.jianshu.io/upload_images/29177961-4cc3492ccb287f78.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 三、 创建注入类

```
public interface IHelloMessageService
{
    string GetMessage();
}

public class HelloMessageService : IHelloMessageService
{
    public string GetMessage()
    {
        return "Hello World";
    }
}
```
# 四、 配置Autofac
## 创建 container 容器
```
 public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .UseServiceProviderFactory(new AutofacServiceProviderFactory())
            .ConfigureContainer<ContainerBuilder>(builder =>
```
这行代码中，我们使用了UseServiceProviderFactory方法，并传递了一个AutofacServiceProviderFactory实例作为参数，这个实例是由Autofac提供的一个工厂类，用于创建Autofac容器并返回一个IServiceProvider实例。
在使用AutofacServiceProviderFactory创建容器时，我们可以在主机构建器中使用.ConfigureContainer<ContainerBuilder>方法来配置容器，这个方法接受一个泛型参数ContainerBuilder，用于指定容器的类型。

##  在Startup中配置
```
  public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc();
        services.AddControllers();
    }
```
##  注册组件
通过 RegisterType() 方法将自定义的类注入到 Autofac 容器中，如下代码所示：
```
builder.RegisterType<HelloMessageService>().As<IHelloMessageService>().InstancePerLifetimeScope();
```
## 在 Controller 中使用 Autofac
```
public class HelloWorldController : ControllerBase
{
    private readonly IHelloMessageService _helloMessageService;
    private readonly InstancePerService _instancePerService;
    
    public HelloWorldController(IHelloMessageService helloMessageService, InstancePerService instancePerService)
    {
        _helloMessageService = helloMessageService;
        _instancePerService = instancePerService;
    }

    [HttpGet]
    [Route("api/hello")]
    public IActionResult Hello()
    {
        _instancePerService.Test();
        return Ok(_helloMessageService.GetMessage());
    }
}
```
![image.png](https://upload-images.jianshu.io/upload_images/29177961-bb8ea70cc2e87248.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




