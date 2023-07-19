# 一、mediator Pattern的概念

中介者模式（Mediator Pattern）用于把多个对象之间的复杂交互和控制逻辑集中到一个中介者对象中，把各个对象之间的关系变为一对多的关系，从而降低了系统的复杂性。

mediator扮演着协调者和调度者的角色，它负责处理对象之间的通信和协作，从而达到解耦的目的。

# 二、mediator与三层架构

mediator在mvc中的作用：controller层可以不用直接和services层做"交流",它不再需要引入其他类，也不再需要理解如何调用这些类，它只需要引入一个mediator，让它把你的请求发到去目的地，目的地也会把对应的结果发回来。

举个生动的例子，假设你需要租房，你可以选择自己寻找许多不同的房子，但是也可以选择寻找房屋中介，你只需要需求告诉房屋中介，让它去寻找符合你需求的房源即可。
假设controller发送了一个请求，可以利用mediator去找到处理这个请求的handler，并且handle这个请求

# 三、mediator具体是如何实现的？

Mediator的工作原理是它注册了Handler和信息（Message）之间的绑定，因此当他收到特定信息时就会在它的注册表里找出对应的Handler从而调用。

那么，mediator具体是怎么实现寻找到对应的handler的呢？
首先我们会在controller使用SendAsync(command)/RequestAsync(request)，（他们都使用了SendMessage来判断传来的IMessage是属于command/request/event中的哪一种类型）
随后选择到对应的管道，寻找到对应的handler，最后handle执行的任务

即：假设我们传递的是SendAsync(command). （ps:虽然说我们现在限定了它是一个command,但其实这个command只是IMessage中的一种，还是需要通过SendMessage在生成传参实例的时候去判断这个iMessage是属于command/request/event哪一个）；
通过SendMessage才能判断好SendAsync（command）是一个command
那么mediator就可以选择对应的commandHandler去handle处理这个command；

# 四、把mediator融入到项目中去

在controller层，我们引入一个mediator，controller发对应的信息（message contract)。

```
[HttpPost]
[Route("create")]
public async Task<IActionResult> CreateAsync([FromBody] CreatePeopleCommand command)
{
    var response = await _mediator.SendAsync<CreatePeopleCommand, CreatePeopleResponse>(command).ConfigureAwait(false);
            
    return Ok(response);
}
```

CreatPeopleCommand中，ICommand和IResponse都是通过调用IMessage来判断CreatPeopleCommand是一个Command；CreatePeopleResponse是一个Response

```
public class CreatePeopleCommand : ICommand
{
    public Person person { get; set; }
}

public class CreatePeopleResponse : IResponse
{
    public string result { get; set; }
}
```

随后进入到对应的管道，寻找到对应的CreatePeopleCommandHandler，在这里面调用Handle方法来调用了 _personService 的 AddPersonAsync 方法

```
public async Task<CreatePeopleResponse> Handle(IReceiveContext<CreatePeopleCommand> context, CancellationToken cancellationToken)
{
    var @event = await _personService.AddPersonAsync(context.Message, cancellationToken).ConfigureAwait(false);
    
    await context.PublishAsync(@event, cancellationToken).ConfigureAwait(false);

    return new CreatePeopleResponse
    {
        result = @event.result
    };
}
```

上面的`CommandHandler`中的主要功能实现后会生成一个事件，并通过`PublishAsync`通知到对应的 `EventHandler`去做后续的处理

```
public class PeopleCreatedEvent : IEvent
{
    public string result { get; set; }
}
```

```
public class PeopleCreatedEventHandler : IEventHandler<PeopleCreatedEvent>
{
    public async Task Handle(IReceiveContext<PeopleCreatedEvent> context, CancellationToken cancellationToken)
    {
        // 或者说是其他处理逻辑
        await Task.CompletedTask;
    }
}
```

PersonService中调用PersonDataProvider的CreatAsync方法

```
public async Task<PeopleCreatedEvent> AddPersonAsync(CreatePeopleCommand command, CancellationToken cancellationToken)
{
    return new PeopleCreatedEvent
    {
        result = await _personDataProvider.CreatAsync(command.person, cancellationToken).ConfigureAwait(false) > 0
            ? "数据写入成功"
            : "数据写入失败"
    };
}
```

在PersonDataProvider中去AddAsync

```
public async Task<int> CreatAsync(Person person, CancellationToken cancellationToken)
{
    await _dbContext.People.AddAsync(person, cancellationToken).ConfigureAwait(false);

    return await _dbContext.SaveChangesAsync(cancellationToken).ConfigureAwait(false);
}
```