---
title: Markdown语法
date: 2019-11-04 20:53:51
tags: 杂
---

- ## 标题

直接通过#号的个数来控制标题级别

```
# 标题一
## 标题二
### 标题三
#### 标题四
```
**效果如下所示：**

# 标题一
## 标题二
### 标题三
#### 标题四

- ## 字体
```
*斜体*
**加粗**
***斜体加粗***
~~删除线~~
```
**效果如下所示：**
*斜体*
**加粗**
***斜体加粗***
~~删除线~~

- ## 图片
```
![棒棒哒](http://pydi4imsi.bkt.clouddn.com/1001.jpg "猫咪ok")
```
**效果如下所示：**
![棒棒哒](http://pydi4imsi.bkt.clouddn.com/1001.jpg "猫咪ok")

- ## 超链接
```
参考链接：[https://www.jianshu.com/p/191d1e21f7ed](https://www.jianshu.com/p/191d1e21f7ed)
```
**效果如下所示：**
参考链接：[https://www.jianshu.com/p/191d1e21f7ed](https://www.jianshu.com/p/191d1e21f7ed)

- ## 表格
```
|姓名|性别|年龄|
|:-|:--:|--:|   #|:-| 代表左对齐; :-:| 代表居中; -:| 代表右对齐
|张三|男|5|
|李四|男|10|
|小花|女|18|
```
**效果如下所示：**

| 姓名 | 性别 | 年龄 |
| :--- | :--: | ---: |
| 张三 |  男  |    5 |
| 李四 |  男  |   10 |
| 小花 |  女  |   18 |

- ## 引用
```
>你猜
>>你猜
```
**效果如下所示：**
>你猜
>
>>你猜

- ## 分割线
```
---
***
```
**效果如下所示：**

---
***

- ## 列表
### 有序列表
```
1. java
2. python
3. Go
4. C++
```
**效果如下所示：**
1. java
2. python
3. Go
4. C++

### 无序列表
```
- java
- python
- GO
- C++
```
**效果如下所示：**
- java
- python
- GO
- C++

### 列表嵌套
```
- java
   - java
   - python
   - Go
   - C++
      - java
      - python
      - Go
      - C++
- python
   1. java
   2. python
   3. Go
   4. C++
      1. java
      2. python
      3. Go
      4. C++
- Go
- C++
```
**效果如下所示：**
- java
   - java
   - python
   - Go
   - C++
      - java
      - python
      - Go
      - C++
- python
   1. java
   2. python
   3. Go
   4. C++
      1. java
      2. python
      3. Go
      4. C++
- Go
- C++

下一级列表缩减3个空格，与上一级列表进行区分

- ## 代码
（1）代码块：缩进 4 个空格或是 1 个制表符。效果如下：
（2）行内式：如果在一个行内需要引用代码，只要用反引号\`引起来就好（一般在ESC键下方，和~同一个键）
（3）多行代码块与语法高亮：在需要高亮的代码块的前一行及后一行使用三个单反引号“\`”包裹，就可以了。

```
`` #单行代码使用反引号即可
```

**效果如下所示：**

```
&#9786
```

- ## 特殊符号

（1）对于 Markdown 中的语法符号，前面加反斜线\即可显示符号本身。

（2）其他特殊字符，示例如下：

```
&#9813;
&#9784;
```
**效果如下所示：**

&#9813;
&#9784;

参考链接：[字符百科](https://unicode-table.com/cn/#2655)

- ## 常用技巧

- 字体、字号与颜色
```
<font color=#FF0000 size=4 face="黑体" >报错</font>    #color 设置颜色，size 设置字体大小（1~7），face 设置字体
<b>内容</b>    #加粗
<strong>内容</strong>    #强调
```
  **效果如下：**

  <font color=#FF0000 size=4 face="黑体" ><b><strong>报错</strong></b></font>

 可以配合markdown自己语言的加粗和斜体对字体进行设置。

  


- ## 流程图

```flow
st=>start: 开始
op=>operation: My Operation
cond=>condition: Yes or No?
op1=>operation: My Operation1
e=>end: 结果
st->op->op1->cond
cond(yes)->e
cond(no)->op
&
```

Typora 快捷键：[https://www.cnblogs.com/hongdada/p/9776547.html](https://www.cnblogs.com/hongdada/p/9776547.html)