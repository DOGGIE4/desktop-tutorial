# 一、Hangfire是什么？

Hangfire可以帮助在 ASP.NET 或 .NET Core 应用程序中轻松地执行后台任务。

Hangfire 提供了一个简单的 API，可以添加、删除和管理后台任务，同时也提供了一个可扩展的插件模型，允许自定义任务调度和执行行为。

# 二、配置

## 1.引入包：

![image.png](https://upload-images.jianshu.io/upload_images/29177961-c4c0f7293e65704f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 2.新建一个类来配置：

```
public static class HangFireExtension
{
    public static void AddHangFireService(this IServiceCollection services, IConfiguration configuration)
    {
       services.AddHangfire(config => config
            .SetDataCompatibilityLevel(CompatibilityLevel.Version_170)
            .UseSimpleAssemblyNameTypeSerializer()
            .UseRecommendedSerializerSettings()
            .UseStorage(
                new MySqlStorage(
                    configuration.GetConnectionString("Default"),
                    new MySqlStorageOptions
                    {
                        QueuePollInterval = TimeSpan.FromSeconds(10),
                        JobExpirationCheckInterval = TimeSpan.FromHours(1),
                        CountersAggregateInterval = TimeSpan.FromMinutes(5),
                        PrepareSchemaIfNecessary = true,
                        DashboardJobListLimit = 25000,
                        TransactionTimeout = TimeSpan.FromMinutes(1),
                        TablesPrefix = "Hangfire",
                    }
                )
            ));

        // Add the processing server as IHostedService
        services.AddHangfireServer(options => options.WorkerCount = 1);
    }
}
```

然后在Configure方法中启用hangfire中间件:

```
app.UseHangfireServer();  //启动hangfire服务 
app.UseHangfireDashboard();  //启动hangfire面板
```

现在我们运行一下项目，可以看到，数据库里自动生成了很多表，这些表就是用来持久化任务的

![image.png](https://upload-images.jianshu.io/upload_images/29177961-bdb91cc41fb9ac01.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们打开如下地址，可以看到hangfire的作业控制面板（在没有开始任何作业的时候，仪表盘是空滴）

![image.png](https://upload-images.jianshu.io/upload_images/29177961-957ee35a2a51d141.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 三、hangfired 使用

官方网站：https://www.hangfire.io/

主要的三个创建任务的方法：

## 1. Fire-and-Forget Jobs

即发即忘作业仅执行一次，并且几乎在创建后立即执行。

```
var jobId = BackgroundJob.Enqueue(
    () => Console.WriteLine("Fire-and-forget!"));
```

## 2. Delayed Jobs

不是马上调用方法，而是设定一个未来时间点再来执行，延迟作业仅执行一次

```
var jobId = BackgroundJob.Schedule(
    () => Console.WriteLine("Delayed!"),
    TimeSpan.FromDays(7));
```

## 3. Recurring Jobs

重复作业按照指定的CRON 计划多次触发。

```
RecurringJob.AddOrUpdate(
     "myrecurringjob",
    () => Console.WriteLine("Recurring!"),
    Cron.Daily);
```

# 四、在项目中使用

## 1.Enqueue使用

我们在service层的AddPersonAsync中写入

```
public async Task<PeopleCreatedEvent> AddPersonAsync(CreatePeopleCommand command, CancellationToken cancellationToken)
{
BackgroundJob.Enqueue<IPersonDataProvider>(x => x.CreatAsync(new Person()
{
    Id = 50
}, cancellationToken));
```

使用 BackgroundJob.Enqueue<IPersonDataProvider> 方法创建一个后台任务，该任务将会调用实现了 IPersonDataProvider 接口的对象的方法。

将一个新的 Person 对象作为参数传递给 CreateAsync 方法，对象的 Id 属性被设置为 50。

send一下

![image.png](https://upload-images.jianshu.io/upload_images/29177961-b6d05497b660716a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

启动程序后：

![image.png](https://upload-images.jianshu.io/upload_images/29177961-c227e9b59c4fb663.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![image.png](https://upload-images.jianshu.io/upload_images/29177961-82117bc2fa7e6a0b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们可以看到，数据库里也更新了：

![image.png](https://upload-images.jianshu.io/upload_images/29177961-bb84fea68fa15214.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 2.Schedule使用

在service层的UpdatePersonAsync方法中写入：

```
BackgroundJob.Schedule(() => _personDataProvider.UpdatePersonAsync(person, CancellationToken.None), TimeSpan.FromMinutes(1));
```

使用 BackgroundJob.Schedule 方调用 _personDataProvider 对象的 UpdatePersonAsync 方法。

将一个 person 对象作为参数传递给 UpdatePersonAsync 方法，该方法将会在任务执行时使用该对象来更新数据存储。

使用 TimeSpan.FromMinutes(1) 方法指定任务的执行时间，该任务将会在一分钟后执行。

再次send一下：

![image.png](https://upload-images.jianshu.io/upload_images/29177961-93b06b0f96ce9427.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

程序启动后：

![image.png](https://upload-images.jianshu.io/upload_images/29177961-45cef9e809b2bc14.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

一分钟后，我们再次查看数据库，可以看见刚才id为50的数据，firstname和last name已经被更改了

![image.png](https://upload-images.jianshu.io/upload_images/29177961-b84f654df067c79d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 3.AddOrUpdate

我们在service层的AddPersonAsync中写入

```
RecurringJob.AddOrUpdate<IPersonDataProvider>(
"createPersonRecurringJob",
x => x.CreatAsync(new Person()
{
    Id = 664
}, cancellationToken),
"0 13 11 29 7 *", new RecurringJobOptions()
{
    TimeZone = TimeZoneInfo.FindSystemTimeZoneById("China Standard Time")
});
```

启动后：我么可以看到周期性作业里就有这条要执行的

![image.png](https://upload-images.jianshu.io/upload_images/29177961-33f2663696e00eae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

预定的时间过了后，我们打开数据库就能看见ID为444的数据

![image.png](https://upload-images.jianshu.io/upload_images/29177961-826b18357226f8b9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 五、有关于Cron

基本结构是这样滴：

* Second Minute Hour Day Month Week Year

举一个最常见的情况，如果我们想要表示 每天0点，就可以这么写 Cron 表达式:  0 0 0 * * ?

Day 和 Month 都填的是星号（*），这个比较好理解，代表全匹配所有的天和月份，但是 Week 这里写的是问号（？），含义是什么呢？
这是因为 Week 和 Day 存在互斥关系，不可以同时设定规则，一旦有一方设定了，另一方就不能设定，这时候就使用问号来代表一种 “随便什么都可以” 的意思。

其实我们可以在这里生成cron

https://bradymholt.github.io/cron-expression-descriptor/?locale=zh-CN&expression=0%2023%20?%20*%20MON-FRI

使用效果如图所示：

![image.png](https://upload-images.jianshu.io/upload_images/29177961-0934a1bdcb30505f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)