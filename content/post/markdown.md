---
title: "markdown基本写法"
date: 2018-07-09T18:17:05+08:00
categories: [markdown]
tags: [markdown]
TableOfContents: true
---
> Markdown是一种可以使用普通文本编辑器编写的标记语言，通过简单的标记语法，它可以使普通文本内容具有一定的格式。
>Markdown具有一系列衍生版本，用于扩展Markdown的功能（如表格、脚注、内嵌HTML等等），这些功能原初的Markdown尚不具备，它们能让Markdown转换成更多的格式，例如LaTeX，Docbook。Markdown增强版中比较有名的有Markdown Extra、MultiMarkdown、 Maruku等。这些衍生版本要么基于工具，如Pandoc；要么基于网站，如GitHub和Wikipedia，在语法上基本兼容，但在一些语法和渲染效果上有改动。

标题
----

# 标题 H1
## 标题 H2
### 标题 H3
#### 标题 H4
##### 标题 H5
###### 标题 H6
```markdown
# 标题 H1
## 标题 H2
### 标题 H3
#### 标题 H4
##### 标题 H5
###### 标题 H6
```
下划线
-----

 ----
 ```markdown
----
```
表格
-----
| 水果     | 价格    |  数量  |
| ------- | -----:   | :----: |
| 香蕉    | $1      |   5    |
| 苹果    | $1      |   6    |
| 草莓    | $1      |   7    |
```markdown
| 水果     | 价格    |  数量  |
| ------- | -----:   | :----: |
| 香蕉    | $1      |   5    |
| 苹果    | $1      |   6    |
| 草莓    | $1      |   7    |

```
文本强调
----
*斜体* or _强调_

**加粗** or __加粗__

***粗斜体*** or ___粗斜体__

```markdown
*斜体* or _强调_
**加粗** or __加粗__
***粗斜体*** or ___粗斜体__
```
Lists 列表
-----
##### Unordered 无序列表
* 无序列表
* 子项
* 子项
 
+ 无序列表
+ 子项
+ 子项
 
- 无序列表
- 子项
- 子项

```markdown
* 无序列表
* 子项
* 子项
 
+ 无序列表
+ 子项
+ 子项
 
- 无序列表
- 子项
- 子项

```
#####  Ordered 有序列表：
1. 第一行
2. 第二行
3. 第三行
 
* 第一行
    - 第二行
    - 第三行

```go
1. 第一行
2. 第二行
3. 第三行
 
* 第一行
    - 第二行
    - 第三行
```

#####  组合
* 产品介绍（子项无项目符号）
    此时子项，要以一个制表符或者4个空格缩进
 
* 产品特点
    1. 特点1
        - 特点2
        - 特点3
* 产品功能
    1. 功能1
        - 功能2
        - 功能3

```go
* 产品介绍（子项无项目符号）
    此时子项，要以一个制表符或者4个空格缩进
 
* 产品特点
    1. 特点1
        - 特点2
        - 特点3
* 产品功能
    1. 功能1
        - 功能2
        - 功能3
```
Links 连接（title为可选项）：
-----
##### Inline-style 内嵌方式：

[W3Cschool](http://www.w3cschool.cn/ "W3Cschool")
```go
[W3Cschool](http://www.w3cschool.cn/ "W3Cschool")
```

##### Reference-style 引用方式：

[链接文字][id]
[id]: http://www.w3cschool.cn/ "标题文字"

```go
[链接文字][id]
[id]: http://www.w3cschool.cn/ "标题文字"
```

##### Relative reference to a repository file 引用存储文件：

[链接文字](../path/file/readme.text "标题文字")
```go
[链接文字](../path/file/readme.text "标题文字")
```

##### 还能这样使用：

[链接文字][]
[链接文字]: http://www.w3cschool.cn/

```go
[链接文字][]
[链接文字]: http://www.w3cschool.cn/
```
##### Email 邮件：

<example@w3cschool.cn>
```go
<example@w3cschool.cn>
```
Images 图片
----
##### Inline-style 内嵌方式：
![替代文字](https://user-images.githubusercontent.com/8891076/42434621-73549158-8386-11e8-8224-9f4fec7d62b1.jpg "标题文字")

```

![替代文字](https://user-images.githubusercontent.com/8891076/42434621-73549158-8386-11e8-8224-9f4fec7d62b1.jpg "标题文字")

```

Blockquotes 引用
----
>Blockquotes 引用

```go
>Blockquotes 引用
```