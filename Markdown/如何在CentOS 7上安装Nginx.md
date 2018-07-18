# 如何在CentOS 7上安装Nginx

### 关于Nginx

Nginx是一款高性能的Web服务器软件。它比Apache HTTP Server更灵活轻便。

本教程将教您如何在CentOS 7服务器上安装并启动Nginx。

## 先决条件

本教程中的步骤要求用户拥有root权限。您可以通过[使用CentOS 7](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-centos-7)教程的[初始服务器设置](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-centos-7)中的步骤3和4来了解如何设置。

## 第一步 - 添加Nginx存储库

要添加CentOS 7 EPEL存储库，请打开终端并使用以下命令：

```
sudo yum install epel-release
```

## 第二步 - 安装Nginx

现在Nginx存储库已安装在您的服务器上，请使用以下`yum`命令安装Nginx ：

```
sudo yum install nginx
```

在对提示回答yes后，Nginx将在您的虚拟专用服务器（VPS）上完成安装。

## 第三步 - 启动Nginx

Nginx不会自行启动。要让Nginx运行，请输入：

```
sudo systemctl start nginx
```

如果您正在运行防火墙，请运行以下命令以允许HTTP和HTTPS流量：

```
sudo firewall-cmd --permanent --zone=public --add-service=http 
sudo firewall-cmd --permanent --zone=public --add-service=https
sudo firewall-cmd --reload
```

您可以立即进行现场检查，通过在Web浏览器中访问服务器的公共IP地址来验证所有事情是否按计划进行（请参阅下一标题下的注释，以了解如果您没有此信息时您的公共IP地址是什么已经）：

```
http://server_domain_name_or_IP/
```

您将看到默认的CentOS 7 Nginx网页，该网页用于提供信息和测试目的。它应该看起来像这样：

![CentOS 7 Nginx默认](https://assets.digitalocean.com/articles/lemp_1404/nginx_default.png)

如果您看到此页面，则您的Web服务器现在已正确安装。

在继续之前，您可能需要在系统引导时启用Nginx。为此，请输入以下命令：

```
sudo systemctl enable nginx
```

恭喜！Nginx现在已经安装并正在运行！

### 如何查找您的服务器的公共IP地址

要找到您的服务器的公共IP地址，请键入以下命令找到您计算机上的网络接口：

```
ip addr
```

```
1. lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN

. . .
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000

. . .
```

您可能会在此看到许多接口，具体取决于服务器上可用的硬件。该`lo`接口是本地环回接口，这不是我们想要的。在我们上面的例子中，`eth0`接口就是我们想要的。

获得接口名称后，可以运行以下命令来显示服务器的公共IP地址。替换您在上面找到的接口名称：

```
ip addr show eth0 | grep inet | awk '{ print $2; }' | sed 's/\/.*$//'
```

## 服务器根目录和配置

如果你想通过Nginx开始提供自己的页面或应用程序，你需要知道Nginx配置文件和默认服务器根目录的位置。

### 默认服务器根

默认的服务器根目录是`/usr/share/nginx/html`。放置在那里的文件将在您的Web服务器上提供。该位置在位于的Nginx附带的默认服务器块配置文件中指定`/etc/nginx/conf.d/default.conf`。

### 服务器块配置

任何额外的服务器模块，在Apache中称为虚拟主机，都可以通过在其中创建新的配置文件来添加`/etc/nginx/conf.d`。`.conf`当Nginx启动时，将以该目录结尾的文件加载。

### Nginx全局配置

主要的Nginx配置文件位于`/etc/nginx/nginx.conf`。这是您可以更改设置的位置，例如运行Nginx守护进程的用户，以及Nginx运行时产生的工作进程数量等。