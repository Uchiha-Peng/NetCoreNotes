## 内置HtmlHelper

在理解了HTML帮助程序的关键概念之后，让我们看一下ASP.NET核心框架附带的HTML帮助程序，它们也称为**内置HTML帮助程序**。

在这里，我们将仅介绍最常用的内置HTML帮助程序。由于HTML帮助器（方法）可能有十多个重载，我们将仅为主流和常用的示例创建示例。因此，这个主题很长，但对于理解这些内置帮助程序非常重要。

将要介绍的HTML助手包括：

| 名称                         | 描述                     | 生成的HTML元素             | 是表单元素 |
| ---------------------------- | ------------------------ | -------------------------- | ---------- |
| ActionLink                   | 生成超链接               | `<a/>`                     | 没有       |
| BeginForm                    | 生成HTML表单             | `<form></form>`            | 是         |
| Hidden/HiddenFor             | 生成隐藏的名称 - 值对    | `<input type="hidden"/>`   | 是         |
| TextBox/TextBoxFor           | 生成一个文本框           | `<input type="text"/>`     | 是         |
| Password/PasswordFor         | 生成密码的文本框         | `<input type="password"/>` | 是         |
| TextArea/TextAreaFor         | 生成多行文本区域         | `<textarea/>`              | 是         |
| RadioButton/RadioButtonFor   | 生成没有文本的单选按钮   | `<input type="radio"/>`    | 是         |
| CheckBox/CheckBoxFor         | 生成没有文本的复选框     | `<input type="checkbox"/>` | 是         |
| DropDownList DropDownListFor | 生成下拉列表             | `<select>` 和 `<option>`   | 是         |
| ListBox/ListBoxFor           | 生成一个列表框           | `<select>` 和 `<option>`   | 是         |
| Label/LabelFor               | 为另一个输入元素生成标签 | `<label/>`                 | 是         |

#### 准备模型和控制器

在演示每个HTML帮助程序之前，让我们准备模型和控制器。我们将在本主题的所有示例中共享模型和控制器。

以下是我们使用的域模型：

```
namespace MyWebApp.Models {
    public class Product {
        public int ID { get; set; }
        public string Name { get; set; }
        public double Price { get; set; }
        public string Description { get; set; }

        public Product(int id, string name, double price) {
            this.ID = id; this.Name = name; this.Price = price;
        }
    }
}
```

这是控制器及其动作：

```
namespace MyWebApp.Controllers {
    public class ProductController : Controller {

        // Edit single product
        public IActionResult Edit() {
            var product = new Product(901, "Rocket", 99.99);
            return View(product);
        }

        // Show a product list
        public IActionResult Show() {
            var products = new List<Product>();
            products.Add(new Product(101, "Apple", 19.99));
            products.Add(new Product(201, "Bike", 29.99));
            products.Add(new Product(301, "Couch", 39.99));
            return View(products);
        }
    }
}
```

对于`Edit`和`Show`操作，我们在文件夹中创建`Edit.cshtml`和`Show.cshtml`查看页面文件`~/Views/Product`。这两个视图文件是我们演示内置HTML帮助程序的游乐场。他们的代码框架如下所示：

```
@model MyWebApp.Models.Product
<html>
<body>
    <!-- Demo code for single product object -->
</body>
</html>
```

和：

```
@model List<MyWebApp.Models.Product>
<html>
<body>
    <!-- Demo code for product collections -->
</body>
</html>
```

#### ActionLink的

`ActionLink`HTML帮助器方法的功能是用于生成HTML超链接。调用它的原因`ActionLink`是您可以为超链接设置目标控制器和操作。

修改如下代码`Edit.cshtml`：

```
@model MyWebApp.Models.Product
<html>
<body>
    @Html.ActionLink("Hyperlink 01", "TargetAction")<br />
    @Html.ActionLink("Hyperlink 02", "TargetAction", new { id = Model.ID })<br />
    @Html.ActionLink("Hyperlink 03", "TargetAction", new { name = Model.Name })<br />
    @Html.ActionLink("Hyperlink 04", "TargetAction", "TargetController", new { id = Model.ID }, new { style = "color:red" })<br />
</body>
</html>
```

