# C#一维数组

可以声明五个整数的一维数组，如以下示例所示：

```C#
int[] array = new int[5];
```

此数组包含从 `array[0]` 到 `array[4]` 的元素。 [new](https://docs.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/new) 运算符用于创建数组并将数组元素初始化为其默认值。 在此示例中，所有数组元素都将被初始化为零。

可使用相同方式声明存储字符串元素的数组。 例如:

```C#
string[] stringArray = new string[6];
```

## 数组初始化

可以在声明时初始化数组，在这种情况下，无需秩说明符，因为它已由初始化列表中的元素数目提供。 例如:

```C#
int[] array1 = new int[] { 1, 3, 5, 7, 9 };
```

可使用相同方式初始化字符串数组。 下面是一个字符串数组的声明，其中每个数组元素都由一天的名称初始化：

```C#
string[] weekDays = { "Sun", "Mon", "Tue", "Wed", "Thu", "Fri", "Sat" };
```

如果在声明时初始化数组，可以使用以下快捷方式：

```C#
int[] array2 = { 1, 3, 5, 7, 9 };
string[] weekDays2 = { "Sun", "Mon", "Tue", "Wed", "Thu", "Fri", "Sat" };
```

可以在不初始化的情况下声明数组变量，但必须使用 `new` 运算符向此变量分配数组。 例如:

```C#
int[] array3;
array3 = new int[] { 1, 3, 5, 7, 9 };   // OK
//array3 = {1, 3, 5, 7, 9};   // Error
```

C# 3.0 引入了隐式类型化数组。 有关详细信息，请参阅[隐式类型化数组](https://docs.microsoft.com/zh-cn/dotnet/csharp/programming-guide/arrays/implicitly-typed-arrays)。

## 值类型和引用类型数组

请考虑以下数组声明：

```C#
SomeType[] array4 = new SomeType[10];
```

此语句的结果取决于 `SomeType` 是值类型还是引用类型。 如果它是值类型，该语句将创建一个 10 个元素的数组，其中每个元素的类型都为 `SomeType`。 如果 `SomeType` 是引用类型，该语句将创建一个 10 个元素的数组，其中每个元素都将被初始化为空引用。

有关值类型和引用类型的详细信息，请参阅[类型](https://docs.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/types)。



# C#多维数组

数组可具有多个维度。 例如，以下声明创建一个具有四行两列的二维数组。

```C#
int[,] array = new int[4, 2];
```

以下声明创建一个具有三个维度（4、2 和 3）的数组。

```C#
int[, ,] array1 = new int[4, 2, 3];
```

## 数组初始化

声明后即可初始化数组，如以下示例所示。

```C#
// 二维数组.
int[,] array2D = new int[,] { { 1, 2 }, { 3, 4 }, { 5, 6 }, { 7, 8 } };
// 另一种方式指定二位数组，并且限定长度为4
int[,] array2Da = new int[4, 2] { { 1, 2 }, { 3, 4 }, { 5, 6 }, { 7, 8 } };
// 字符串类型的二维数组，并且限定长度为3
string[,] array2Db = new string[3, 2] { { "one", "two" }, { "three", "four" },
                                        { "five", "six" } };

// 三维数组
int[, ,] array3D = new int[,,] { { { 1, 2, 3 }, { 4, 5, 6 } }, 
                                 { { 7, 8, 9 }, { 10, 11, 12 } } };
// 另一种方式指定三维数组，并且限定长度为2，每个元素内含两个子元素，且每个子元素有三个维度
int[, ,] array3Da = new int[2, 2, 3] { { { 1, 2, 3 }, { 4, 5, 6 } }, 
                                       { { 7, 8, 9 }, { 10, 11, 12 } } };

// Accessing array elements.
System.Console.WriteLine(array2D[0, 0]);
System.Console.WriteLine(array2D[0, 1]);
System.Console.WriteLine(array2D[1, 0]);
System.Console.WriteLine(array2D[1, 1]);
System.Console.WriteLine(array2D[3, 0]);
System.Console.WriteLine(array2Db[1, 0]);
System.Console.WriteLine(array3Da[1, 0, 1]);
System.Console.WriteLine(array3D[1, 1, 2]);

// Getting the total count of elements or the length of a given dimension.
var allLength = array3D.Length;
var total = 1;
for (int i = 0; i < array3D.Rank; i++) {
    total *= array3D.GetLength(i);
}
System.Console.WriteLine("{0} equals {1}", allLength, total);

// Output:
// 1
// 2
// 3
// 4
// 7
// three
// 8
// 12
// 12 equals 12
```

还可在不指定秩的情况下初始化数组。

```C#
int[,] array4 = { { 1, 2 }, { 3, 4 }, { 5, 6 }, { 7, 8 } };
```

如果选择在不初始化的情况下声明数组变量，则必须使用 `new` 运算符将数组赋予变量。 `new` 的用法如以下示例所示。

```C#
int[,] array5;
array5 = new int[,] { { 1, 2 }, { 3, 4 }, { 5, 6 }, { 7, 8 } };   // OK
//array5 = {{1,2}, {3,4}, {5,6}, {7,8}};   // Error
```

以下示例将值赋予特定的数组元素。

```C#
array5[2, 1] = 25;
```

同样，以下示例将获取特定数组元素的值并将其赋予变量 `elementValue`。

```C#
int elementValue = array5[2, 1];
```

以下代码示例将数组元素初始化为默认值（交错数组除外）。

```C#
int[,] array6 = new int[10, 10];
```


