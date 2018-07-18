# [JSON.NET与LINQ序列化示例教程](https://www.xcode.me/more/json-net-serializing-linq-samples)



JSON.NET与LINQ的完美结合，让.NET操作JSON变得非常的简单，最后我们还会演示如何通过JSON.NET转换BSON数据、如何实现JSON格式与XML格式的互相转换，让您熟练在.NET中序列化对象。

### 1、手动创建JSON对象和数组

JSON格式主要包括对象和数组两种形式，在JSON.NET中分别用JArray和JObject表示，属性用JProperty表示，属性值用JValue表示，这些对象都直接或间接继承JToken抽象类，而且都有一个ToString方法，该方法用于直接输出元素的JSON字符串表示形式，我们可以通过JArray和JObject手动创建JSON并序列化，使用方式非常灵活。

```
JArray array = new JArray();
array.Add("Manual text");
array.Add(new DateTime(2000, 5, 23));
JObject o = new JObject();
o["MyArray"] = array;
string json = o.ToString();
```

```
{
  "MyArray": [
    "Manual text",
    "2000-05-23T00:00:00"
  ]
}
```

### 2、用集合的方式初始化JArray数组和JObject对象

```
JObject o = new JObject { { "Cpu", "Intel" }, { "Memory", 32 }, { "Drives", new JArray { "DVD", "SSD" } } };
Console.WriteLine(o.ToString());
```

```
{
  "Cpu": "Intel",
  "Memory": 32,
  "Drives": [
    "DVD",
    "SSD"
  ]
}
```

### 3、使用动态类型dynamic类型创建JSON数组和对象

C#中的动态类型dynamic允许开发者动态构造对象，这些动态构造的对象在运行时被解析执行，JSON.NET支持动态类型，无需定义类，即可构造JSON，这一些就像在JavaScript中编写一个对象那样简单，正是因为动态类型在运行时才被解析，所以，在开发工具中不能检查因书写而导致错误，这点一定要注意。

```
dynamic product = new JObject();
product.ProductName = "Elbow Grease";
product.Enabled = true;
product.Price = 4.90m;
product.StockCount = 9000;
product.StockValue = 44100;
product.Tags = new JArray("Real", "OnSale");
Console.WriteLine(product.ToString());
```

```
{
  "ProductName": "Elbow Grease",
  "Enabled": true,
  "Price": 4.90,
  "StockCount": 9000,
  "StockValue": 44100,
  "Tags": [
    "Real",
    "OnSale"
  ]
}
```

### 4、使用JTokenWriter创建对象和数组

```
JTokenWriter writer = new JTokenWriter();
writer.WriteStartObject();
writer.WritePropertyName("name1");
writer.WriteValue("value1");
writer.WritePropertyName("name2");
writer.WriteStartArray();
writer.WriteValue(1);
writer.WriteValue(2);
writer.WriteEndArray();
writer.WriteEndObject();
JObject o = (JObject)writer.Token;
Console.WriteLine(o.ToString());
```

```
{
  "name1": "value1",
  "name2": [
    1,
    2
  ]
}
```

### 5、使用对象创建JSON对象和数组

JToken、JObject和JArray都提供一个名为FromObject的方法，允许通过一个对象来创建JSON对象。

```
Computer computer = new Computer
{
    Cpu = "Intel",
    Memory = 32,
    Drives = new List<string> { "DVD", "SSD" }
};
JObject o = (JObject)JToken.FromObject(computer);
Console.WriteLine(o.ToString());
```

```
{
  "Cpu": "Intel",
  "Memory": 32,
  "Drives": [
    "DVD",
    "SSD"
  ]
}
```

```
JArray a = (JArray)JToken.FromObject(computer.Drives);
Console.WriteLine(a.ToString());
```

```
[
  "DVD",
  "SSD"
]
```

### 6、使用字符串创建JSON对象和数组

```
string json = @"['Small', 'Medium','Large']";
JArray a = JArray.Parse(json);
Console.WriteLine(a.ToString());
```

```
[
  "Small",
  "Medium",
  "Large"
]
```

```
string json = @"{
    CPU: 'Intel',
    Drives: [
      'DVD read/writer',
      '500 gigabyte hard drive'
    ]
  }";
JObject o = JObject.Parse(json);
Console.WriteLine(o.ToString());
```

