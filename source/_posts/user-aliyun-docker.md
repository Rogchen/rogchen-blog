---

title: 使用私有服镜像仓库

tags:
	- aliyun
	- repository

---

1、登录仓库

`sudo docker login --username=chenhk128@163.com registry.cn-hangzhou.aliyuncs.com`

2、从Registry中拉取镜像

`sudo docker pull registry.cn-hangzhou.aliyuncs.com/rogchen/rogchen_public:[镜像版本号]`

3、将镜像推送到Registry

<!--more-->

```
--- [ImageId]换成自己的镜像 ---
 sudo docker login --username=chenhk128@163.com registry.cn-hangzhou.aliyuncs.com
 sudo docker tag [ImageId] registry.cn-hangzhou.aliyuncs.com/rogchen/rogchen_public:[镜像版本号]
 sudo docker push registry.cn-hangzhou.aliyuncs.com/rogchen/rogchen_public:[镜像版本号]
```

4、选择合适的镜像仓库地址

从ECS推送镜像时，可以选择使用镜像仓库内网地址。推送速度将得到提升并且将不会损耗您的公网流量。

如果您使用的机器位于VPC网络，请使用 registry-vpc.cn-hangzhou.aliyuncs.com 作为Registry的域名登录，并作为镜像命名空间前缀。


5、使用"docker tag"命令重命名镜像，并将它通过专有网络地址推送至Registry。

```
sudo docker images
	REPOSITORY                                                         TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
	registry.aliyuncs.com/acs/agent                                    0.7-dfb6816         37bb9c63c8b2        7 days ago          37.89 MB
 sudo docker tag 37bb9c63c8b2 registry-vpc.cn-hangzhou.aliyuncs.com/acs/agent:0.7-dfb6816
使用"docker images"命令找到镜像，将该镜像名称中的域名部分变更为Registry专有网络地址。

 sudo docker push registry-vpc.cn-hangzhou.aliyuncs.com/acs/agent:0.7-dfb6816
```
