## MVC中的模型分类

基于模型的使用，我们可以将模型分为三类。他们是：

1. **域模型**(**Domain models**)：域模型类表示并反映参与业务逻辑的现实世界对象。例如，`Student`和`Teacher`对象在学校系统，`Order`和`Product`对象在销售管理系统。
2. **视图模型**(**View models**)：视图模型类是为特定视图设计的。例如，如果我们想在页面上显示客户及其所有订单，我们可以创建一个名为的类，`CustomerOrdersViewModel`并让此类具有`Customer`type属性和`IList<Order>`type属性。
3. **数据传输模型**(**Data transfer model**)：通常，我们称之为数据传输对象（DTO）。有时我们必须将一些临时的数据字段组合从视图转移到控制器，尤其是在提交表单或调用AJAX调用时。在这些情况下，使用DTO是一个很好的解决方案。

## 模型的基本设计原则

在整个课程中我们使用C＃，我们知道C＃是一种面向对象（OO）编程语言。作为一名经验丰富的C＃开发人员，您可能听说过**SOLID**原则，即面向对象设计（OOD）的“常识”。要设计一个好的域模型，我们应该遵循SOLID原则，但在必要时也适度适应业务逻辑的特定情况。

下表是**SOLID**原则的摘要。你可以找到在[百科]（https://en.wikipedia.org/wiki/SOLID_（对象oriented_design）这些原则的更多细节。另外，我强烈推荐这本书*敏捷原则，模式与实践在C＃中*进一步阅读这个主题。

| 初始 | 原理                  | 概念                                                         |
| ---- | --------------------- | ------------------------------------------------------------ |
| S    | 单一责任原则（SRP）   | 一个类应该只有一个责任（即，软件规范中只有一个潜在的变化应该能够影响类的规范） |
| O    | 开放/封闭原则（OCP）  | “软件实体......应该是可以扩展的，但是需要修改。”             |
| L    | Liskov替换原理（LSP） | “程序中的对象应该可以替换为其子类型的实例，而不会改变该程序的正确性。”另见合同设计。 |
| I    | 接口隔离原理（ISP）   | “许多客户端特定的接口比一个通用接口更好。”                   |
| D    | 依赖倒置原则（DIP）   | 一个人应该“依赖抽象，而不是固定”。                           |

## 先进的设计原则和设计模式

当抽象来自现实世界的数据并将其封装到域模型时，域模型之间的关系反映了现实世界对象之间的关系。其中一些关系是清晰，固定和通用的。例如，经理的下属也可以是经理，文件夹中的子项也可以是文件夹。对于它们，我们可以设计一个接口：

```
public interface IComponent {
    int ID { get; set; }
    string Name { get; set; }
    IComponent Parent { get; set; }
    ICollection<IComponent> Children { get; set; }
}
```