运行应用程序，您将看到：

查看HTML源代码，您应该看到：

```
<html>
<body>
    <a href="/product/TargetAction">Hyperlink 01</a><br />
    <a href="/product/TargetAction/901">Hyperlink 02</a><br />
    <a href="/product/TargetAction?name=Rocket">Hyperlink 03</a><br />
    <a href="/TargetController/TargetAction/901" style="color:red">Hyperlink 04</a><br />
</body>
</html>
```

在视图中，我们调用了**三种**不同的重载`ActionLink`，它们是：

- `public static IHtmlContent ActionLink(this IHtmlHelper helper, string linkText, string actionName);`
- `public static IHtmlContent ActionLink(this IHtmlHelper helper, string linkText, string actionName, object routeValues);`
- `public static IHtmlContent ActionLink(this IHtmlHelper helper, string linkText, string actionName, string controllerName, object routeValues, object htmlAttributes);`

现在让我们关注参数：

- `this IHtmlHelper helper` 表示此方法是一种扩展方法
- `string linkText`让您有机会设置超链接的文本。文本可以是存储在视图模型中的静态字符串或值
- `string actionName` 是目标操作的名称，同样，该值可以是存储在视图模型中的静态字符串或值
- `string controllerName`是目标控制器的名称。如果重载没有此参数，则目标控制器将是此视图所属的控制器。正如您在Hyperlink 01和02中看到的那样
- `object routeValues`使您有机会设置URL路由值或查询字符串。通常我们`new { /*member-value pairs*/ }`为此参数指定一个匿名类型对象（）。请注意，如果对象成员的名称在路由URL模板中显示为路由参数，则其值将是URL的一部分（如`id = Model.ID`），或者它将是查询字符串中的名称 - 值对（如`name = Model.Name`）。比较Hyperlink 03和Hyperlink 04。
- `object htmlAttributes`使您有机会为生成的HTML元素设置属性值。我们还使用匿名类型对象来设置其值。请参阅超链接04.注意，如果要设置`class`属性的值，则不能只使用`class = "aClassName"`，因为它`class`是C＃中的关键字。你应该`@class = "aClassName"`用来解决这个问题。

这是一个温和的提示：当您使用HTML帮助程序方法时，很容易意外地选择错误的重载。要选择正确的重载，有时您必须将`null`值传递给某些参数以使参数与参数列表匹配。

#### BeginForm

众所周知，HTML页面向Web服务器发送数据的标准方式是提交表单。该`<form>`标签自1995年HTML版本2.0以来就已经存在于HTML中。其功能`BeginForm`是生成`<form>`元素。

有十多个重载`BeginForm`，但这三个是最受欢迎的：

- `public static MvcForm BeginForm(this IHtmlHelper htmlHelper);`
- `public static MvcForm BeginForm(this IHtmlHelper htmlHelper, string actionName, string controllerName);`
- `public static MvcForm BeginForm(this IHtmlHelper htmlHelper, string actionName, string controllerName, FormMethod method);`

在`string actionName`和`string controllerName`给你一个机会来设定目标控制器和行动。如果没有这两个参数，Razor引擎将使用当前视图的名称作为目标操作的名称，并使用当前视图的控制器作为目标控制器。通常，`FormMethod method`省略。默认的HTTP方法`<form>`是POST。

现在，让我们修改`Edit.cshtml`如下：

```
@model MyWebApp.Models.Product
<html>
<body>
    @using (Html.BeginForm("TargetAction", "TargetController")) {
        <input type="text" name="id" value="@Model.ID" /><br />
        <input type="text" name="name" value="@Model.Name" /><br />
        <input type="text" name="price" value="@Model.Price" /><br />
        <input type="submit" value="Submit" /><br />
    }
</body>
</html>
```

运行应用程序，您将看到：

页面源代码是：

```
<html>
<body>
    <form action="/TargetController/TargetAction" method="post">
        <input type="text" name="id" value="901" /><br />
        <input type="text" name="name" value="Rocket" /><br />
        <input type="text" name="price" value="99.99" /><br />
        <input type="submit" value="Submit" /><br />
    </form>
</body>
</html>
```

