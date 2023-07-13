# 一、ORM

ORM 是一种将对象模型和关系型数据库之间的映射关系进行自动化处理的技术。开发人员在使用 ORM 框架时，可以使用面向对象的方式定义实体类，然后通过 ORM 框架将这些实体类映射到数据库中的表和记录，从而实现对数据库的访问和操作。ORM 框架还可以自动生成 SQL 语句，从而简化数据访问的开发工作。

* ORM框架是连接数据库的桥梁，只要提供了持久化类与表的映射关系，ORM框架在运行时就能参照映射文件的信息，把对象持久化到数据库中。
* 主要目的：操作实体类就相当于操作数据库表
* 建立两个映射关系
  实体类和表的映射关系
  实体类中属性和表中字段的映射关系

举个例子，如果有一个 Customers 表，包含 Id、Name、Age 和 Address 四个字段，开发人员可以使用 ORM 框架定义一个名为 Customer 的实体类，该实体类包含 Id、Name、Age 和 Address 四个属性。在运行时，ORM 框架会自动将 Customer 实体类映射到 Customers 表中的记录，从而使得开发人员可以使用面向对象的方式来访问和操作 Customers 表中的数据。

# 二、EF Core是什么

Entity Framework Core（EF Core）是一种轻量级、跨平台的 ORM（对象关系映射）框架，用于将实体类和数据库之间的映射关系进行描述和管理。

EF Core 是 .NET Core 平台中的一个重要组件，可以在多个平台（例如 Windows、Linux、macOS 等）上运行，支持多种类型的数据库（例如 SQL Server、MySQL、SQLite 等），并提供了一组 API，用于进行数据的存储、检索和操作。 

# 三、EF Core 的主要作用包括：

1.提供一致的 API：EF Core 提供了一致的 API，用于进行数据的存储、检索和操作，无论使用哪种数据库都可以使用相同的 API 进行操作，简化了开发人员的工作。

2.简化数据访问：EF Core 可以将实体类和数据库之间的映射关系进行描述和管理，开发人员只需要关注实体类的定义和操作，而无需关心数据库的操作细节，从而简化了数据访问的工作。

3.提高代码质量：EF Core 可以通过编译时类型安全的方式进行查询，避免了常见的 SQL 注入等安全问题，并提供了一些高级功能，例如延迟加载、事务管理、并发处理、缓存机制等，可以提高代码的质量和可维护性。

4.跨平台支持：EF Core 可以在多个平台上运行，包括 Windows、Linux、macOS 等，可以使用命令行工具进行开发和管理，例如使用 Package Manager Console 或者 .NET CLI。

使用 EF Core 进行开发时，开发人员只需要定义好实体类的结构和属性，然后使用 EF Core 提供的 API 进行数据的存储、检索和操作

# 四、创建示例项目

在Domain包里创建一个Person实体类

```
//这是数据库中的people表
[Table("people")]
public class Person
{
    //将ID设为主键
    [Key]
    //写数据注解，用于将该属性映射到数据库表中的一个特定列
    [Column("id")]
    public int Id { get; set; }
        
    [Column("first_name")]
    public string FirstName { get; set; }
        
    [Column("last_name")]
    public string LastName { get; set; }
        
    [Column("create_at")]
    public DateTime CreatedAt { get; set; }
}
```

创建数据库上下文，把数据库People表映射到Person类中

```
public class MyDbContext : DbContext
{
    public DbSet<Person> People { get; set; }

    public MyDbContext(DbContextOptions<MyDbContext> options, DbSet<Person> people) : base(options)
    {
        People = people;
    }
}
```

数据表：

```
CREATE TABLE `people`  (
  `Id` int NOT NULL AUTO_INCREMENT,
  `FirstName` varchar(50) NULL,
  `LastName` varchar(50) NULL,
  `CreatedAt` datetime NULL,
  PRIMARY KEY (`Id`)
);
```

配置appsettings.json

```
  "ConnectionStrings": {
    "MySQL": "server=localhost;userid=root;password=xxxxx;database=test;"
  }
```

Startup.cs注册

在Startup.cs注册MySQL数据库上下文服务，如下：

```
   public Startup(IConfiguration configuration)
    {
        Configuration = configuration;
    }
    public IConfiguration Configuration { get; }
    
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc();
        services.AddControllers();
        //用GetConnectionString查找注册中的"ConnectionStrings":"MySQL"，后面的是MySql的版本号
        services.AddDbContext<MyDbContext>(options => options.UseMySql(Configuration.GetConnectionString("MySQL"), new MySqlServerVersion(new Version(5, 7, 0))));
    
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.UseRouting();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapControllers();
        });
    }
```

创一个PeopleController的控制器

```
    [ApiController]
    [Route("api/[controller]/[action]")]
    public class PeopleController : ControllerBase
    {
        private readonly IPersonService _personService;

        public PeopleController(IPersonService personService)
        {
            _personService = personService;
        }

        [HttpGet]
        public IActionResult Create()
        {
            //调用PersonService中的AddPerson方法
            return Ok(_personService.AddPerson());
        }

        [HttpGet]
        public IActionResult GetById(int id)
        {
            //调用PersonService中的GetPersonById方法
            return Ok(_personService.GetPersonById(id));
        }

        [HttpGet]
        public IActionResult GetAll()
        {
            //调用PersonService中的.GetAllPersons方法
            return Ok(_personService.GetAllPersons());
        }
    }
```

创一个PersonService的业务逻辑层

```
    public interface IPersonService
    {
        //接口类中三个不同的方法
        string AddPerson();
        List<Person> GetAllPersons();
        Person GetPersonById(int id);
    }

    public class PersonService : IPersonService
    {
        private readonly IPersonDataProvider _personDataProvider;
        
        //把PersonDataProvider注入到PersonService
        public PersonService(IPersonDataProvider personDataProvider)
        {
            _personDataProvider = personDataProvider;
        }

        public string AddPerson()
        {
            //调用PersonDataProvider中的Creat方法
            return _personDataProvider.Creat() > 0 ? "数据写入成功" : "数据写入失败";//执行Creat方法的逻辑
        }

        public List<Person> GetAllPersons()
        {
            //调用PersonDataProvider中的GetPersonAll方法
            return _personDataProvider.GetPersonAll();
        }

        public Person GetPersonById(int id)
        {
            //调用PersonDataProvider中的GetPersonById方法
            return _personDataProvider.GetPersonById(id);
        }
    }
```

创建PersonDataProvider数据访问层

```
public interface IPersonDataProvider
{
    //IPersonDataProvider接口中的三种方法
    int Creat();
    
    List<Person> GetPersonAll();
    
    Person GetPersonById(int id);
} 

public abstract class PersonDataProvider : IPersonDataProvider
{
    private readonly MyDbContext _dbContext;

    protected PersonDataProvider(MyDbContext dbContext)
    {
        //通过在 PersonDataProvider 类中注入 MyDbContext 类的实例，我们可以在该类中使用 _dbContext 来访问数据库
        _dbContext = dbContext;
    }

    public int Creat()
    {
        //创建
        var person = new Person
        {
            FirstName = "Rector",
            LastName = "Liu",
            CreatedAt = DateTime.Now
        };

        _dbContext.People.Add(person);

        return _dbContext.SaveChanges();
    }

    public Person GetPersonById(int id)
    {
        //读取指定Id的数据
        return _dbContext.People.Find(id) ?? new Person();
    }

    public List<Person> GetPersonAll()
    {
        //读取所有
        return _dbContext.People.ToList();
    }
```