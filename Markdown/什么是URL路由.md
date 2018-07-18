## 什么是URL路由

**URL路由**（或**路由**）功能负责将传入请求映射到路由处理程序。通常，处理程序是一个动作。

**路由**描述了URL路径应如何映射到操作，并且它们**不区分大小写**。ASP.NET Core使用*Routing中间件*将传入请求的URL与预定义的路由进行匹配; 然后，有效的URL将映射到目标操作。结果，将触发目标操作。

请注意，在本课中使用**URL**时，我们通常会忽略完整URL的协议和主机名部分。也就是说，对于完整的URL，`http://localhost:5000/product/update/101`我们只考虑其中的一部分`product/update/101`。

ASP.NET Core支持两种类型的路由：

- **常规路由** - 常规路由通过使用约定来定义路由。在日常通信中，约定，URL模板，路由模板是可互换的名称。典型的惯例是`"{controller=Home}/{action=Index}/{id?}"`。
- **属性路由** - 属性路由使用一组属性将操作直接映射到路径模板。例如，如果修改了控制器类`[Route("Product")]`，则所有以*Product*开头的URL 都将导航到此控制器。如果修改此控制器中的操作`[Route("Create")]`，则`product/create`只会通过此操作处理URL 。

使用ASP.NET Core，我们可以编写Web应用程序和Web API应用程序。**要编写Web API应用程序，只能使用属性路由**。要编写Web应用程序，您可以使用传统路由或属性路由。我们从不将这两种路由一起使用。

