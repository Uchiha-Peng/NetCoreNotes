#### 常用的DAX函数

1. #####  聚合函数

   > SUM(计算某列中的数字的和) 
   >
   > COUNT(计算某列的行数)
   >
   > DISTINCTCOUNT(求某列非重复的行数)
   >
   > MIN(求某列的最小值，或是从两个数字中输入较小的值)
   >
   > MAX(求某列的最大值，或是从两个数字中输入较大的值)
   >
   > AVERAGE(求某列的平均值)

2. ##### Calculate(计算公式，筛选条件1，筛选条件2) 

   >  多条件筛选用Calculate(计算公式，'table'[column] IN  {"value1","value2","value3" }) 
   >
   > 或Calculate(计算公式，NOT 'table'[column] IN {"value1","value2","value3" }) 

3. ##### 时间智能

   > ****