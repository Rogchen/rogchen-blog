
---

title: vim使用

tags: vim

---

# 入门操作

## 1、输入i进入编辑模式，按下esc/ctrl+c 输入:wq 保存，q！不保存强制退出，wq！强制保存

# 操作提升

## 2、 :set showmode+回车 即可显示提示。

## 3、粘贴复制删除等操作

```

----命令模式：----
yy	复制光标所在行
2yy	复制光标所在行开始的2行
p	粘贴
dd	删除光标所在行（实际是剪切）
3dd	删除光标所在行开始的3行（剪切）
x	删除当前光标所在的字符
X（大写)	删除当前光标前一个字符


```
<!--more-->

## 4、光标的移动

```

----命令模式：----
 
h 左移   j 下移 k 上移  l 右移 （四个箭头也可以上下左右移动）
H 当前屏幕第一行
M 当前屏幕中间
L 当前屏幕最后一行
ctrl+f  往下翻页
ctrl+b  网上翻页
G       定位到最后一行（整个文档最后一行）
5G      定位到第5行
gg      定位到第一行（整个文档第一行）
w       跳到下一个单词
b       跳到上一个单词
{       移动到上一个代码块
}       移动到下一个代码块
v、V    先按v再用上下箭头选中几行，再>         这几行集体右移，再.继续右移 
v、V    先按v再用上下箭头选中几行，再<         这几行集体左移，再.继续左移 

```

## 5、底行模式

```
----底行模式：----
 
q！                不保存退出
wq                 保存退出
 
%s/abc/123/g       把全文的abc替换成123
1,10s/abc/123/g    把1-10行的abc替换成123

```

## 6、其他

```
----命令模式：----
 
u            撤销
ctrl+r       反撤销（撤销u操作）
r            修改当前字符
R            从当前位置开始修改，按Esc返回
/            向下搜索，先输入/,再输入搜索内容+回车。n、N表示上（下）一个搜索结果
?			 向上搜索，先输入？,再输入搜索内容+回车。n、N表示上（下）一个搜索结果

```

# 操作提升

## 7、批量插入、删除

```

----插入模式：----
v	进入可视界面，通过上下选中需要操作的行
ctrl+v	进行可视化块选中
I	输入大写I进入操作界面，输入#或者//进行注释
esc	连续按两下esc，完成操作

----删除模式：----
ctrl +v 进入块操作，选中你要删除的字符/行
d	单个d删除字符，dd删除行

```

## 8、 删除

对3的补充

```
dd	删除一行
ndd	删除以当行开始的n行
dw	删除以当前字符开始的一个字符，比如：abc,dwt。光标在a，dw就删除abc
ndw	删除以当前字符开始的n个字符
d$、D	删除以当前字符开始的一行字符
d)	删除到下一句的开始
d}	删除到下一段的开始
d回车	删除2行
全部删除：按esc后，然后dG

```

## 9、复制多行

```

方法1：（强烈推荐）
：9，15 copy 16  或 ：9，15 co 16	将第9行至第15行的数据，复制到第16行
：9，15 move 16  或 :9,15 m 16 		将第9行到第15行的文本内容到第16行的后面 

方法2：
光标移动到结束行，ma
光标移动到起始行,输入y'a
光标移动到需要复制的行，输入p,行前复制则输入大写P


全部复制：按esc后，然后ggyG
全选高亮显示：按esc后，然后ggvG或者ggVG

```


