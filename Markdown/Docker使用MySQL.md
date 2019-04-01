## Docker使用MySQL

### Docker命令

**拉取镜像，默认latest最新版**

```powershell
docker pull mysql
```

**运行MySQL容器，并创建用户设置密码，修改字符集【不使用资料卷】**
```
docker run -d -p 3306:3306 -e MYSQL_USER="sa" -e MYSQL_PASSWORD="sasa" -e MYSQL_ROOT_PASSWORD="sasa" --name mysql mysql:latest --character-set-server=utf8 --collation-server=utf8_general_ci
```

**进入bash**

```
docker exec -it mysql bash
```

### MySQL命令

**root登录**

```
mysql -uroot -p
```

**选择mysql系统数据库**

```
use mysql;
```

**查询用户和Host**

```
select host,user from user;
```

**修改权限，所有iP可访问**

```
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'sasa';
ALTER USER 'sa'@'%' IDENTIFIED WITH mysql_native_password BY 'sasa';
```

**刷新权限**

```
FLUSH PRIVILEGES;
```

