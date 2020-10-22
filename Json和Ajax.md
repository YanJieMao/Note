

# Ajax和JSON



## web1.0时代

登录，如果失败，需要刷新才能重新登录

现在大多数网站都是局部刷新，不刷新整个页面的情况，实现页面更新

## web2.0时代

最重要的因素ajax

## JSON

[JSON](https://baike.baidu.com/item/JSON)([JavaScript](https://baike.baidu.com/item/JavaScript) Object Notation, JS 对象简谱) 是一种轻量级的数据交换格式。

简洁和清晰的层次结构使得 JSON 成为理想的数据交换语言。 易于人阅读和编写，同时也易于机器解析和生成，并有效地提升网络传输效率。

前后端分离，数据交互变得很重要

```html
<script type="text/javascript">
    //编写一个对象
    var user = {
        name:"郝大胖",
        age:3,
        sex:"男"
    };
    //输出这个对象
    console.log(user);
    //将js对象转为json字符串
    var str = JSON.stringify(user);
    console.log(str);//{"name":"郝大胖","age":3,"sex":"男"}
    var obj = JSON.parse(str);
    console.log(obj);


</script>
```















































## 简介

AJAX = Asynchronous JavaScript and XML（异步的 JavaScript 和 XML）。