正如你可能会注意到，我们必须使用`BeginForm`同一个`using`声明。那是因为`using`语句会调用`EndForm`方法来关闭`<form>`元素。

该`action="/TargetController/TargetAction" method="post"`和`method="post"`反映了开发商给出的参数值。

在此示例`<input>`中，表单中的元素是静态的。在以下主题中，我们将讨论如何使用HTML帮助程序生成它们。

#### 隐藏/ HiddenFor

您可能已经知道，它`<input type="hidden">`拥有一个不可见的名称 - 值对。对于那些我们不希望用户修改但必须回发到Web服务器的值，我们使用这个HTML元素。例如，`ID`属性`Product`

让我们修改`Edit.cshtml`为：

```
@model MyWebApp.Models.Product
<html>
<body>
    @using (Html.BeginForm("TargetAction", "TargetController")) {
        @Html.Hidden("id", Model.ID)<br />
        @Html.TextBox("name", Model.Name)<br />
        @Html.TextBox("price", Model.Price)<br />
        <input type="submit" value="Submit" /><br />
    }
</body>
</html>
```

运行应用程序，您应该看到：

生成的HTML代码是：

```
<html>
<body>
    <form action="/TargetController/TargetAction" method="post">
        <input id="id" name="id" type="hidden" value="901" /><br />
        <input id="name" name="name" type="text" value="Rocket" /><br />
        <input id="price" name="price" type="text" value="99.99" /><br />
        <input type="submit" value="Submit" /><br />
    </form>
</body>
</html>
```

如我们所见，`@Html.Hidden("id", Model.ID)`表达式呈现为`<input id="id" name="id" type="hidden" value="901" />`。

我们完了吗？不，让我们考虑一个场景：开发人员将属性名称从更改`ID`为`ProductID`。很明显，`id`和`name`属性的值不会相应地改变（它仍然是`"id"`）。当此表单回发到目标操作时，如果参数类型为，则模型绑定将失败`Product`。

这就是`-For`HTML助手存在的原因。在这种情况下，更好的视图代码是：

```
@model MyWebApp.Models.Product
<html>
<body>
    @using (Html.BeginForm("TargetAction", "TargetController")) {
        @Html.HiddenFor(p => p.ID, Model.ID)<br />
        @Html.TextBoxFor(p => p.Name, Model.Name)<br />
        @Html.TextBoxFor(p => p.Price, Model.Price)<br />
        <input type="submit" value="Submit" /><br />
    }
</body>
</html>
```

使用`-For`HTML帮助程序的好处是，一旦属性名称发生更改，IDE将帮助开发人员更新属性名称，否则将出现编译时错误。

#### 文本框/ TextBoxFor

正如我们在过去的例子中看到，无论是`@Html.TextBox( "name", Model.Name)`和`@Html.TextBoxFor(p => p.Name, Model.Name)`产生相同的HTML代码`<input id="name" name="name" type="text" value="Rocket" />`。

#### 密码/ PasswordFor

这类似于`TextBox`和`TextBoxFor`，但它会生成`<input type="password" />`。所以下面的视图代码：

```
<html>
<body>
    @using (Html.BeginForm("TargetAction", "TargetController")) {
        @Html.TextBox("user", "Timothy")<br />
        @Html.Password("password", "123456")<br />
        <input type="submit" value="Login" /><br />
    }
</body>
</html>
```

将呈现给：

```
<html>
<body>
    <form action="/TargetController/TargetAction" method="post">
        <input id="user" name="user" type="text" value="Timothy" /><br />
        <input id="password" name="password" type="password" value="123456" /><br />
        <input type="submit" value="Login" /><br />
    </form>
</body>
</html>
```

它应该看起来像：

#### 文本区/ TextAreaFor

二者`TextArea`并`TextAreaFor`产生一个`<textarea>`元素。所以视图代码如下所示：

```
@model MyWebApp.Models.Product
<html>
<body>
    @using (Html.BeginForm("TargetAction", "TargetController")) {
        @Html.TextArea("description", Model.Description)<br />
        @Html.TextAreaFor(p => p.Description, Model.Description)<br />
        <input type="submit" value="Submit" /><br />
    }
</body>
</html>
```