由于大多数ASP.NET Core Web应用程序使用传统路由，因此在本课程中我们将重点介绍传统路由。[使用ASP.NET构建Web API](https://www.edx.org/course/build-web-apis-using-asp-net-microsoft-dev247x)课程中将介绍属性路由。

## 常规路由

创建ASP.NET Core应用程序后，您将找到`Startup.cs`项目中指定的文件。如果打开此文件，则可以看到名为的类`Startup`。当Web应用程序启动时，将调用此类的方法，并在此处发生许多关键配置选项，包括传统的路由创建。

在`Configure`方法的正文中，您可以看到以下代码：

```
app.UseMvc(routes => {
    routes.MapRoute(
        name: "default",
        template: "{controller=Home}/{action=Index}/{id?}");
});
```

这段代码做了两件事：

1. 告诉Web应用程序加载MVC中间件。如果没有这个，应用程序根本就不是MVC应用程序。
2. 添加一个命名`default`为其传统路由系统的路由。

## 路由模板

字符串`"{controller=Home}/{action=Index}/{id?}"`是默认路由的**路由模板**。此模板指导路由系统将传入的HTTP请求映射到目标控制器和操作。

路由模板的“语法”非常简单，下面是如何处理您可能遇到的大多数情况的摘要：

1. 路由模板中只有两种内容 - *路由参数*和*文字字符串*

- 例如`{controller}`，路径**参数**是花括号项目，`{action}`和`{id}`
- **文字字符串**是除路由参数之外的所有内容。例如，在模板`search/{year}/{file}.{ext}`的`search/`，在`/`之间`{year}`和`{file}`，和`.`之间`{file}`，并`{ext}`全部文字字符串。
- 两个路由参数必须用文字字符串分隔，否则在应用程序启动时会出现异常。

1. 路由参数有一些约束和装饰器：

- 在`{controller}`和`{action}`两个特殊的参数，它们的值会相应地映射到目标控制器的名称和目标行动的名字。
- 将`{controller}`参数映射到目标控制器类时`Controller`，将忽略类名称的后缀（如果存在）。例如，如果`{controller}`参数获取值`Product`，则它匹配类`ProductController`，`Product`修改`[Controller]`的类和类`Product`继承`Microsoft.AspNetCore.Mvc.Controller`。
- 除了`{controller}`和`{action}`其他参数将被映射到系统默认的目标操作的参数。
- 甲`?`后缀表示所述参数可以被省略，例如`{id?}`
- 路由参数可以具有默认值，例如`Home`in `{controller=Home}`和`Index`in`{action=Index}`
- 您可以通过添加约束来修改route参数。例如，`{id:int}`将参数限制为仅接受整数值，并`{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}`要求参数的值`{ssn}`与正则表达式匹配。例如，您可以修改具有多个约束的参数`{password:minlength(6):maxlength(20)}`。

基于上面的描述，让我们仔细看看模板`{controller=Home}/{action=Index}/{id?}`：

| URL                                          | Intention                                                    | Is Valid | Comments                                                     | Maps To                                              |
| -------------------------------------------- | ------------------------------------------------------------ | -------- | ------------------------------------------------------------ | ---------------------------------------------------- |
| http://localhost:5000                        | Access the *home* of the website                             | Yes      | Controller name, action name and ID parameter are all omitted | `HomeController`'s `Index()`method                   |
| http://localhost:5000/                       | Access the *root* of the website                             | Yes      | Controller name, action name and ID parameter are all omitted | `HomeController`'s `Index()`method                   |
| http://localhost:5000/home                   | Access the *home* of the website                             | Yes      | Action name and ID parameter are omitted                     | `HomeController`'s `Index()`method                   |
| http://localhost:5000/home/index             | Access the `home/index` URL                                  | Yes      | Only the ID parameter is omitted                             | `HomeController`'s `Index()`method                   |
| http://localhost:5000/home/index/101         | Access the `home/index` URL with additional ID information   | Yes      | All parameters get value assigned                            | `HomeController`'s `Index(int id)` method            |
| http://localhost:5000/home/index?id=101      | Access the `home/index` URL with additional ID information   | Yes      | ID is passing through query string                           | `HomeController`'s `Index(int id)` method            |
| http://localhost:5000/product                | Show me some product information                             | Yes      | Action name and ID parameter are omitted                     | `ProductController`'s `Index`method                  |
| http://localhost:5000/product/showavailable  | Show me all available products                               | Yes      | We don't need ID here, so it's omitted                       | `ProductController`'s `ShowAvailable` method         |
| http://localhost:5000/product/showdetail/101 | Show me the detail information of product whose ID is 101    | Yes      | All parameters get value assigned                            | `ProductController`'s `ShowDetail(int id)`method     |
| http://localhost:5000/product/search?kw=bike | Search for bikes                                             | Yes      | We don't need ID here, but the target method must have a parameter named `kw` | `ProductController`'s `Search(string kw)`method      |
| http://localhost:5000/101                    | Omit the controller and action name, call `HomeController`'s `Index(int id)` method with id=101 | Yes      | You will get a `404 Not Found` error                         | `101Controller`'s `Index`action, which doesn't exist |
| http://localhost:5000/Index/101              | Omit the controller name, call `HomeController`'s `Index(int id)` method with id=101 | Yes      | You will get a `404 Not Found` error                         | `IndexController`'s `101`action, which doesn't exist |
| http://localhost:5000///101                  | Omit the controller and action name, call `HomeController`'s `Index(int id)` method with id=101 | No       | It is not a valid HTTP URL at all                            | –                                                    |
| http://localhost:5000//Index/101             | Omit the controller name, call `HomeController`'s `Index(int id)` method with id=101 | No       | It is not a valid HTTP URL at all                            | –                                                    |

注意：当用户使用Web浏览器与Web应用程序（在服务器上运行）进行交互时，他们的大多数操作都是单击超链接，提交表单或使用地址栏进行导航。地址栏和超链接只能启动HTTP GET请求，HTML表单可以启动HTTP POST或HTTP GET请求。这意味着，在Web应用程序中，我们将仅使用`[HttpGet]`和`[HttpPost]`属性来修改操作。如果您在Web应用程序中使用AJAX技术，请始终将服务器端逻辑与Web API应用程序分开。 