## #EF6数据库初始化Seed数据和使用事务

#### User实体

```c#
	public partial class User
    {
        [Key]
        public int Uid { get; set; }
        public string NickName { get; set; }
        public string RealName { get; set; }
        public int Age { get; set; }
        public string Gender { get; set; }
        public string Phone { get; set; }
        public string Email { get; set; }
        public int AddressId { get; set; }
        public string LoginName { get; set; }
        public string PassWord { get; set; }
        public DateTime RegisterTime { get; set; }
        public DateTime? LastLoginTime { get; set; }
    }
```

#### DbContext上下文对象

```c#
	public class SqlDbContext : DbContext
    {
        public SqlDbContext() : base("name=SqlDbContext")
        {
            //指定自定义初始化对象
            Database.SetInitializer(new DBInitializer());
        }
        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
            //取消表名复数
            modelBuilder.Conventions.Remove<PluralizingTableNameConvention>();
        }

        public virtual DbSet<User> User { get; set; }
    }
```

#### 数据库连接字符串

```xml
<add name="SqlDbContext" connectionString="data source=xxx.xxx.xxx.xxx;initial catalog=xxx;persist security info=True;user id=sa;password=xxxx;MultipleActiveResultSets=True;App=EntityFramework" providerName="System.Data.SqlClient"/>
```

#### App_Start使用初始化

```c#
 	protected void Application_Start()
        {
        	//进行初始化
            using (SqlDbContext db = new SqlDbContext())
            {
                db.Database.Initialize(false);
            }
            GlobalConfiguration.Configure(WebApiConfig.Register);
            GlobalConfiguration.Configuration.Formatters.XmlFormatter.SupportedMediaTypes.Clear();
        }
```

#### 使用事务

```c#
 public class UserController : ApiController
    {
        private SqlDbContext db = new SqlDbContext();
        
        public IHttpActionResult DeleteUser(int id)
        {
            if (!ModelState.IsValid)
            {
                return BadRequest(ModelState);
            }
            try
            {
                User user = db.User.Find(id);
                if (user == null)
                {
                    return BadRequest("不存在该对象");
                }
                using (var tran = new TransactionScope())
                {
                    db.User.Remove(user);
                    db.SaveChanges();
                    Random random = new Random();
                    int i = random.Next(0, 99);
                    if (i >= 44)
                    {
                        tran.Complete();
                        return Ok();
                    }
                }
                return BadRequest("删除失败");
            }
            catch (Exception e)
            {
                return BadRequest("删除出错！");
            }
        }
        
        //.NET FrameWork 4.5.1及以上，允许异步执行事务
        public IHttpActionResult Delete ([FromUri]int id)
        {
            User user = db.User.Find(id);
            if (user == null)
            {
                return BadRequest("不存在该对象");
            }
            using (var scope = new TransactionScope(TransactionScopeAsyncFlowOption.Enabled))
            {
                db.User.Remove(user);
                db.SaveChanges();
                Random random = new Random();
                int i = random.Next(0, 99);
                if (i >= 84)
                {
                    scope.Complete();
                    return Ok();
                }

            }
            return BadRequest();
        }
	}
```