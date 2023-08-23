# 一、委托
为什么需要委托？
想象一下，你希望编写一个函数，这个函数的一个参数是另一个函数。这样，你可以在函数内部调用这个作为参数的函数。为了做到这一点，你需要一种方法来“传递”函数作为参数。在 C# 中，这是通过委托来实现的。

委托用来储存一个方法或者多个方法的引用，并且可以将这些方法作为参数传递，储存和调用

例如，下面是一个委托类型的定义：这个委托类型可以表示两个 int 类型参数，返回一个 int 类型的方法。在定义好委托类型后，就可以创建一个委托实例，将其指向一个具有相同签名的方法。例如：

```
delegate int MyDelegate(int arg1, int arg2);
```

在这个示例中，我们定义了一个名为 Add 的方法，它接受两个 int 类型的参数，并返回它们的和。然后，我们创建了一个 MyDelegate 类型的委托实例 myDelegate，将其指向 Add 方法。

```
int Add(int x, int y)
{
    return x + y;
}

MyDelegate myDelegate = new MyDelegate(Add);
```

# 二、Func 和 Action
Func 和 Action 是两种常见的委托类型：

Func：Func 是一个泛型委托类型，它可以引用具有指定返回类型的方法。Func 委托可以接受零个或多个输入参数，并返回一个值。最后一个泛型参数表示返回类型，前面的泛型参数表示输入参数的类型。例如，Func<int, int, string> 表示接受两个整数参数并返回一个字符串的方法。

Action：Action 是一个委托类型，它可以引用不返回值的方法（即无返回类型的方法）。Action 委托可以接受零个或多个输入参数，但不返回任何值。例如，Action<string> 表示接受一个字符串参数的方法。

下面是一些示例，说明如何使用 Func 和 Action：

使用 Func 示例：

```
// 定义一个接受两个整数参数并返回它们的和的方法
int Add(int a, int b)
{
    return a + b;
}

// 创建一个 Func 委托实例来引用 Add 方法
Func<int, int, int> addFunc = Add;

// 调用委托实例，传递两个整数参数，并获取返回值
int result = addFunc(2, 3); // 结果为 5
```

在上述示例中，我们定义了一个接受两个整数参数并返回它们的和的方法 Add。然后，我们创建了一个 Func 委托实例 addFunc，并将其指向 Add 方法。最后，我们通过调用委托实例并传递两个整数参数来获取返回值。

使用 Action 示例：

```
// 定义一个接受字符串参数并打印它的方法
void PrintMessage(string message)
{
    Console.WriteLine(message);
}

// 创建一个 Action 委托实例来引用 PrintMessage 方法
Action<string> printAction = PrintMessage;

// 调用委托实例，传递一个字符串参数
printAction("Hello, World!"); // 输出 "Hello, World!"
```

在上述示例中，我们定义了一个接受字符串参数并打印它的方法 PrintMessage。然后，我们创建了一个 Action 委托实例 printAction，并将其指向 PrintMessage 方法。最后，我们通过调用委托实例并传递一个字符串参数来执行方法。

通过使用委托类型（如 Func 和 Action），您可以将方法作为参数传递给其他方法，实现更灵活和可组合的代码。这在事件处理、回调函数和函数式编程等场景中非常有用。

# 三、🌰🌰🌰

```
 protected async Task Run<T>(Func<T, Task> action, Action<ContainerBuilder> extraRegistration = null)
 {
    var dependency = extraRegistration != null
        ? _scope.BeginLifetimeScope(extraRegistration).Resolve<T>()
        : _scope.BeginLifetimeScope().Resolve<T>();
    await action(dependency);
 }
```

Run<T>是一个方法名，<T> 是一个泛型参数，用于指定方法接受的参数类型。
Func<T, Task>表明，接受类型为 T 的参数并返回一个 Task 的委托
Action<ContainerBuilder>：这是一个可选参数，它接受一个类型为 ContainerBuilder 的参数的委托

```
public async Task RegisterUserAccount(string username, string password)
{
    await Run<IMediator>(async mediator =>
    {
        await mediator.SendAsync<RegisterCommand, RegisterResponse>(new RegisterCommand
        {
            UserName = username,
            Password = password
        });
    });
}
```

Run<IMediator> 表示调用 Run 方法并将泛型参数 T 指定为 IMediator ，意味着 action 参数应该是一个接受 IMediator 类型的参数并返回一个异步任务的方法
它传递了一个异步 Lambda 表达式给 Run 方法作为 action 参数。
这个 Lambda 表达式接受一个 IMediator 类型的参数 mediator，并在方法体内部调用了 mediator.SendAsync<RegisterCommand, RegisterResponse>(new RegisterCommand()) 方法。

### 总结

1：Action用于没有返回值的方法（参数可以根据自己情况进行传递）

2：Func恰恰相反用于有返回值的方法（同样参数根据自己情况情况）

3：记住无返回就用action，有返回就用Func