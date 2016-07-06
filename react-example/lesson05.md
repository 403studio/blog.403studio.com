---
title: React实例——使用React搭建订单页面Lesson05商品列表添加事件响应
link: lesson05
date: 2016-07-05
project: react-example
excerpt: 使用React搭建订单页面05商品列表添加事件响应，将事件的响应处理集中放在一个地方然后通过props传递给子组件是一个很好的方式。在交互界面，对事件进行响应处理并更新界面是WebAPP应用最多的地方。在React中我们只需要关心并处理好事件响应，界面的处理由React自动完成更新处理。很多时候事件的响应处理涉及数据的更新，所以对于这类事件我们一般把处理方法与state放在同一个地方
---

## 学习要点

* 这本节我们会考虑到事件冒泡的处理
* 事件直接响应方法都会接收到e参数，通过这个参数我们可以获取到发生事件的节点信息
* 很多时候我们不能使用state的数据，因为这样做事很危险的。一处改变，处处改变这个是state的逻辑。所以在使用和改变state的时候应特别小心。
* array的filter,find,forEach等的使用

## 功能说明

### 点击商品列表的商品，相当于把商品添加到购物车，同步更新订单明细组件。先来处理点击事件

商品点击事件发生，我们只需要知道哪个商品及商品的id即可。在事件处理函数我们可以通过商品id在商品信息中查到所有的相关信息，在orderBox中添加处理函数并通过props一直传递给GoodsList

```javascript
handleGoodsClick: function(goods_id) {
    var goodsClick = this.state.goods.find(function(value, index, arr) {
        return (value.id == goods_id);
    });
    console.log(goodsClick);
},
```

```javascript
var GoodsList = React.createClass({
    onClick: function(e) {
        var goods_id = e.target.getAttribute("data-id");
        this.props.onGoodsClick(goods_id);
    },

    render: function() {
        var goodsNode = this.props.goods.map(function(goods) {
            var filterCategory = this.props.filterCategory;
            if (filterCategory != 0 && filterCategory != goods.category_id) {return}
            return (
                <div onClick={this.onClick} data-id={goods.id} key={goods.id} className="goods-detail btn btn-default">
                    <span>{goods.goods_name}</span>
                </div>
            );
        }.bind(this));
        return (
            <div className="col-md-12 block" id="goods-list">
                {goodsNode}
            </div>
        );
    }
});
```

到这一步，使用Google的开发者工具在Console我们可以发现点击商品有时候能够打印出我们想要的结果，有时候却是undefined。造成这个的原因是我们点击的商品的名称，而这个节点是用span标签并放置在div标签内的，我们的onClick事件是直接处理的div，因此我们点击它的子节点onClick事件也会响应。所以我们需要考虑这种情况，我么把onClick简单修改一下即可。

```javascript
var GoodsList = React.createClass({
    onClick: function(e) {
        var nodeName = e.target.nodeName;
        var divNode = e.target;
        if (nodeName == "SPAN") {
            divNode = e.target.parentNode;
        }
        var goods_id = divNode.getAttribute("data-id");
        this.props.onGoodsClick(goods_id);
    },

    render: function() {
        var goodsNode = this.props.goods.map(function(goods) {
            var filterCategory = this.props.filterCategory;
            if (filterCategory != 0 && filterCategory != goods.category_id) {return}
            return (
                <div onClick={this.onClick} data-id={goods.id} key={goods.id} className="goods-detail btn btn-default">
                    <span>{goods.goods_name}</span>
                </div>
            );
        }.bind(this));
        return (
            <div className="col-md-12 block" id="goods-list">
                {goodsNode}
            </div>
        );
    }
});
```

这样我们就正确的把商品的id传递给响应函数了，接下来我们要做的就是修改订单明细组件让其根据点击的商品来显示明细信息。我们把这个会变动的订单明细信息通过orderGoods保存在state里面

```javascript
handleGoodsClick: function(goods_id) {
    var goodsClick = this.state.goods.find(function(value, index, arr) {
        return (value.id == goods_id);
    });
    var orderGoods = this.state.orderGoods;
    var time = (new Date()).getTime();

    var orderGoodsNew = {
        key: time, 
        id: goodsClick.id,
        goods_name: goodsClick.goods_name,
        barcode: goodsClick.barcode,
        plucode: goodsClick.plucode,
        category_id: goodsClick.category_id,
        category_name: goodsClick.category_name,
        price: goodsClick.price,
        unit: goodsClick.unit,
        number: 1,
    };
    this.setState({orderGoods: orderGoods.concat(orderGoodsNew)});
    console.log(this.state.orderGoods);
},
```

