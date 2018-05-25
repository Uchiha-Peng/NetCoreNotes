#### 使用Cookie-based认证

1. 引用命名空间**using Microsoft.AspNetCore.Authorization**，添加**[Authorize]**标签

```c#
[Authorize]
        public IActionResult Index()
        {
            return View(list);
        }    
```

2. 在Starup.cs中使用注册服务和MiddleWare中间件

```c#
  public void ConfigureServices(IServiceCollection services)
        {
      //注册认证服务
      services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme).AddCookie();
      services.AddMvc();
        }
        
  public void Configure(IApplicationBuilder app, IHostingEnvironment env)
        {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
            }
            app.UseStaticFiles();
            //注册MiddleWare中间件
            app.UseAuthentication();
            app.UseMvc(routes =>
            {
                routes.MapRoute(
                    name: "default",
                    template: "{controller=Home}/{action=Index}/{id?}");
            });
        }
```

3. 直接访问Index，会出错，并跳转至Account/Login，添加AccountController，并模拟登录和注销

```c#
//引入以下命名空间
//using Microsoft.AspNetCore.Authentication;
//using Microsoft.AspNetCore.Authentication.Cookies;
//using System.Security.Claims;

public IActionResult Login()
        {
            var claims = new List<Claim> { new Claim(ClaimTypes.Name, "Uchiha"), new Claim(ClaimTypes.Role, "admin") };

            var claimInentity = new ClaimsIdentity(claims, CookieAuthenticationDefaults.AuthenticationScheme);
            HttpContext.SignInAsync(CookieAuthenticationDefaults.AuthenticationScheme, new ClaimsPrincipal(claimInentity));
            return View();
        }

        public IActionResult LoginOut()
        {
            HttpContext.SignOutAsync(CookieAuthenticationDefaults.AuthenticationScheme);

            return View();
        }
```

