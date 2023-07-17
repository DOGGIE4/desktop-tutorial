# 一、什么是异步？

异步操作通常用于执行完成时间可能较长的任务，如打开大文件、连接远程计算机或查询数据库=异步操作在主应用程序线程以外的线程中执行。应用程序调用方法异步执行某个操作时，应用程序可在异步方法执行其任务时继续执行。

## 1、Async和await

“async”关键字将方法标记为异步，这意味着它可以在执行其他代码时在后台运行。将方法标记为异步时，可以使用“await”关键字来指示该方法应等待异步操作的结果，然后再继续。

# 二、同步与异步

同步（`Synchronous`）：在执行某个操作时，应用程序必须等待该操作执行完成后才能继续执行。

异步（`Asynchronous`）：在执行某个操作时，应用程序可在异步操作执行时继续执行。实质：异步操作，启动了新的线程，主线程与方法线程并行执行。

# 三、示例

假设需要从网络上下载一些数据，但下载的过程可能需要一段时间，为了不阻塞 UI 线程，以免影响用户的使用体验，可以使用异步方法来执行下载操作。例如：

```
async Task DownloadDataAsync()
{
    // 创建用于下载数据的 HttpClient 实例
    var client = new HttpClient();

    // 发送 HTTP 请求，并等待响应结果
    var response = await client.GetAsync("http://example.com/data");

    // 读取响应结果，并将数据保存到文件中
    var data = await response.Content.ReadAsStringAsync();
    File.WriteAllText("data.txt", data);
}
```

在上述代码中，使用 async 关键字定义了一个名为 DownloadDataAsync 的异步方法，它会从网络上下载数据并保存到文件中。

在方法中，首先创建了一个 HttpClient 实例，然后使用它发送 HTTP 请求，等待响应结果。

在等待响应结果的过程中，异步方法会暂停执行，并允许其他任务继续执行。当响应结果返回后，使用 await 关键字读取响应结果并将数据保存到文件中。

# 1.官网给出的示例：

假设现在需要做早餐，有以下的六个流程。

1.倒一杯咖啡。2.热锅，然后煎两个鸡蛋。3.煎三片培根。4.烤两片面包。5.在吐司中加入黄油和果酱。6.倒入一杯橙汁。

## （1）同步的流程：

