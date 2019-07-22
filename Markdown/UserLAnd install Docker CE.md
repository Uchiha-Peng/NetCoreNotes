1. 卸载旧版本(若以前安装过)
```
sudo apt-get remove docker docker-engine docker.io containerd runc
```

2. 更新apt列表
```
sudo apt-get update
```

3. 安装包以允许apt通过HTTPS使用存储库：
```
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
```
4. 添加Docker的官方GPG密钥
```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

sudo apt-key fingerprint 0EBFCD88
```

5.将docker存储库加入 vi /etc/apt/sources.list中
```
sudo add-apt-repository \
   "deb [arch=arm64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```
这一步常常会报错，看了网上的资料，可以手动添，需要安装vim
```
sudo apt-get install vim
```
安装完成后，手动编辑文件
```
sudo vi /etc/apt/sources.list
```
打开改文件后，为只读状态，输入**i**即可进行编辑，在最后加入一行，我这里是**arm64**，另外**bionic**也需要依据实际情况修改，执行`sudo lsb_release -cs`命令，将结果替换为我这里的**bionic**即可，修改完成后，按**ESC**键，输入**:wq**即可保存，输入**:q!**退出不保存
```
deb [arch=arm64] https://download.docker.com/linux/ubuntu bionic stable
```

6. 再次更新apt列表，开始安装
```
sudo apt-get update

sudo apt-get install docker-ce docker-ce-cli containerd.io
```

7. 查看可用的版本列表
```
$ apt-cache madison docker-ce
```

8. 根据版本列表，选择一个版本安装
```
sudo apt-get install docker-ce=5:18.09.8~3-0~ubuntu-bionic docker-ce-cli=5:18.09.8~3-0~ubuntu-bionic containerd.io
```