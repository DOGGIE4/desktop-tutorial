
# 一、Dbup是什么？

DbUp跟踪已运行的 SQL 脚本，并运行使数据库保持最新状态所需的更改脚本。可以将数据库升级的过程自动化，避免手动执行 SQL 脚本的繁琐和出错风险。

如果不使用Dbup我们就需要手动执行SQL脚本，创建新的表，并且要在应用程序中添加代码来使用新的表，
例如添加一个名为 "User" 的数据模型类，用于映射用户表的结构。同时，修改数据访问层代码，以便使用新的数据模型类。

而我们使用Dbup就可以实现自动化的数据库迁移和部署过程。

#  二、配置

## 1.先引入这三个包：

![image.png](https://upload-images.jianshu.io/upload_images/29177961-3db68ce8738ecc41.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 2. 新建一个类来进行配置

```
public void Run()
{
    //检查数据库是否存在，如果不存在，则会自动创建一个新的数据库
    EnsureDatabase.For.MySqlDatabase(_connectionString);
    
    //创建一个 MySql 数据库迁移引擎对象。
    var upgradeEngine = DeployChanges.To.MySqlDatabase(_connectionString)
        .WithScriptsEmbeddedInAssembly(typeof(DbUpRunner).Assembly)
        .WithTransaction()  //开启事务
        .LogToAutodetectedLog()  //跟踪日志
        .LogToConsole()    //日志打印到控制台
        .Build();

    var result = upgradeEngine.PerformUpgrade();

    if (!result.Successful)
        throw result.Error;
    {
        Console.ForegroundColor = ConsoleColor.Green;
        Console.WriteLine("Success!");
        Console.ResetColor();
    }
}
```

配置好后，你可以

获取将要执行的脚本 ( GetScriptsToExecute())
获取已执行的脚本 ( GetExecutedScripts())
检查是否需要升级 ( IsUpgradeRequired())
为任何新的迁移脚本创建版本记录而不执行它们 ( MarkAsExecuted)
对于使开发环境与自动化环境同步很有用
尝试连接数据库 ( TryConnect())
执行数据库升级 ( PerformUpgrade())
日志脚本输出 ( LogScriptOutput())

# 三、使用

## 1.首先，我们要新建一个数据脚本放在我们的项目中：

![image.png](https://upload-images.jianshu.io/upload_images/29177961-8dfe244107a0a467.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Script0001_initial_tables.sql中：

```
create table if not exists user_question
(
    id int auto_increment
    primary key,
    created_at int not null,
    question varchar(512) charset utf8 not null,
    rasa_predicted_qid int not null,
    rasa_confidence decimal(8,3) null,
    anyq_predicted_qid int not null,
    anyq_confidence decimal(8,3) null,
    correct_qid int not null,
    model3_predicted_qid int default 0 null,
    model3_confidence decimal(8,3) default 0.000 null,
    status int default 0 null,
    remark varchar(512) charset utf8 null,
    ask_by varchar(128) charset utf8 null,
    constraint idx_question
    unique (question)
) charset=utf8mb4;
```

## 2.要记得，在.csproj中，将位于项目目录下的 Dbup\Scripts_2023\Script0001_initial_tables.sql 文件添加到项目中

```
<ItemGroup>
    <EmbeddedResource Include="Dbup\Scripts_2023\Script0001_initial_tables.sql" />
</ItemGroup>
```

## 3.在program中调用我们创建的DbUpRunner类中的run方法

```
 new DbUpRunner("server=localhost;userid=root;password=123456;database=smart_faq;").Run();
```

表示创建一个 DbUpRunner 对象，并将连接字符串 "server=localhost;userid=root;password=123456;database=smart_faq;" 作为参数传递给 DbUpRunner 的构造函数。

## 4.在DbUpRunner中：

```
public class DbUpRunner
{
    private readonly string _connectionString;//connectionString就是咱们传进来的连接数据库的字符串

    public DbUpRunner(string connectionString)
    {
        _connectionString = connectionString;
    }

    public void Run()
    {
        EnsureDatabase.For.MySqlDatabase(_connectionString);

        var upgradeEngine = DeployChanges.To.MySqlDatabase(_connectionString)
            .WithScriptsEmbeddedInAssembly(typeof(DbUpRunner).Assembly)
            .WithTransaction()  //生成事务
            .LogToAutodetectedLog()  //生成日志
            .LogToConsole()  //打印日志
            .Build();

        var result = upgradeEngine.PerformUpgrade();  //执行数据库升级

        //判断是否执行成功
        if (!result.Successful)
            throw result.Error;
        {
            Console.ForegroundColor = ConsoleColor.Green;
            Console.WriteLine("Success!");
            Console.ResetColor();
        }
    }
}
```

WithScriptsEmbeddedInAssembly(typeof(DbUpRunner).Assembly)表示获取 DbUpRunner 类型所在的程序集，这个方法会在这个程序集中查找嵌入的 SQL 脚本文件，并将其作为数据库迁移脚本执行，也就是说，它会扫描到咱们建的Script0001_initial_tables.sql这个文件，并执行里面的代码。

## 5.运行成功后，数据库就会生成user_question的表，如下所示：

![image.png](https://upload-images.jianshu.io/upload_images/29177961-2a5b882782cddf77.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

schemaversion也会有记录：

![image.png](https://upload-images.jianshu.io/upload_images/29177961-0333c16857dee450.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 四、补充

🙋提问！ 如果我想要删除数据库脚本中的某些字段，该怎么办呢？🤔
Script0001_initial_tables.sql中修改成这样：

```
create table if not exists user_question
(
    id int auto_increment
    primary key,
    created_at int not null,
    question varchar(512) charset utf8 not null,
    constraint idx_question
    unique (question)
)
charset=utf8mb4;
```

首先我们需要把之前数据库中创建好的user_question表删掉，schemaversion中的记录也要删除，再去执行，我们的数据库就会变成这样：
![image.png](https://upload-images.jianshu.io/upload_images/29177961-d77aa126bf969d0b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

schemaversion中的记录也会变化：
![image.png](https://upload-images.jianshu.io/upload_images/29177961-fe64e99f0dfc4fba.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)