![image.png](https://upload-images.jianshu.io/upload_images/29177961-7b0daf463557ad03.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

使用同步准备的早餐大约花了30分钟，因为总时间是每个任务时间的总和。计算机会阻塞每个语句，直到一个工作完成，然后再继续执行下一个语句。这就造成了一顿令人不满意的早餐。直到前面的任务完成后，后面的任务才会开始。准备早餐需要更长的时间，而且有些食物在上菜之前就已经冷了。

# （2）而使用异步，不会阻塞任务，而是等待任务

```
static async Task Main(string[] args)
{
    Coffee cup = PourCoffee();
    Console.WriteLine("coffee is ready");

    Egg eggs = await FryEggsAsync(2);
    Console.WriteLine("eggs are ready");

    Bacon bacon = await FryBaconAsync(3);
    Console.WriteLine("bacon is ready");

    Toast toast = await ToastBreadAsync(2);
    ApplyButter(toast);
    ApplyJam(toast);
    Console.WriteLine("toast is ready");

    Juice oj = PourOJ();
    Console.WriteLine("oj is ready");
    Console.WriteLine("Breakfast is ready!");
}
```

为了让线程在任务运行时不会阻塞。await关键字提供了一种非阻塞方式来启动任务。这段代码中，每个异步操作都是依次执行的。在执行完一个异步操作后，才会执行下一个异步操作。例如，先执行 FryEggsAsync 方法，等待其完成后再执行 FryBaconAsync 方法，再等待其完成后再执行 ToastBreadAsync 方法。这种方式可以保证异步操作的执行顺序，但可能会增加等待时间，降低程序的效率。

# （3）那如何才能节省时间，来完成早餐呢？我们可以选择同时启动任务

将以上代码修改为:

```
Coffee cup = PourCoffee();
Console.WriteLine("Coffee is ready");

Task<Egg> eggsTask = FryEggsAsync(2);
Task<Bacon> baconTask = FryBaconAsync(3);
Task<Toast> toastTask = ToastBreadAsync(2);

Toast toast = await toastTask;
ApplyButter(toast);
ApplyJam(toast);
Console.WriteLine("Toast is ready");
Juice oj = PourOJ();
Console.WriteLine("Oj is ready");

Egg eggs = await eggsTask;
Console.WriteLine("Eggs are ready");
Bacon bacon = await baconTask;
Console.WriteLine("Bacon is ready");

Console.WriteLine("Breakfast is ready!");
```

此时，三个异步操作是同时启动的。在启动异步操作后，使用 await 关键字等待异步操作的完成。由于异步操作是同时启动的，因此可以减少等待时间，提高程序的效率。

但是需要注意的是，异步操作的完成顺序可能是不确定的，因此需要在使用异步操作的结果时进行判断，避免出现错误。

此段代码中，Egg 和 Bacon 的异步任务是并行执行的，而ToastBreadAsync 方法完成后，才会执行 ApplyButter 和 ApplyJam 方法。这种方式可以保证 ApplyButter 和 ApplyJam 方法的正确执行顺序，避免出现错误。

这时我们就要提及一个一个重要概念：

* 异步操作后跟同步工作的组合是异步操作。换句话说，如果操作的任何部分是异步的，则整个操作也是异步的。

```
static async Task<Toast> MakeToastWithButterAndJamAsync(int number)
{
    var toast = await ToastBreadAsync(number);
    ApplyButter(toast);
    ApplyJam(toast);

    return toast;
}
```

上面的方法`async`在其签名中具有修饰符。这向编译器发出信号，表明该方法包含一条`await`语句；它包含异步操作。此方法代表烤面包，然后添加黄油和果酱的任务。此方法返回一个[Task<TResult>]，它表示这三个操作的组合。主要代码块现在变成：

```
static async Task Main(string[] args)
{
    Coffee cup = PourCoffee();
    Console.WriteLine("coffee is ready");

    var eggsTask = FryEggsAsync(2);
    var baconTask = FryBaconAsync(3);
    var toastTask = MakeToastWithButterAndJamAsync(2);

    var eggs = await eggsTask;
    Console.WriteLine("eggs are ready");

    var bacon = await baconTask;
    Console.WriteLine("bacon is ready");

    var toast = await toastTask;
    Console.WriteLine("toast is ready");

    Juice oj = PourOJ();
    Console.WriteLine("oj is ready");
    Console.WriteLine("Breakfast is ready!");
}
```

所以可以通过将操作分离到返回任务的新方法中来组合任务。您可以选择何时等待该任务，选择同时启动其他任务。

# （4）Await tasks efficiently

*  `await`可以使用类的方法来改进前面代码末尾的一系列语句`Task`。其中一个 API 是[WhenAll]，它返回一个“任务”，当其参数列表中的所有任务都完成时，该任务完成，
   如以下代码所示：

```
await Task.WhenAll(eggsTask, baconTask, toastTask);
Console.WriteLine("Eggs are ready");
Console.WriteLine("Bacon is ready");
Console.WriteLine("Toast is ready");
Console.WriteLine("Breakfast is ready!");
```

* 另一种选择是使用[WhenAny]

它返回一个`Task<Task>`在其任何参数完成时完成的值。您可以等待返回的任务，知道它已经完成。以下代码显示如何使用[WhenAny]等待第一个任务完成，然后处理其结果。处理已完成任务的结果后，您可以从传递给 的任务列表中删除该已完成任务`WhenAny`。

```
//使用[WhenAny]，您可以等待返回的任务，知道它已经完成。以下代码显示如何使用[WhenAny]等待第一个任务完成，然后处理其结果。
var breakfastTasks = new List<Task> { eggsTask, baconTask, toastTask };
while (breakfastTasks.Count > 0)
{
    Task finishedTask = await Task.WhenAny(breakfastTasks);
    if (finishedTask == eggsTask)
    {
        Console.WriteLine("Eggs are ready");
    }
    else if (finishedTask == baconTask)
    {
        Console.WriteLine("Bacon is ready");
    }
    else if (finishedTask == toastTask)
    {
        Console.WriteLine("Toast is ready");
    }
    await finishedTask;
    //处理已完成任务的结果后，可以从传递给的任务列表中删除该已完成任务`WhenAny`。
    breakfastTasks.Remove(finishedTask);
}
```

这时，我们的做早餐时间就变成了酱紫：
![image.png](https://upload-images.jianshu.io/upload_images/29177961-bfe40d8453b10f8d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
异步准备的早餐的最终版本大约需要 15 分钟，因为一些任务同时运行，并且代码同时监视多个任务，并且仅在需要时才采取行动。

# 四、异步方法中发生了些什么？

异步方法中，控制流是如何移动的呢？
![image.png](https://upload-images.jianshu.io/upload_images/29177961-49652ed37c601df2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

具体来说，当GetUrlContentLengthAsync方法调用GetStringAsync方法时，GetStringAsync方法会启动一个异步操作来获取指定URL的内容，并立即返回一个Task<string>对象。此时，GetUrlContentLengthAsync方法将控制权返回给调用方，同时异步操作在后台继续运行。

接着，GetUrlContentLengthAsync方法调用DoIndependentWork方法，该方法输出一条消息，但与异步操作无关。在DoIndependentWork方法执行完毕后，控制流重新回到GetUrlContentLengthAsync方法。

此时，由于GetStringAsync方法仍在后台执行，因此GetUrlContentLengthAsync方法会继续执行下一行代码，即使用await关键字等待异步操作完成。在等待过程中，控制流会暂时离开GetUrlContentLengthAsync方法，并返回调用方，直到异步操作完成并返回结果。

当异步操作完成后，控制流会回到GetUrlContentLengthAsync方法，并将结果存储在contents变量中。随后，GetUrlContentLengthAsync方法返回contents字符串的长度作为一个int值。

总之，通过使用异步方法，可以在执行期间暂停和恢复，以允许执行其他任务，而不会阻塞当前线程。await 关键字可以等待异步操作的完成，并在操作完成后继续执行其他代码。

# 五、补充——EFCore中使用Async

EF Core 为所有执行 I/O 的同步方法提供异步对应方法。async这些与同步方法具有相同的效果，并且可以与 C#和关键字一起使用await。

PersonDateProvider中

```
    public async Task<int> CreatAsync(Person person)
    {
        await _dbContext.People.AddAsync(person).ConfigureAwait(false);

        return await _dbContext.SaveChangesAsync().ConfigureAwait(false);
    }

    public async Task<int> UpdatePersonAsync(Person person)
    {
        _dbContext.People.Update(person);

        return await _dbContext.SaveChangesAsync().ConfigureAwait(false);
    }

    public async Task<Person> GetPersonByIdAsync(int id)
    {
        return await _dbContext.People.FindAsync(id).ConfigureAwait(false) ?? new Person();
    }

    public async Task<List<Person>> GetPersonAllAsync()
    {
        return await _dbContext.People.ToListAsync().ConfigureAwait(false);
    }

```

ps：调用_dbContext.People.FindAsync(id)方法来异步查找指定id的Person对象。
使用await关键字等待FindAsync方法完成，并将结果存储在一个Person对象中。

使用ConfigureAwait(false)方法告诉编译器不要强制执行结果的上下文捆绑，以提高程序的性能。
使用null合并运算符??来检查返回的Person对象是否为null。如果是null，则返回一个新的Person对象；否则返回查找到的Person对象。

PersonService 中

```
  public async Task<string> AddPersonAsync(Person person)
  {
     return await _personDataProvider.CreatAsync(person).ConfigureAwait(false) > 0 ? "数据写入成功" : "数据写入失败";
  }

  public async Task<List<Person>> GetAllPersonsAsync()
  {
     return await _personDataProvider.GetPersonAllAsync().ConfigureAwait(false);
  }

  public async Task<Person> GetPersonByIdAsync(int id)
  {
     return await _personDataProvider.GetPersonByIdAsync(id).ConfigureAwait(false);
  }

  public async Task<string> UpdatePersonAsync(Person person)
  {
     return await _personDataProvider.UpdatePersonAsync(person).ConfigureAwait(false) > 0 ? "更改成功" : "更改失败";
  }
```

PeopleController中

```
[HttpPost]
[Route("create")]
public async Task<IActionResult> CreateAsync([FromBody] Person person)
{
    var response = await _personService.AddPersonAsync(person).ConfigureAwait(false);
            
    return Ok(response);
}

[HttpGet]
public async Task<IActionResult> GetByIdAsync(int id)
{
    var response = await _personService.GetPersonByIdAsync(id).ConfigureAwait(false);
            
    return Ok(response);
}

[HttpPost]
[Route("update")]
public async Task<IActionResult> UpdateAsync([FromBody] Person person)
{
    var response = await _personService.UpdatePersonAsync(person).ConfigureAwait(false);

    return Ok(response);
}

[HttpGet]
[Route("all")]
public async Task<IActionResult> GetAllAsync()
{
    var response = await _personService.GetAllPersonsAsync().ConfigureAwait(false);
            
    return Ok(response);
}
```