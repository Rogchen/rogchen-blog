title: windows通过命令获取连接过的所有wifi和密码

tags:
	[wifi,cmd]
	
---

* 在windows 打开dos（win+r）输入下面命令就列出很多行 你以前连接过的wifi信息、加密方式、包括密码
```
for /f "skip=9 tokens=1,2 delims=:" %i in ('netsh wlan show profiles') do  @echo %j | findstr -i -v echo | netsh wlan show profiles %j key=clear
```