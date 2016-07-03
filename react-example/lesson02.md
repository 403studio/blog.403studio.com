---
title: React实例——使用React搭建订单页面Lesson02完善商品组件静态内容布局
link: lesson02
date: 2016-07-03
project: react-example
git: https://github.com/403studio/react-example/lesson02/
excerpt: 使用React搭建订单页面Lesson02，完善商品组件静态内容布局。使用Bootstrap提供样式表支持，用静态数据实现商品组件的布局
---

## 学习要点

* React组件嵌套
* 使用Bootstrap搭建界面布局，没有使用过Bootstrap的看官强烈建议去学习了解下。
* 在某些地方我们开始使用了自定的样式，具体请查看样式表文件

## 功能实现

### 商品分类组件

每个商品都有自己的分类，通过分类我们可以很便捷的找到对应的商品。在本实例中商品分类没有做多级，只有一级分类。

```javascript
var CategoryList = React.createClass({
    render: function() {
        return (
            <div className="col-md-12" id="category-list">
                <span className="btn btn-lg btn-danger">最热</span>
                <span className="btn btn-lg btn-danger">推荐</span>
                <span className="btn btn-lg btn-success">奶茶</span>
                <span className="btn btn-lg btn-success">咖啡</span>
            </div>
        );
    }
});
```

### 商品标签组件

在订单时，很多商品有一些自定义的组件。例如奶茶店：有加冰，少冰；快餐店：微辣，不要味精等，默认情况是没有标签的。

```javascript
var FlagList = React.createClass({
    render: function() {
        return (
            <div className="col-md-12 block" id="flag-list">
                <span className="btn btn-sm btn-warning">常温</span>
                <span className="btn btn-sm btn-warning">加冰</span>
                <span className="btn btn-sm btn-warning">加热</span>
            </div>
        );
    }
});
```

### 商品列表组件

```javascript
var GoodsList = React.createClass({
    render: function() {
        return (
            <div className="col-md-12 block" id="goods-list">
                <div className="goods-detail btn btn-default">
                    <span>燕麦兰香</span>
                </div>
                <div className="goods-detail btn btn-default">
                    <span>生生乌龙</span>
                </div>
                <div className="goods-detail btn btn-default">
                    <span>幽兰拿铁</span>
                </div>
                <div className="goods-detail btn btn-default">
                    <span>卡布奇诺</span>
                </div>
            </div>
        );
    }
});
```

### 订单明细组件

```javascript
var OrderDetailContainer = React.createClass({
    render: function() {
        return (
            <div className="col-md-4">
                <div className="panel panel-default">
                    <div className="panel-heading">订单明细</div>
                    <div className="panel-body" id="order-detail-container">
                        <div className="row order-goods-detail">
                            <div className="col-md-4">深深乌龙</div>
                            <div className="col-md-2">1</div>
                            <div className="col-md-3">12</div>
                            <div className="col-md-3">12.00</div>
                            <div className="col-md-12 text-right">
                                <small className="btn btn-tiny btn-warning">加冰</small>
                                <small className="btn btn-tiny btn-warning">常温</small>
                            </div>
                        </div>

                        <div className="row">
                            <div className="col-md-12 text-right">
                                合计:32.00
                            </div>
                        </div>

                        <div className="row">
                            <button className="btn btn-success">结账出单</button>
                        </div>
                    </div>
                </div>
            </div>
        );
    }
});
```

## 总结说明

通过上面的组件代码修改一个简单的静态页面就能够展示出来了，到这里我们还无法了解到React的强大，毕竟我们所做的工作还只是页面组件布局。本节完整的代码，请查看[GitHub](https://github.com/403studio/react-example/lesson02/)上的代码库。