并将呈现给：

```
<html>
<body>
    <form action="/TargetController/TargetAction" method="post">
        <textarea id="description" name="description"></textarea><br />
        <textarea id="Description" name="Description"></textarea><br />
        <input type="submit" value="Submit" /><br />
    </form>
</body>
</html>
```

它输出：

#### 单选按钮/ RadioButtonFor

生成元素的功能`RadioButton`和`RadioButtonFor`生成`<input type="radio"/>`元素。通常，他们使用集合。如果一组单选按钮共享相同的名称，则只能选择其中一个。

让我们修改代码`Show.cshtml`为：

```
@model List<MyWebApp.Models.Product>
<html>
<body>
    <h2>Single Select</h2>
    @using (Html.BeginForm("TargetAction", "TargetController")) {
        <table border="1">
            <tr><th>Buy</th><th>Name</th><th>Price</th></tr>
            @foreach (var p in Model) {
                <tr>
                    <td>@Html.RadioButton("selected", p.ID)</td>
                    <td>@p.Name</td>
                    <td>@p.Price</td>
                </tr>
            }
        </table><br />
        <input type="submit" value="Checkout" />
    }
</body>
</html>
```

运行您应该看到的应用程序：

生成的HTML内容为：

```
<html>
<body>
    <h2>Single Select</h2>
    <form action="/TargetController/TargetAction" method="post">
        <table border="1">
            <tr><th>Buy</th><th>Name</th><th>Price</th></tr>
            <tr>
                <td><input id="selected" name="selected" type="radio" value="101" /></td>
                <td>Apple</td>
                <td>19.99</td>
            </tr>
            <tr>
                <td><input id="selected" name="selected" type="radio" value="201" /></td>
                <td>Bike</td>
                <td>29.99</td>
            </tr>
            <tr>
                <td><input id="selected" name="selected" type="radio" value="301" /></td>
                <td>Couch</td>
                <td>39.99</td>
            </tr>
        </table><br />
        <input type="submit" value="Checkout" />
    </form>
</body>
</html>
```

请注意，此表单将发布一个名称 - 值对。目标操作的参数应该是`int selected`。

#### 复选框/ CheckBoxFor

生成元素的功能`CheckBox`和`CheckBoxFor`生成`<input type="checkbox" />`元素。他们通常也使用收藏品。但与单选按钮不同，复选框允许多个选择。

修改代码`Show.cshtml`为：

```
@model List<MyWebApp.Models.Product>
<html>
<body>
    <h2>Multiple Select</h2>
    @using (Html.BeginForm("TargetAction", "TargetController")) {
        <table border="1">
            <tr><th>Buy</th><th>Name</th><th>Price</th></tr>
            @foreach (var p in Model) {
                <tr>
                    <td>@Html.CheckBox("selected", false, new { value = p.ID })</td>
                    <td>@p.Name</td>
                    <td>@p.Price</td>
                </tr>
            }
        </table><br />
        <input type="submit" value="Checkout" />
    }
</body>
</html>
```

运行应用程序，您应该看到：

生成的HTML代码是：

```
<html>
<body>
    <h2>Multiple Select</h2>
    <form action="/TargetController/TargetAction" method="post">
        <table border="1">
            <tr><th>Buy</th><th>Name</th><th>Price</th></tr>
            <tr>
                <td><input id="selected" name="selected" type="checkbox" value="101" /></td>
                <td>Apple</td>
                <td>19.99</td>
            </tr>
            <tr>
                <td><input id="selected" name="selected" type="checkbox" value="201" /></td>
                <td>Bike</td>
                <td>29.99</td>
            </tr>
            <tr>
                <td><input id="selected" name="selected" type="checkbox" value="301" /></td>
                <td>Couch</td>
                <td>39.99</td>
            </tr>
        </table><br />
        <input type="submit" value="Checkout" />
    </form>
</body>
</html>
```

此表单将发布所选产品ID的集合，因此目标操作的参数应为`int[] selected`。

#### DropDownList / DropDownListFor和ListBox / ListBoxFor

在HTML页面上，下拉列表和列表框只是`<select>`元素的不同视图，其中包含`<option>`元素。

