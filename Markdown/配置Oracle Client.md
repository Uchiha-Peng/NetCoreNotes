#### 配置Oracle Client

1. 下载

> 下载地址：http://www.oracle.com/technetwork/topics/winx64soft-089540.html
> 这是Oracle Instant Client的下载首页，有很多种版本可供下载。
> 但要注意第三方工具如：PL/SQL Developer和Toad的版本，32位的要对应32位的OracleInstant Client，不要因为系统是64位的就下载64位的，这个要注意。
 

2. 配置

> 把下载的instantclient-basic-nt-11.2.0.2.0.zip压缩包解压，放到 C:\instantclient_11_2 目录下。
> 在“环境变量”的“系统变量”中增加：
> ORACLE_HOME = C:\instantclient_11_2
> TNS_ADMIN = C:\instantclient_11_2
> NLS_LANG = SIMPLIFIED CHINESE_CHINA.ZHS16GBK
> 修改Path变量，在后面添加 C:\instantclient_11_2
 

3. 新建tnsnames.ora文件
> 在C:\instantclient_11_2 新建一个tnsnames.ora文件，增加自己的数据库别名配置。
> 示例如下：
> MyDB= 
> (DESCRIPTION =
> (ADDRESS = (PROTOCOL = TCP)(HOST= 172.16.1.16)(PORT = 1521))
> (CONNECT_DATA =
> (SERVER = DEDICATED)
> (SERVICE_NAME = ora10g)
> )
> ) 
> 注意格式要排列好
> 主要改 = 前面的别名，Host为IP地址, SERVICE_NAME为数据库服务器的实例名。
 

4. 卸载方法

> “环境变量”中的“系统变量”中：
> 删除 ORACLE_HOME,TNS_ADMIN, NLS_LANG 三个变量,修改path变量，去掉C:\instantclient_11_2目录
> 删除C:\instantclient_11_2目录