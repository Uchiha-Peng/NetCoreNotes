#### .NET Core 笔记

##### 1. .NET Core生成带身份验证的MVC模板,并使用SQLite 

```c#
/*进入相应的目录*/

cd /D D:\NetCore

/*创建模板,-au Individual表示使用身份验证,-uld是使用Local DB,如果参数是False,则使用SQLite,如果为True,则使用SQL Server LocalDB*/

dotnet new mvc -au Individual -uld false --name SQLiteCore
```
##### 2.  .使用依赖注入读取appsettings.json里的连接字符串,并创建EntityFrameWorkCore数据库上下文对象DbContext 
```json
{
  "Logging": {
    "IncludeScopes": false,
    "Debug": {
      "LogLevel": {
        "Default": "Warning"
      }
    },
    "Console": {
      "LogLevel": {
        "Default": "Warning"
      }
    }
  },
  "ConnectionStrings": {
    "SQLiteContext": "DataSource=ShopDB.db"
  }
}
```
在**Startup.cs**的**ConfigureServices**方法中要注意一下几点:

```c#
 public void ConfigureServices(IServiceCollection services)
        {
            //重点一：
            //services.Configure<SQLiteDbContext> 此方法作用是为非EF Core上下文对象从appsettings文件中读取配置字符串
            //services.AddDbContext<SQLiteDbContext> 此方法作用是专门为EF Core上下文对象从appsettings文件中读取配置字符串的

            //重点二：
            //Configuration.GetSection("ConnectionStrings") 此方法作用是从appsettings文件中读取名为"ConnectionStrings"的这一节点的所有数据，如这里如果需要读取数据库连接字符串，则需要如下步骤
            var aa = this.Configuration.GetSection("ConnectionStrings");
            var cc = aa["SQLiteContext"];
            //Configuration.GetConnectionString("SQLiteContext") 此方法的作用是从appsettings文件中读取名为"ConnectionStrings"的连接字符串，但是要求"ConnectionStrings"的父级节点的名称必须为"ConnectionStrings",否则将一直为Null，使用步骤如下
            var bb = this.Configuration.GetConnectionString("SQLiteContext");

            services.AddDbContext<SQLiteDbContext>(options
                => options.UseSqlite(Configuration.GetConnectionString("SQLiteContext")));
     
            //services.Configure<SQLiteDbContext>(this.Configuration.GetSection("SQLiteConn"));
            services.AddMvc();
        }
```

DbContext对象内容如下:

```c#
 public class SQLiteDbContext : DbContext
    {
        public SQLiteDbContext(DbContextOptions<SQLiteDbContext> options) : base(options)
        {

        }
        
        public virtual DbSet<Address> Address { get; set; }
        public virtual DbSet<Brand> Brand { get; set; }
        public virtual DbSet<Category> Category { get; set; }
        public virtual DbSet<Order> Order { get; set; }
        public virtual DbSet<Orderdetail> Orderdetail { get; set; }
        public virtual DbSet<Product> Product { get; set; }
        public virtual DbSet<Shopcart> Shopcart { get; set; }
        public virtual DbSet<Status> Status { get; set; }
        public virtual DbSet<User> User { get; set; }
    }
```

在Controller中使用注入如下:

```c#
private readonly SQLiteDbContext db;

        public ProductsController(SQLiteDbContext context)
        {
            db = context;
        }

        public ActionResult Get()
        {
            var list = db.Product;
            if (list.Count() > 0)
            {
                return Ok(list);
            }
            return NotFound();
        }
```

