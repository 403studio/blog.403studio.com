---
title: React实例——使用React搭建订单页面Lesson03使用数据填充静态页面
link: lesson03
date: 2016-07-03
project: react-example
git: https://github.com/403studio/react-example/lesson03/
excerpt: 使用React搭建订单页面Lesson03使用数据填充静态页面，使用props,state来对数据进行追踪填充
---

## 学习要点

* React中Props和State的使用
* 在一个统一的地方管理State然后通过props传递给子组件
* 传递javascript变量在jsx里面是要用{}包住的
* ECMAScript6 为javascript添加了很多新的语法特性，例如数组的map,find, findIndex等。在接下来的实例中会用到这些知识，这些知识并不难理解很多从语义结构上就能够明白是什么意思。如果想进一步了解的话可以访问[ECMSScript入门](http://es6.ruanyifeng.com/)这个是国内开发者编写的，或者直接去官网查看文档。
* 使用map方法生成React控件的时候，我们需要为每个节点添加一个key属性，否则React会报错。因为React需要根据这个唯一的key来查找节点

## 说明

>
props 是不可变的：它们从父级传来并被父级“拥有”。为了实现交互，我们给组件引进了可变的 state。this.state 是组件私有的，可以通过调用 this.setState() 改变它。每当state更新，组件就重新渲染自己。
render() 方法被声明为一个带有 this.props 和 this.state 的函数。框架保证了 UI 总是与输入一致。

在这节里面我们开始使用到React的一些强大功能，开始与数据打交道。可以这样简单的理解：我们通过state来保存数据，并通过setState来修改数据，通过props来传递数据。所以state的存放位置是很关键的。基于这种数据的保存流动方式在大交互的界面并不是很好用的，后来又出现了结合flux,redux的解决方案，有兴趣的可以上网搜索了解下。正式生产环境有很多人采用了React+Redux的解决方案。

如果对props和state没有任何概念的话，可以先去[官方网站](https://facebook.github.io/react/docs/tutorial-zh-CN.html)读一下文档（React官方本身提供了中文文档，只需要在文件名后面添加-zh-CN即可读到中文文档）文档里面有一个简单的示例来介绍使用React。

在我自己的项目里面，数据是通过Ajax的方式从数据库获取。这也是大多数的页面解决方案，在本实例中我是采用的静态数据。

## 功能说明

### state存放初始数据

我们把数据的初始化放在最外层的组件OrderBox里面，如果想要从服务器数据库获取数据的话，可以把被注释的布冯打开，然后按格式返回需要的数据即可。

```javascript
var OrderBox = React.createClass({
    getInitialState: function() {
        return {
            goods: [
                {id: "1", goods_name: "青草悠然", barcode: "0", plucode: "0", category_id: "1", category_name: "奶茶", price: "15", unit: "杯"},
                {id: "2", goods_name: "燕麦兰香", barcode: "0", plucode: "0", category_id: "1", category_name: "奶茶", price: "12", unit: "杯"},
                {id: "3", goods_name: "幽兰拿铁", barcode: "0", plucode: "0", category_id: "1", category_name: "奶茶", price: "15", unit: "杯"},
                {id: "4", goods_name: "声声乌龙", barcode: "0", plucode: "0", category_id: "1", category_name: "奶茶", price: "14", unit: "杯"},
                {id: "5", goods_name: "卡布奇诺", barcode: "0", plucode: "0", category_id: "2", category_name: "咖啡", price: "18", unit: "杯"}
            ],
            category: [
                {id: "1", parent_id: "0", category_name: "奶茶", open: "0"},
                {id: "2", parent_id: "0", category_name: "咖啡", open: "0"}
            ], 
            flag: [
                {key: 1, value: "常温"}, 
                {key: 2, value: "加冰"},
                {key: 3, value: "加热"}
            ],
        }
    },

    /**这里是采用jQuery的Ajax从服务器获取数据this.props.url修改成实际的地址就可以
    componentDidMount: function() {
        $.ajax({
            url: this.props.url,
            dataType: "json",
            cache: false,
            success: function(data) {
                this.setState({goods: data.goods});
                this.setState({category: data.category});
            }.bind(this),
            error: function(xhr, status, err) {
                console.error(this.props.url, status, err.toString);
            }.bind(this)
        });
    },
    **/

    render: function() {
        return (
            <div className="row">
                <OrderDetailContainer />
                <GoodsContainer goods={this.state.goods} category={this.state.category} flag={this.state.flag}  />
            </div>
        );
    }
});
```

数据弄好后，我们就可以通过props来传递需要的数据给组件了，`this.state.`来获取数据。`<GoodsContainer goods={this.state.goods} category={this.state.category} flag={this.state.flag}  />`这样商品列表组件的数据就传递过去了。在商品列表组件里面通过`this.props.goods, this.props.category`就能够获取到传递的数据。

### 修改商品类目组件，根据数据生成我们需要的内容

在GoodsContainer组件里面我们需要把数据进一步传递给他的子组件

```javascript
var GoodsContainer = React.createClass({
    render: function() {
        return (
            <div className="col-md-8">
                <CategoryList category={this.props.category}  />
                <FlagList flag={this.props.flag} />
                <GoodsList goods={this.props.goods} />
            </div>
        );
    }
});
```

在接下来我们会使用javascript的array.map方法，这个方法会给原数组的每个元素调用一次callback函数。另外在使用map方法生成React控件的时候，我们需要为每个节点添加一个key属性，否则React会报错。因为React需要根据这个唯一的key来查找节点

```javascript
var CategoryList = React.createClass({
    render: function() {
        var categoryNode = this.props.category.map(function(category) {
            return (<span key={category.id} className="btn btn-lg btn-success">{category.category_name}</span>);
        });
        return (
            <div className="col-md-12" id="category-list">
                <span className="btn btn-lg btn-danger">最热</span>
                <span className="btn btn-lg btn-danger">推荐</span>
                {categoryNode}
            </div>
        );
    }
});
```

以此类推生成我们需要的标签组件和商品组件：

```javascript

var FlagList = React.createClass({
    render: function() {
        var flagNode = this.props.flag.map(function(flag) {
            return (<span key={flag.key} className="btn btn-sm btn-warning">{flag.value}</span>);
        });
        return (
            <div className="col-md-12 block" id="flag-list">
                {flagNode}
            </div>
        );
    }
});

var GoodsList = React.createClass({
    render: function() {
        var goodsNode = this.props.goods.map(function(goods) {
            return (
                <div key={goods.id} className="goods-detail btn btn-default">
                    <span>{goods.goods_name}</span>
                </div>
            );
        });
        return (
            <div className="col-md-12 block" id="goods-list">
                {goodsNode}
            </div>
        );
    }
});
```

## 说明

通过上面的几步，我们基本就实现了根据数据来展现界面。可以发现React还是很容易上手的，数据传递生成组件最后渲染。真正神奇的地方在渲染，在本节还没有使用要数据更新和界面交互，我会在下一节开始使用这些工具。

是React我们需要对功能组件做很好的划分，第一步做好了后面的工作都好做了。同时React的官方文档上面提供了两个入门的实例，可以一步一步的带领进入React的殿堂。