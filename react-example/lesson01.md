---
title: React实例——使用React搭建订单页面Lesson01功能模块划分
date: 2016-07-02
project: react-example
git: https://github.com/403studio/react-example/lesson01/
excerpt: 使用React搭建订单页面Lesson01，对界面进行模块划分，构建界面基本框架
---

## 学习要点

* React环境搭建
* 使用Babel编译JSX文件
* 界面功能模块划分，并使用React实现界面的功能区域
* 注意：React的组件是大写字母开头的，而原生的HTML标签则是小写字母开头的
* React的编写还可以使用ES6的形式，在本实例中没有采用

## 工作准备

开发环境搭建可以参考[实例介绍](http://blog.403studio.com/react-example/)，在本实例中我们使用了NPM来安装React，使用Bootstrap提供样式表支持，使用Babel来编译JSX文件。首先我们得让我们的React正常运行起来。

index.html文件如下：
```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>React实例——使用React搭建订单页面Lesson01</title>
    <script type="text/javascript" src="./node_modules/react/dist/react.js"></script>
    <script type="text/javascript" src="./node_modules/react-dom/dist/react-dom.js"></script>
    <link href="https://cdn.bootcss.com/bootstrap/3.3.6/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
<div id="example"></div>
<script type="text/javascript" src="./lib/app.js"></script>
</body>
</html>
```

src/app.js(**请注意这里不是lib/app.js，我们把文件放在src中经过Babel的自动编译存放到lib文件处，所以在index.html中引用是通过lib/app.js**)文件如下：
```javascript
ReactDOM.render(
    <h1>Hello,world</h1>,
    document.getElementById('example')
);
```

在项目目录使用Babel命令`babel src --out-dir lib --presets react --watch`即可完成自动编译保存。一切配置正确的话，在浏览器中查看index.html就可以看到界面效果了。

## 模块划分

如图所示，我们将整个界面按照功能模块进行划分。模块划分在React中是非常重要的，合理的模块划分能够很便捷的实现我们的效果以及后期的维护

![界面展示](http://blog.403studio.com/react-example/img/lesson01-01.png)

* **OrderBox(灰色)：**订单页面盒子
* **OrderDetailContainer(深红色)：**订单明细
* **GoodsContainer(浅红色)：**商品信息
* **GoodsList(深绿色)：**商品列表
* **CategoryList(浅绿色)：**商品类目信息
* ......

*** 根据功能划分，来创建React的组件。我们来修改app.js来实现模块功能的划分

```javascript
var OrderDetailContainer = React.createClass({
    render: function() {
        return (
            <div className="col-md-4">OrderDetailContainer</div>
        );
    }
});

var GoodsContainer = React.createClass({
    render: function() {
        return (
            <div className="col-md-8">
                <CategoryList  />
                <FlagList />
                <GoodsList />
            </div>
        );
    }
});

var CategoryList = React.createClass({
    render: function() {
        return (
            <div>CategoryList</div>
        );
    }
});

var FlagList = React.createClass({
    render: function() {
        return (
            <div>FlagList</div>
        );
    }
});

var GoodsList = React.createClass({
    render: function() {
        return (
            <div>GoodsList</div>
        );
    }
});

var OrderBox = React.createClass({
    render: function() {
        return (
            <div className="row">
                <OrderDetailContainer />
                <GoodsContainer />
            </div>
        );
    }
})

ReactDOM.render(
    <OrderBox />,
    document.getElementById('example')
);
```
我们通过使用最简单的div布局来实现最原始的功能布局界面，达到我们的要求后就可以完善每个功能模块了。
