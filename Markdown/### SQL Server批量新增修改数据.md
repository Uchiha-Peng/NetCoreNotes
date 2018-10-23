### SQL Server批量新增修改数据

DECLARE @i INT,@count INT
set @i=0;
select @count=Count(*) from Pms_DeviceInformation where F_ObjectType=0
WHILE @i<@count     --10000为你要执行的次数
BEGIN
update Pms_DeviceInformation set F_ElectricQuantity=cast( floor(rand()*100) as int) where F_ObjectType=0 
SET @i=@i+1
END