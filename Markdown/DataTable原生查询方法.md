## DataTable原生查询方法

1. 实例化一个DataTable

   ```c#
               DataTable dt = new DataTable();
               DataColumn col1 = new DataColumn("ID", typeof(System.String));
               DataColumn col2 = new DataColumn("姓名", typeof(System.String));
               DataColumn col3 = new DataColumn("年龄", typeof(System.Int32));
               DataColumn col4 = new DataColumn("性别", typeof(System.Boolean));
               DataColumn col5 = new DataColumn("身高", typeof(System.Decimal));
               dt.Columns.AddRange(new DataColumn[] { col1, col2, col3, col4, col5 });
    
               dt.PrimaryKey = new DataColumn[] { col1};
    
               dt.Rows.Add("14101", "张三", 24, true, 1.65);
               dt.Rows.Add("14102", "张三", 22, false, 1.71);
               dt.Rows.Add("14103", "张三", 21, true, 1.58);
               dt.Rows.Add("14104", "张三", 20, false, 1.69);
               dt.Rows.Add("14105", "李四", 23, true, 1.71);
               dt.Rows.Add("14106", "李四", 26, false, 1.72);
               dt.Rows.Add("14107", "李四", 24, true, 1.71);
               dt.Rows.Add("14108", "李四", 22, false, 1.79);
               dt.Rows.Add("14109", "李四", 25, true, 1.81);
               dt.Rows.Add("14110", null, 25, true, 1.81);
   ```

2. **Compute方法**，按条件筛选数据并对数据进行运算

   ```c#
               object result = this.dt.Compute("Avg(身高)", "年龄 > 22 AND 姓名 LIKE '李*'");
               Console.WriteLine("所有姓李并且年龄大于22岁的学生的平均身高是{0}米", result.ToString());
   ```

   

3. **DataTableReader对象**遍历DataTable

   ```c#
               using(DataTableReader reader = this.dt.CreateDataReader()){
                   do
                   {
                       if (!reader.HasRows)
                       {
                           Console.WriteLine("没有数据");
                       }
                       else
                       {
                           Console.WriteLine("===============开始读取数据===============");
                           while (reader.Read())
                           {
                               for (int i = 0; i < reader.FieldCount; i++)
                               {
                                   Console.Write(reader[i] + "  ");
                               }
                               Console.WriteLine("");
                           }
                           Console.WriteLine("===============读取数据完成===============");
                       }
                   } while (reader.NextResult());
               }
   ```

   

4. **Select方法**按照条件筛选一条或多条数据

   ```c#
               DataRow[] resultArray = this.dt.Select("姓名 is NULL");
               for (int i = 0; i < resultArray.Length; i++)
               {
                   Console.WriteLine("姓名为空的ID：" + resultArray[i][0]);
               }
   ```

   

5. **Find方法**根据主键查找1行数据

   ```c#
               DataRow dr = this.dt.Rows.Find("14109");
               if (null != dr)
               {
                   Console.WriteLine("学号为14109的学生姓名为：" + dr[1].ToString());
               }
   ```