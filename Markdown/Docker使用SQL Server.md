## Docker使用SQL Server

#### Docker命令

**拉取最新镜像**

```
docker pull mcr.microsoft.com/mssql/server
```

**运行SQL Server容器，设置sa密码、Express版本，创建资料卷、自动启动**

```
docker run -d -p 1433:1433 --restart always --name mssql -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=.a201314'  -e 'MSSQL_PID=Express'   -v sqlvolume:/var/opt/mssql mcr.microsoft.com/mssql/server:latest
```

