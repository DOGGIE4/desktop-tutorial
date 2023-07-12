# 三层架构
三层架构 (3-tier application) 是将整个业务应用划分为：表现层（UI）、业务逻辑层（BLL）、数据访问层（DAL）。区分层次的目的即为了“高内聚，低耦合”的思想。

1、表现层（UI）：展现给用户的界面，即用户在使用一个系统的时候的所见所得。

2、业务逻辑层（BLL）：对数据层的操作，对数据业务逻辑处理。

3、数据访问层（DAL）：直接操作数据库，针对数据的增添、删除、修改、更新、查找等。

Controller（表现层）中调用HelloMessageService（业务逻辑层）

```
    public HelloWorldController(IHelloMessageService helloMessageService)
    {
        _helloMessageService = helloMessageService;
    }

    [HttpGet]
    [Route("api/hello")]
    public IActionResult Hello()
    {
        return Ok(_helloMessageService.GetMessage());
    }
}
```

HelloMessageService（业务逻辑层）调用HelloWorldDataProvider（数据访问层），HelloMessageService继承IHelloMessageService类后，通过构造方法把HelloWorldDataProvider注入到HelloMessageService中，最后实现IHelloMessageService的方法。

```
public class HelloMessageService : IHelloMessageService
{
    private readonly IHelloWorldDataProvider _helloWorldDataProvider;

    public HelloMessageService(IHelloWorldDataProvider helloWorldDataProvider)
    {
        _helloWorldDataProvider = helloWorldDataProvider;
    }

    public string GetMessage()
    {
        return _helloWorldDataProvider.GetMessage();
    }
}
```

Provider中，类 HelloWorldDataProvider 实现了接口 IHelloWorldDataProvider，实现了接口中声明的 GetMessage 方法，并返回了一个字符串 "Hi"。

```
public class HelloWorldDataProvider : IHelloWorldDataProvider
{
    public string GetMessage()
    {
        return "Hi";
    }
}
```

DataProvider和Service为什么要分层呢？

为了实现每个层专注于负责自己的模块，假设我们要新增一条数据，我们就要在service里面增加数据，还要查询这条数据是否重复，这样的情况下，service中的代码会比较混乱。

其次，假设我们有多个方法都需要在数据库中进行select,为了减少代码的重复性，我们可以直接在dataprovider中实现select，然后在service中进行调用即可。
