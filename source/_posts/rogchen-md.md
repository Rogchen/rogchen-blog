---
title: Rogchen-md学习
tags:
   md
---

# 一级标题
## 二级标题
### 三级标题
###### 共有六级标题
 
---
### 1、分割线--\就是转义
使用横线（\---上换行或者\***或者\___）
***
### 2、代码块->单行\`\`
`代码块输出使用"``"`

### 3、代码块->多行\`\`\`
``` 这里可以注释
<p>abc</p>
<p>bbb</p>
```
### 4、使用大于号">"表示引用
> * 一级引用，不以结婚为目的的恋爱都是耍流氓
>> * 二级引用
>>> * 三级引用

<!--more-->

### 5、引用图片 \![title](url)
![rogchen](G:CHK/Desktop/ip.pn)

> 也是支持参数式 []()
>> [rogchen]:(http://baidu.com)
>> 图片的参数式![rogchen]
### 6、无序列表使用-、+或*
- 列表一
* 列表二
+ 列表三

### 7、有序列表：有序列表的序号是根据第一行列表的数字顺序来的
3. 列表三
6. 列表四
7. 列表五

### 8、强调：粗体和斜体->一个星号或者是一个下划线包起来，会转换为<em>倾斜，如果是2个，会转换为<strong>加粗

*两个星号(\*)包含一段文本就是粗体的语法*

_用一个下划线(\_)包含一段文本就是斜体的语法_

**加粗<strong>**

### 9、表格

| table |table1 |
| ----- |:-----:|
| 123   |fwer   |

### 10、行内式：直接嵌套->使用链接
[rogchen](www.baidu.com)

### 11、参数式: 占位符->支持title
[name]: http://www.baidu.com "百度"
[home]: http://google.com "谷歌"
[支持的哦]: /name/home "瞎写的"
这里是[name]，这里是[home]，这也是[支持的哦]
>
> [foo1]: http://example.com/ "Optional Title Here"
> [foo2]: http://example.com/ 'Optional Title Here'
> [foo3]: http://example.com/ (Optional Title Here)
> [foo4]: <http://example.com/> "推荐使用这种"
> 这里也是可[foo1]，使用[foo2]，使用[foo3]，使用[foo4]

### 11、转义
> * \\
> * \`
> * \*
> * \/

### 12、删除线使用双\~~
~~删除线~~
