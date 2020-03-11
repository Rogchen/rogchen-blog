
---

title: linux不起服务占用一个端口

tags: linux

---

使用nc命令占用端口


```

nc -l 8888    ####启用TCP端口，可以直接telnet通
在其他主机上使用nc测试
nc -v -w 1 192.168.1.1 -z 8888
结果
Connection to 192.168.1.1 8888 port [tcp/ddi-udp-1] succeeded!

nc -l -u 8888 ####启用UDP端口，无法telnet通，可以使用nc检测。-u表示使用UDP协议
nc -v -w 1 192.168.1.1 -u -z 8888
结果
Connection to 192.168.1.1 8888 port [udp/ddi-udp-1] succeeded!


```
