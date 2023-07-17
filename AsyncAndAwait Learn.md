# 一、 Async和await

当需要执行一些耗时的操作时，例如从数据库中读取大量数据，或者从远程服务器上下载大文件，这些操作可能会阻塞当前线程，导致程序失去响应。为了避免这种情况，可以使用异步方法来执行这些操作，让它们在后台线程中执行，并在执行完成后通知主线程。

“async”关键字将方法标记为异步，这意味着它可以在执行其他代码时在后台运行。将方法标记为异步时，可以使用“await”关键字来指示该方法应等待异步操作的结果，然后再继续。

# 二、示例

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

在上述代码中，使用 async 关键字定义了一个名为 DownloadDataAsync 的异步方法，它会从网络上下载数据并保存到文件中。在方法中，首先创建了一个 HttpClient 实例，然后使用它发送 HTTP 请求，等待响应结果。在等待响应结果的过程中，异步方法会暂停执行，并允许其他任务继续执行。当响应结果返回后，使用 await 关键字读取响应结果并将数据保存到文件中。

官网给出的示例：

```
 class Program
    {
        static async Task Main(string[] args)
        {
            Coffee cup = PourCoffee();
            Console.WriteLine("coffee is ready");
            //通过调用相应的异步方法来创建异步任务，这种方式可以同时启动多个异步任务，
            var eggsTask = FryEggsAsync(2);
            var baconTask = FryBaconAsync(3);
            var toastTask = MakeToastWithButterAndJamAsync(2);
            
            //使用[WhenAny]，您可以等待返回的任务，知道它已经完成。以下代码显示如何使用[WhenAny]等待第一个任务完成，然后处理其结果。
            var breakfastTasks = new List<Task> { eggsTask, baconTask, toastTask };
            while (breakfastTasks.Count > 0)
            {
                Task finishedTask = await Task.WhenAny(breakfastTasks);
                if (finishedTask == eggsTask)
                {
                    Console.WriteLine("eggs are ready");
                }
                else if (finishedTask == baconTask)
                {
                    Console.WriteLine("bacon is ready");
                }
                else if (finishedTask == toastTask)
                {
                    Console.WriteLine("toast is ready");
                }
                await finishedTask;
                //处理已完成任务的结果后，可以从传递给的任务列表中删除该已完成任务`WhenAny`。
                breakfastTasks.Remove(finishedTask);
            }

            Juice oj = PourOJ();
            Console.WriteLine("oj is ready");
            Console.WriteLine("Breakfast is ready!");
        }
```

总之，async 和 await 是 C# 中用于编写异步代码的关键字，可以帮助编写响应更快、效率更高的程序。通过使用异步方法，可以在执行期间暂停和恢复，以允许执行其他任务，而不会阻塞当前线程。
await 关键字可以等待异步操作的完成，并在操作完成后继续执行其他代码。

# 三、异步方法中发生了些什么？

异步方法中，控制流是如何移动的呢？
![image.png](https://upload-images.jianshu.io/upload_images/29177961-49652ed37c601df2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

具体来说，当GetUrlContentLengthAsync方法调用GetStringAsync方法时，GetStringAsync方法会启动一个异步操作来获取指定URL的内容，并立即返回一个Task<string>对象。此时，GetUrlContentLengthAsync方法将控制权返回给调用方，同时异步操作在后台继续运行。

接着，GetUrlContentLengthAsync方法调用DoIndependentWork方法，该方法输出一条消息，但与异步操作无关。在DoIndependentWork方法执行完毕后，控制流重新回到GetUrlContentLengthAsync方法。

此时，由于GetStringAsync方法仍在后台执行，因此GetUrlContentLengthAsync方法会继续执行下一行代码，即使用await关键字等待异步操作完成。在等待过程中，控制流会暂时离开GetUrlContentLengthAsync方法，并返回调用方，直到异步操作完成并返回结果。

当异步操作完成后，控制流会回到GetUrlContentLengthAsync方法，并将结果存储在contents变量中。随后，GetUrlContentLengthAsync方法返回contents字符串的长度作为一个int值。

总之，通过使用异步方法，可以在执行期间暂停和恢复，以允许执行其他任务，而不会阻塞当前线程。await 关键字可以等待异步操作的完成，并在操作完成后继续执行其他代码。

# 四、补充——EFCore中使用Async

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