```
{
  "CPU": "Intel",
  "Drives": [
    "DVD read/writer",
    "500 gigabyte hard drive"
  ]
}
```

### 7、使用JToken转换JSON字符串

在示例1中，我们已经说明JToken类型，它是JSON.NET的核心，几乎所有的JSON元素都继承于JToken抽象类，虽然JToken是抽象类，但它也提供一些很有用的基础方法，可以转换一段字符串并判断它应有的JSON类型。

```
JToken t1 = JToken.Parse("{}");
Console.WriteLine(t1.Type);
// Object

JToken t2 = JToken.Parse("[]");
Console.WriteLine(t2.Type);
// Array

JToken t3 = JToken.Parse("null");
Console.WriteLine(t3.Type);
// Null

JToken t4 = JToken.Parse(@"'A string!'");
Console.WriteLine(t4.Type);
// String
```

### 8、动态修改JSON对象和数组

```
string json = @"{
     'channel': {
       'title': 'Star Wars',
       'link': 'www.xcode.me',
       'description': 'Star Wars blog.',
       'obsolete': 'Obsolete value',
       'item': []
     }
   }";
JObject rss = JObject.Parse(json);
JObject channel = (JObject)rss["channel"];
channel["title"] = ((string)channel["title"]).ToUpper();
channel["description"] = ((string)channel["description"]).ToUpper();
channel.Property("obsolete").Remove();
channel.Property("description").AddAfterSelf(new JProperty("new", "New value"));
JArray item = (JArray)channel["item"];
item.Add("Item 1");
item.Add("Item 2");
Console.WriteLine(rss.ToString());
```

```
{
  "channel": {
    "title": "STAR WARS",
    "link": "www.xcode.com",
    "description": "STAR WARS BLOG.",
    "new": "New value",
    "item": [
      "Item 1",
      "Item 2"
    ]
  }
}
```

### 9、合并两个JSON对象

```
JObject o1 = JObject.Parse(@"{
      'FirstName': 'John',
      'LastName': 'Smith',
      'Enabled': false,
      'Roles': [ 'User' ]
    }");
JObject o2 = JObject.Parse(@"{
      'Enabled': true,
      'Roles': [ 'User', 'Admin' ]
    }");
o1.Merge(o2, new JsonMergeSettings { MergeArrayHandling = MergeArrayHandling.Union });
string json = o1.ToString();
```

```
{
  "FirstName": "John",
  "LastName": "Smith",
  "Enabled": true,
  "Roles": [
    "User",
    "Admin"
  ]
}
```

这里要特别说明的是JsonMergeSettings，该对象用于设置合并时的行为，MergeArrayHandling是枚举，可设置为Concat（连接数组）、Union（跳过存在的元素，添加不存在的元素）、Replace（替换所有数组项）和Merge（根据数组索引将数组项合并在一起）。

### 10、使用动态类型dynamic查询JSON对象

```
string json = @"[
  {
    'Title': 'Json.NET is awesome!',
    'Author': {
      'Name': 'James Newton-King',
      'Twitter': '@JamesNK',
      'Picture': '/jamesnk.png'
    },
    'Date': '2013-01-23T19:30:00'
  }
]";

dynamic blogPosts = JArray.Parse(json);
dynamic blogPost = blogPosts[0];
Console.WriteLine(blogPost.Title);
```

```
Json.NET is awesome!
```

### 11、使用ToObject将JSON对象或数组转换成C#对象

```
string json = @"{
   'd': [
     {
       'Name': 'John Smith'
     },
     {
       'Name': 'Mike Smith'
     }
   ]
 }";
JObject o = JObject.Parse(json);
JArray a = (JArray)o["d"];
IList<Person> person = a.ToObject<IList<Person>>();
Console.WriteLine(person[1].Name);
```

```
Mike Smith
```

### 12、通过属性遍历JObject对象

```
JObject o = new JObject { { "name1", "value1" }, { "name2", "value2" } };
foreach (JProperty property in o.Properties())
{
    Console.WriteLine(property.Name + " - " + property.Value);
}
```

```
name1 - value1
name2 - value2
```

```
foreach (KeyValuePair<string, JToken> property in o)
{
    Console.WriteLine(property.Key + " - " + property.Value);
}
```

```
name1 - value1
name2 - value2
```

### 13、使用ToString方法进行JSON序列化

