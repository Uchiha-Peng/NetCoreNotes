## Windows Server 2016+安装Docker(OsType:Windows)

``` 
*此教程适用于windows server 2016及以上版本，不依赖Hyper-v,OsType为Windows
```

1. 第一步，确保window server 服务器更新并安装容器服务，如下图即可

![Screen](/images/docker.png)

2. 第二步骤，从 [PowerShell 库](https://www.powershellgallery.com/packages/DockerMsftProvider)安装 Docker-Microsoft PackageManagement 提供程序。

```
Install-Module -Name DockerMsftProvider -Repository PSGallery -Force
```

3. 第三步，使用 PackageManagement PowerShell 模块安装最新版本的 Docker。
```
Install-Package -Name docker -ProviderName DockerMsftProvider
```

*你以为一切就可以这么顺利的执行下去吗，那这样的话我就没必要写这个所谓对的教程了，到这里，注意，要报错了，错误的内容大概如下：*

```
Install-Package : 找不到路径“C:\Users\你的用户名\AppData\Local\Temp\2\DockerMsftProvider\Docker-18-09-0.zip”，因为该路径不存在。
```

4. 第四步，解决这个错误

```
把这个路劲拷贝出来，在资源管理器访问，记得删掉Docker-18-09-0.zip，进入这个文件夹，你会看到一个json文件，叫啥名不重要，反正只有一个，打开这个json文件往下看，里面会写着这个Docker-18-09-0.zip文件的完整URL，记得找名字一样的，不然还会报错，因为，然后你拷贝出来，下载这个zip文件，然后原封不动的拷贝到json所在的这个文件夹下，然后继续执行上一个命令
```

```
Install-Package -Name docker -ProviderName DockerMsftProvider
```

***注意，执行这个命令的意思是下载最新版的Docker，所以Docker-18-09-0.zip这个文件名不是固定的，有可能是Docker-19-09-0.zip什么的**，如果一切都还顺利，那么这一次会有进度条了，你可以执行```docker info```命令来查看是否安装成功，正确的 应该是下面这样的

```
PS C:\DockerPackages\CoreWeb> docker info
Containers: 0
 Running: 0
 Paused: 0
 Stopped: 0
Images: 1
Server Version: 18.09.0
Storage Driver: windowsfilter
 Windows:
Logging Driver: json-file
Plugins:
 Volume: local
 Network: ics l2bridge l2tunnel nat null overlay transparent
 Log: awslogs etwlogs fluentd gelf json-file local logentries splunk syslog
Swarm: inactive
Default Isolation: process
Kernel Version: 10.0 14393 (14393.953.amd64fre.rs1_release_inmarket.170303-1614)
Operating System: Windows Server 2016 Datacenter Version 1607 (OS Build 14393.969)
OSType: windows
Architecture: x86_64
CPUs: 4
Total Memory: 7.999GiB
Name: 172_16_0_10
ID: ZHB5:4FH7:MJP4:BWFN:HQUF:FG3B:GAES:VIZ3:UEYT:T2SK:I7T2:JIWI
Docker Root Dir: C:\ProgramData\docker
Debug Mode (client): false
Debug Mode (server): false
Registry: https://index.docker.io/v1/
Labels:
Experimental: false
Insecure Registries:
 127.0.0.0/8
Live Restore Enabled: false
```



