---
title: Markdown 语法学习
tags:
    - Markdown
---

# Markdown学习





## 标题
```markdown
# 标题 （#加空格，几个#号就是几号标题）
```
# 一级标题

## 二级标题

### 三级标题

#### 四级标题

##### 五级标题

###### 六级标题



##  字体

```markdown
加粗 ： **hello**
```

**hello**

```markdown
斜体 ： *hello*
```
*hello*
```markdown
斜体加粗 ： ***hello***
```
***hello***
```markdown
删除线 ： 两个~hello两个~
```
~~hello~~



##  引用

~~~markdown
> 输入文字
~~~

> 若无闲事挂心头



## 分割线
~~~markdown
---
~~~
---
或者
~~~markdown
***
~~~
***


## 图片
~~~markdown
![图片名字](图片地址本地或者网络地址都可以)
~~~
~~~markdown
![v](img/v.jpg)
~~~
![v](img/v.jpg)

~~~markdown
![百度图片](https://ss2.bdstatic.com/70cFvnSh_Q1YnxGkpoWK1HF6hhy/it/u=2091711702,2468700162&fm=11&gp=0.jpg)
~~~

![百度图片](https://ss2.bdstatic.com/70cFvnSh_Q1YnxGkpoWK1HF6hhy/it/u=2091711702,2468700162&fm=11&gp=0.jpg)



## 超链接

```markdown
[百度](www.baidu.com)
```

[百度](www.baidu.com)





## 列表

### 有序列表
直接输入1、2、3
1. A
2. B
3. C



### 无序列表
```markdown
- 1
- 2
- 3

```

- 1
- 2
- 3



## 表格
```markdown
名字|性别|年龄
--|--|--|
张三|男|29

```

名字|性别|年龄
--|--|--|
张三|男|29





## 代码



```markdown
​```代码种类 例如 java
代码
​```

```




```java
class Javahelloworld {
    public static void main(String args[]){
        System.out.println("hello world\n");
    }
}
```

在typpra中按shift+tab键 代码格式化