修改代码`Show.cshtml`为：

```
@model List<MyWebApp.Models.Product>
<html>
<body>
    <h2>Dropdown List</h2>
    @using (Html.BeginForm("TargetAction", "TargetController")) {
        @Html.DropDownList("seleted", new SelectList(Model, "ID", "Name"))
        <input type="submit" value="Checkout" />
    }
    <hr />
    <h2>List Box</h2>
    @{
        var items = Model.Select(p => new SelectListItem { Value = p.ID.ToString(), Text = $"{p.Name} (${p.Price})" });
    }
    @using (Html.BeginForm("TargetAction", "TargetController")) {
        @Html.ListBox("seleted", items)<br />
        <input type="submit" value="Checkout" />
    }
</body>
</html>
```

运行应用程序，您应该看到：

生成的HTML代码是：

```
<html>
<body>
    <h2>Dropdown List</h2>
    <form action="/TargetController/TargetAction" method="post">
        <select id="seleted" name="seleted">
            <option value="101">Apple</option>
            <option value="201">Bike</option>
            <option value="301">Couch</option>
        </select>
        <input type="submit" value="Checkout" />
    </form>
    <hr />
    <h2>List Box</h2>
    <form action="/TargetController/TargetAction" method="post">
        <select id="seleted" multiple="multiple" name="seleted">
            <option value="101">Apple ($19.99)</option>
            <option value="201">Bike ($29.99)</option>
            <option value="301">Couch ($39.99)</option>
        </select><br />
        <input type="submit" value="Checkout" />
    </form>
</body>
</html>
```

在视图代码中，我们向您展示了创建项集合的两种不同方法。最简单的方法是使用`SelectList`类的构造函数。如果要自定义显示文本，则必须`SelectListItem`手动制作集合。

正如您在HTML代码中看到，无论是`DropDownList`和`ListBox`生成`<select>`的元素。默认情况下，`<select>`由a生成`ListBox`允许多个选定项。因此，包含下拉列表的表单将回发单个名称 - 值对，而包含列表框的表单将回发名称 - 值对集合。

#### 标签/ LabelFor

该`<label>`元素总是使用`<input>`的元素，共同提高用户体验。如果`<input>`未禁用元素，则单击其标签将使其获得输入焦点。

让我们修改代码`Edit.cshtml`为：

```
@model MyWebApp.Models.Product
<html>
<body>
    @using (Html.BeginForm("TargetAction", "TargetController")) {
        @Html.HiddenFor(p => p.ID, Model.ID)<br />
        <table>
            <tr>
                <td>@Html.LabelFor(p => p.Name)</td>
                <td>@Html.TextBoxFor(p => p.Name, Model.Name)</td>
            </tr>
            <tr>
                <td>@Html.LabelFor(p => p.Price)</td>
                <td>@Html.TextBoxFor(p => p.Price, Model.Price)</td>
            </tr>
            <tr>
                <td colspan="2">
                    <input type="submit" style="float:right" value="Submit" />
                </td>
            </tr>
        </table>
    }
</body>
</html>
```

运行应用程序，我们应该看到：

生成的HTML代码是：

```
<html>
<body>
    <form action="/TargetController/TargetAction" method="post">
        <input data-val="true" data-val-required="The ID field is required." id="ID" name="ID" type="hidden" value="901" /><br />
        <table>
            <tr>
                <td><label for="Name">Name</label></td>
                <td><input id="Name" name="Name" type="text" value="Rocket" /></td>
            </tr>
            <tr>
                <td><label for="Price">Price</label></td>
                <td><input data-val="true" data-val-number="The field Price must be a number." data-val-required="The Price field is required." id="Price" name="Price" type="text" value="99.99" /></td>
            </tr>
            <tr>
                <td colspan="2">
                    <input type="submit" style="float:right" value="Submit" />
                </td>
            </tr>
        </table>
    </form>
</body>
</html>
```

我们知道，如果's 属性值等于's 属性值，则会关联一对`<label>`和`<input>`元素。因此，如果发生任何更改，我们希望值和属性一起更改。这就是我们在这个例子中使用帮助器的原因。`<input>``id``<label>``for``id``for``-For`