## CentOS下用Docker运行开源的分布式文件系统FastDF

***\*本教程适用于CentOS\****

---

#### 1.从官方Github仓库下载下载或克隆代码

```
git clone https://github.com/happyfish100/fastdfs.git
```

#### 2.进入源码的docker目录，有两个文件夹，dockerfile_local和dockerfile_network

```powershell
#任意选择一个，作者也说了，二者并没有什么区别
cd fastdfs/docker/dockerfile_local 
#或
cd fastdfs/docker/dockerfile_network
```

#### 3.选择一个目录进入后要，要编辑conf中的配置文件，主要是将IP修改为自己服务器的IP，有三处要修改

```powershell
cd conf
ls
#conf中包含以下配置文件
#client.conf  http.conf  mime.types  mod_fastdfs.conf  nginx.conf  storage.conf  tracker.conf
#需要修改的有client.conf、 mod_fastdfs.conf、storage.conf三个文件
#将默认的tracker_server=com.ikingtech.ch116221:22122改为自己服务器的ip:22122
#使用vim、其他文本编辑器，都可以
```

#### 4.制作镜像(请在dockerfile所在的目录执行命令)

```
docker build -t fastdfs:cp .
```

 #### 5. 运行容器（设置机器容器运行的IP，与第三步的IP保持一致，暴露22122、23000、8888、8011端口，并将Storage映射到服务器指定目录，以免容器GG后文件丢失）

```powershell
docker run -d -e FASTDFS_IPADDR=10.7.31.250 -v /home/docker/container/fastdfs:/home/dfs -p 8888:8888 -p 22122:22122 -p 23000:23000 -p 8011:80 --name fastdfs fastdfs:cp
#FASTDFS_IPADDR=10.7.31.250请和第三步修改配置文件的IP保持一致
#要注意 /home/docker/container/fastdfs这部分是服务器的路径，你可以自己定义，最好使用绝对路径，冒号后面的映射的是fastdfs storage的路径，建议不要更改
#22122是tracker服务的端口，java、php等客户端上传、下载、删除文件通过该端口
#8888是Nginx的端口，访问文件即通过该端口
```

#### 6. 查看fastdfs服务状态

```powershell
ps -ef| grep fdfs
#正常情况下应该出现类似以下内容
#root 7517  7466  0 Sep11 ? 00:00:18 /usr/bin/fdfs_trackerd etc/fdfs/tracker.conf
#root  7519  7466  0 Sep11 ?  00:00:14 /usr/bin/fdfs_storaged /etc/fdfs/storage.conf
#root  15881 14099  0 14:14 pts/1    00:00:00 grep --color=auto fdfs

#启用tracker服务（默认已启动，无需执行）
/usr/bin/fdfs_trackerd /etc/fdfs/tracker.conf
#启用storage服务（默认已启动，无需执行）
/usr/bin/fdfs_storaged /etc/fdfs/storage.conf
```

7.测试是否部署成功

```powershell
#进入容器内部
docker exec -it fastdfs /bin/bash
#测试上传文件测试
fdfs_upload_file /etc/fdfs/client.conf /usr/local/src/nginx-1.15.4.tar.gz
#若上传成功，应返回类似以下内容，一个带有路径的随机文件名
#group1/M00/00/00/rBEACF1543qAQrDMAA-itrfn0m4.tar.gz
#使用IP+8888/group1/M00/00/00/rBEACF1543qAQrDMAA-itrfn0m4.tar.gz即可访问或下载文件
#如：http://10.7.31.250:8888/group1/M00/00/00/rBEACF1543qAQrDMAA-itrfn0m4.tar.gz
```

#### 8.一些常用命令

```powershell
#搜索文件
find / -name storage.conf  

#导出镜像
docker save -o fastdfs.tar fastdfs:cp

#从本地文件加载镜像
docker load -i fastdfs.tar
```

---

*虽然本教程仅在CentOS下有效，这个是因为作者只考虑到了这个环境，也可以在CentOS做完镜像上传到dockerhub或是到处tar文件，然后就可以在其他的Docker主机中加载镜像了*