在JSON.NET中ToString方法会输出针对当前JSON对象的字符串表示形式，通过这个特点我们可以序列化对象，默认情况下会输出格式化后的JSON字符串，ToString方法提供一些重载，允许开发灵活配置序列化行为。

```
JObject o = JObject.Parse(@"{'string1':'value','integer2':99,'datetime3':'2000-05-23T00:00:00'}");
Console.WriteLine(o.ToString());
```

```
{
  "string1": "value",
  "integer2": 99,
  "datetime3": "2000-05-23T00:00:00"
}
```

ToString方法也提供一个是否格式化重载方法，下面的示例演示输出压缩后的JSON字符串。

```
Console.WriteLine(o.ToString(Formatting.None));
```

```
{"string1":"value","integer2":99,"datetime3":"2000-05-23T00:00:00"}
```

下面的代码演示通过ToString方法提供的重载将日期格式化为JavaScript中的日期对象，这样的JSON在JS中就可以直接使用日期。

```
Console.WriteLine(o.ToString(Formatting.None, new JavaScriptDateTimeConverter()));
```

```
{"string1":"value","integer2":99,"datetime3":new Date(959032800000)}
```

### 14、JSON.NET支持BSON格式的序列化和反序列化

BSON是一种类似于JSON格式，是一种二进制形式存储格式，简称BinaryJSON，它和JSON一样，支持内嵌的对象和数组，BSON可以做为网络数据交换的一种存储形式，它的优点是灵活性高，但它的缺点是空间利用率不是很理想，JSON.NET也支持BSON的序列化和反序列化。

```
byte[] data = Convert.FromBase64String("KQAAAAJuYW1lMQAHAAAAdmFsdWUxAAJuYW1lMgAHAAAAdmFsdWUyAAA=");
MemoryStream ms = new MemoryStream(data);
JObject o;
using (BsonReader reader = new BsonReader(ms))
{
    o = (JObject)JToken.ReadFrom(reader);
}
string value = (string)o["name1"];
Console.WriteLine(value);
```

```
value1
```

```
JObject o = new JObject { { "name1", "value1" }, { "name2", "value2" } };
MemoryStream ms = new MemoryStream();
using (BsonWriter writer = new BsonWriter(ms))
{
    o.WriteTo(writer);
}
string data = Convert.ToBase64String(ms.ToArray());
Console.WriteLine(data);
```

```
KQAAAAJuYW1lMQAHAAAAdmFsdWUxAAJuYW1lMgAHAAAAdmFsdWUyAAA=
```

### 15、JSON.NET也支持JSON与XML数据格式之间的互转

```
string json = @"{
  '@Id': 1,
  'Email': 'james@example.com',
  'Active': true,
  'CreatedDate': '2013-01-20T00:00:00Z',
  'Roles': [
    'User',
    'Admin'
  ],
  'Team': {
    '@Id': 2,
    'Name': 'Software Developers',
    'Description': 'Creators of fine software products and services.'
  }
}";
XNode node = JsonConvert.DeserializeXNode(json, "Root");
Console.WriteLine(node.ToString());
```

```
<Root Id="1">
  <Email>james@example.com</Email>
  <Active>true</Active>
  <CreatedDate>2013-01-20T00:00:00Z</CreatedDate>
  <Roles>User</Roles>
  <Roles>Admin</Roles>
  <Team Id="2">
    <Name>Software Developers</Name>
    <Description>Creators of fine software products and services.</Description>
  </Team>
</Root>
```

```
string xml = @"<?xml version='1.0' standalone='no'?>
 <root>
   <person id='1'>
   <name>Alan</name>
   <url>www.xocde.me</url>
   </person>
   <person id='2'>
   <name>Louis</name>
   <url>www.xocde.me</url>
   </person>
 </root>";
XmlDocument doc = new XmlDocument();
doc.LoadXml(xml);
string json = JsonConvert.SerializeXmlNode(doc);
Console.WriteLine(json);
{
  "?xml": {
    "@version": "1.0",
    "@standalone": "no"
  },
  "root": {
    "person": [
      {
        "@id": "1",
        "name": "Alan",
        "url": "www.xocde.me"
      },
      {
        "@id": "2",
        "name": "Louis",
        "url": "www.xocde.me"
      }
    ]
  }
}
```

当然，JSON.NET框架很强大，功能也很多，零度总结都是JSON.NET与LINQ结合的常用示例，希望对您有所帮助。