在上面的代码我创建了一个变量orderGoodsNew，一开始我是直接使用的`goodsClick，goodsClick.key = time; {orderGoods: orderGoods.concat(goodsClick)}`，读者可以自己去体验下，结果是怎么样。为了解决我这个问题，我后来查了官方文档和stackoverflow。

我们需要为每个订单明细的商品添加一个唯一的key，这也是React动态显示信息必须的。所以我使用了time来生成这个唯一的key

### 根据点击的商品来显示订单明细信息

把订单明细信息保存到state，通过props传递给子组件

```javascript
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
        filterCategory: 0,
        orderGoods: [],
    }
},

<OrderDetailContainer orderGoods={this.state.orderGoods} />
```
最后我们在订单明细组件，让它根据state的数据来进行界面的渲染

```javascript
var OrderDetailContainer = React.createClass({
    render: function() {
        var orderGoodsNode = this.props.orderGoods.map(function(goods) {
            return (
                <div className="row order-goods-detail" key={goods.key} >
                    <div className="col-md-4">{goods.goods_name}</div>
                    <div className="col-md-2">1</div>
                    <div className="col-md-3">{goods.price}</div>
                    <div className="col-md-3">{goods.price * goods.number}</div>
                </div>
            );
        }.bind(this));

        return (
            <div className="col-md-4">
                <div className="panel panel-default">
                    <div className="panel-heading">订单明细</div>
                    <div className="panel-body" id="order-detail-container">
                        {orderGoodsNode}
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

实现商品的点击事件处理后，我们还需要为商品的标签添加处理。每个商品添加到订单明细的时候都可能有特定的标签。

### 标签的点击事件处理

标签默认都是没有被选中的，我们可以为标签添加一条checked属性来进行标记。标签被选中后通过改变其样式来展示进行状态的区别，并修改state里对应标签的checked属性。

```javascript
handleFlagClick: function(key) {
    var flag = this.state.flag;
    var flagClick = flag.find(function(value, index, arr) {
        return (value.key == key);
    });
    flagClick.checked = !flagClick.checked;
    this.setState({flag: flag});
},

...
<GoodsContainer onFlagClick={this.handleFlagClick} onGoodsClick={this.handleGoodsClick} onCategoryClick={this.handleCategoryClick} filterCategory={this.state.filterCategory} goods={this.state.goods} category={this.state.category} flag={this.state.flag} />
...

var FlagList = React.createClass({
    onClick: function(e) {
        var nodeName = e.target.nodeName;
        var divNode = e.target;
        if (nodeName == "SPAN") {
            divNode = e.target.parentNode;
        }
        var key = divNode.getAttribute("data-key");
        this.props.onFlagClick(key);
    },

    render: function() {
        var flagNode = this.props.flag.map(function(flag) {
            var spanStyle = flag.checked ? {display: "inline"} : {display: "none"};
            return (
                <div className="btn btn-sm btn-warning" data-key={flag.key} key={flag.key} onClick={this.onClick}>
                    <span>{flag.value}</span>
                    <span style={spanStyle} className="text-success glyphicon glyphicon-ok"></span>
                </div>
            );
        }.bind(this));
        return (
            <div className="col-md-12 block" id="flag-list">
                {flagNode}
            </div>
        );
    }
});
```

### 把标签处理添加到商品点击事件函数中

商品点击事件，我们要查看哪些标签被选中了，这些标签是需要添加到明细中的。在处理完后，我们还需要恢复标签的初始状态。

```javascript
handleGoodsClick: function(goods_id) {
    var goodsClick = this.state.goods.find(function(value, index, arr) {
        return (value.id == goods_id);
    });
    var orderGoods = this.state.orderGoods;
    var time = (new Date()).getTime();
    var flag = this.state.flag.filter(function(flag) {
        return (flag.checked === true);
    });
    var flagNew = flag.map(function(flag) {
        return ({key: flag.key, value: flag.value, checked: flag.checked})
    });

    var orderGoodsNew = {
        key: time, 
        id: goodsClick.id,
        goods_name: goodsClick.goods_name,
        barcode: goodsClick.barcode,
        plucode: goodsClick.plucode,
        category_id: goodsClick.category_id,
        category_name: goodsClick.category_name,
        price: goodsClick.price,
        unit: goodsClick.unit,
        number: 1,
        falg: flagNew,
    };
    this.setState({orderGoods: orderGoods.concat(orderGoodsNew)});
    this.state.flag.forEach(function(flag) {
        flag.checked = false;
    });
},
```

## 说明

state的使用和修改时特别需要注意的地方，“一处改变，处处生效”state的逻辑要时刻谨记。javascript事件冒泡和继承是在做事件响应处理的时候也需要时刻记住的。