---
title: React实例——使用React搭建订单页面Lesson06订单明细列表处理
link: lesson06
date: 2016-07-07
project: react-example
excerpt: 使用React搭建订单页面Lesson06订单明细列表处理，在本节将完成本实例的基本功能。订单明细列表实现删除某个商品的功能，实现金额统计的功能。
---

## 功能说明

### 订单明细列表，商品删除功能和统计功能

我们需要为订单明细列表的每条商品添加删除的功能（在本实例中没有实现编辑功能，编辑通过先删除后添加来实现）。我们只需要为每条商品添加点击事件，并更新state里orderGoods数据即可。

```javascript
handleOrderDetailDelClick: function(key) {
    var orderGoods = this.state.orderGoods;
    var goodsClickKey = orderGoods.findIndex(function(value, index, arr) {
        return (value.key == key);
    });
    orderGoods.splice(goodsClickKey, 1);
    this.setState({orderGoods: orderGoods});
},

<OrderDetailContainer onOrderDetailDelClick={this.handleOrderDetailDelClick} orderGoods={this.state.orderGoods} />

......

var OrderDetailContainer = React.createClass({
    onDelClick: function(e) {
        var key = e.target.getAttribute("data-key");
        this.props.onOrderDetailDelClick(key);
    },

    countAmount: (function() {
        var sum = 10;
        return sum;
    }),

    render: function() {
        var sum = 0;
        this.props.orderGoods.forEach(function(value, index, arr) {
            var price = value.price;
            if (typeof(price) === "string") {
                price = parseFloat(price);
            }
            sum += price;
        });
        
        var orderGoodsNode = this.props.orderGoods.map(function(goods) {
            var flagNode = goods.flag.map(function(flag) {
                if (flag.checked) {
                    return (<small key={flag.key} className="btn btn-tiny btn-warning">{flag.value}</small>);
                }
            });

            return (
                <div className="row order-goods-detail" key={goods.key} >
                    <div className="col-md-4">{goods.goods_name}</div>
                    <div className="col-md-2">1</div>
                    <div className="col-md-3">{goods.price}</div>
                    <div className="col-md-3">{goods.price * goods.number}</div>
                    <div className="col-md-12 text-right">
                        {flagNode}
                    </div>
                    <span data-key={goods.key} onClick={this.onDelClick} className="remove-btn text-danger glyphicon glyphicon-remove"></span>
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
                                合计:{sum}
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
这样删除订单列表的商品就很好的实现了，而且同时实现了订单列表的统计功能。

### 订单提交

界面交互做好后，就可以进行订单的提交处理了。订单的提交处理时间很容易处理，因为没有涉及到数据库的操作所以这里不把代码写出来，在GitHub上有代码。订单提交就是一个很简单的事件处理，有后台数据库操作的可以通过Ajax把数据发送到后台处理。

## 说明

到此本实例的基本功能已经完成了，通过本实例旨在为大家提供一个React的练手入门，其官方文档上也有两个很简单的实例（一个是评论控件，一个事ToDo控件）都是很基础的。希望大家通过这几个简单的实例能够领会到React的神奇之处。同时通过实例我自己是感受到了state的操作是需要很小心的，如果涉及的数据繁琐的话可能会更复杂。因此Facebook提出了React+Flux的解决方案，后来又有人在Flux的基础上进一步完善提出了React+Redux的方案针对性的解决了一些Flux的不足。本人也只是略微的看了Redux的文档，没有做过多的研究和使用。如果进行复杂应用的开发建议去了解下React+Redux。

当然本实例有很多不足，代码有错误或不当之处望通过GitHub指出。作为一个IT爱好者而非专业者与大家共同学习探讨IT知识。