这种关系将一组对象组合在一起并生成数据结构，我们将其称为**设计模式**。教学设计模式超出了这个课程，但如果你想了解更多有关设计模式的信息，那么[DoFactory](http://www.dofactory.com/)网站是一个非常好的资源。您可以在本网站上找到所有23种经典设计模式及其C＃实现的定义。

另请注意，设计模式基于SOLID原则。如果您抽象业务逻辑并创建域模型遵循SOLID原则，那么设计模式将自行出现。

## MySQL、SQL Server数据库——C＃数据类型映射表

| MySQL Type Name             | Return value of GetColumnClassName | .NET Framework Type                                          |
| --------------------------- | ---------------------------------- | ------------------------------------------------------------ |
| BIT(1)                      | BIT                                | bool/System.Boolean                                          |
| BIT(>1)                     | BIT                                | byte[]                                                       |
| TINYINT                     | TINYINT                            | bool/System.Boolean if the configuration property tinyInt1isBit is set to true (the default) and the storage size is 1, or int/System.Int32 if not. |
| BOOL, BOOLEAN               | TINYINT                            | See TINYINT, above as these are aliases for TINYINT(1), currently. |
| SMALLINT[(M)] [UNSIGNED]    | SMALLINT [UNSIGNED]                | int/System.Int32 (uint/System.UInt32 if it is UNSIGNED)      |
| MEDIUMINT[(M)] [UNSIGNED]   | MEDIUMINT [UNSIGNED]               | int/System.Int32 (uint/System.UInt32 if it is UNSIGNED)      |
| INT,INTEGER[(M)] [UNSIGNED] | INTEGER [UNSIGNED]                 | int/System.Int32, if UNSIGNED System.UInt32                  |
| BIGINT[(M)] [UNSIGNED]      | BIGINT [UNSIGNED]                  | long/System.Int64, if UNSIGNED ulong/System.UInt64           |
| FLOAT[(M,D)]                | FLOAT                              | float/System.Single                                          |
| DOUBLE[(M,B)]               | DOUBLE                             | double/System.Double                                         |
| DECIMAL[(M[,D])]            | DECIMAL                            | decimal                                                      |
| DATE                        | DATE                               | System.DateTime                                              |
| DATETIME                    | DATETIME                           | System.DateTime                                              |
| TIMESTAMP[(M)]              | TIMESTAMP                          | System.DateTime                                              |
| TIME                        | TIME                               | System.DateTime                                              |
| YEAR[(2 \| 4)]              | YEAR                               | int/System.Int32                                             |
| CHAR(M)                     | CHAR                               | string/System.String (unless the character set for the column is BINARY, then byte[] is returned. |
| VARCHAR(M) [BINARY]         | VARCHAR                            | string/System.String (unless the character set for the column is BINARY, then byte[] is returned. |
| BINARY(M)                   | BINARY                             | byte[]                                                       |
| VARBINARY(M)                | VARBINARY                          | byte[]                                                       |
| TINYBLOB                    | TINYBLOB                           | byte[]                                                       |
| TINYTEXT                    | VARCHAR                            | string/System.String                                         |
| BLOB                        | BLOB                               | byte[]                                                       |
| TEXT                        | VARCHAR                            | string/System.String                                         |
| MEDIUMBLOB                  | MEDIUMBLOB                         | byte[]                                                       |
| MEDIUMTEXT                  | VARCHAR                            | string/System.String                                         |
| LONGBLOB                    | LONGBLOB                           | byte[]                                                       |
| LONGTEXT                    | VARCHAR                            | string/System.String                                         |
| ENUM(‘value1’,'value2',...) | CHAR                               | string/System.String                                         |
| SET(‘value1’,'value2',...)  | CHAR                               | string/System.String                                         |

| SQL Server Database Engine type                   | .NET Framework Type                       |
| ------------------------------------------------- | ----------------------------------------- |
| bigint                                            | long/System.Int64                         |
| binary                                            | byte[]/System.Byte[]                      |
| bit                                               | bool/System.Boolean                       |
| char                                              | string/System.String/char[]/System.Char[] |
| date/(SQL Server 2008 and later)                  | System.DateTime                           |
| System.DateTime                                   | System.DateTime                           |
| System.DateTime2/(SQL Server 2008 and later)      | System.DateTime                           |
| System.DateTimeoffset/(SQL Server 2008 and later) | System.DateTimeOffset                     |
| decimal/System.Decimal                            | decimal/System.Decimal                    |
| FILESTREAM attribute (varbinary(max))             | byte[]/System.Byte[]                      |
| float                                             | double/System.Double                      |
| image                                             | byte[]/System.Byte[]                      |
| int                                               | int/System.Int32                          |
| money                                             | decimal/System.Decimal                    |
| nchar                                             | string/System.String/char[]/System.Char[] |
| ntext                                             | string/System.String/char[]/System.Char[] |
| numeric                                           | decimal/System.Decimal                    |
| nvarchar                                          | string/System.String/char[]/System.Char[] |
| real                                              | float/System.Single                       |
| rowversion                                        | byte[]/System.Byte[]                      |
| smallSystem.DateTime                              | System.DateTime                           |
| smallint                                          | short/System.Int16                        |
| smallmoney                                        | decimal/System.Decimal                    |
| sql_variant                                       | object/System.Object                      |
| text                                              | string/System.String/char[]/System.Char[] |
| time/(SQL Server 2008 and later)                  | System.TimeSpan                           |
| timestamp                                         | byte[]/System.Byte[]                      |
| tinyint                                           | byte/System.Byte                          |
| uniqueidentifier                                  | System.Guid                               |
| varbinary                                         | byte[]/System.Byte[]                      |
| varchar                                           | string/System.String/char[]/System.Char[] |
| xml                                               | System.Xml                                |