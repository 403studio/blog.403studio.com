---
title: React实例——使用React搭建订单页面Lesson04分类菜单添加事件响应
link: lesson04
date: 2016-07-05
project: react-example
excerpt: 使用React搭建订单页面Lesson04分类菜单添加事件响应，将事件的响应处理集中放在一个地方然后通过props传递给子组件是一个很好的方式。在交互界面，对事件进行响应处理并更新界面是WebAPP应用最多的地方。在React中我们只需要关心并处理好事件响应，界面的处理由React自动完成更新处理。很多时候事件的响应处理涉及数据的更新，所以对于这类事件我们一般把处理方法与state放在同一个地方
---

## 学习要点

* 很多时候事件的响应处理涉及数据的更新，所以对于这类事件我们一般把处理方法与state放在同一个组件而后通过props传递
* state的更新
* 通过bind(this)把当前对象的数据传递进方法
* 回调时是javascript的一大特色，无论是事件回调还是Node里面的回调，我们会在很多地方见到回调

## 功能说明

### 点击分类菜单，更新并只显示对应分类下的产品

这个功能涉及到界面商品的更新显示，所以我们最好是将处理的逻辑放在state定义的组件里，也就是OrderBox这个组件。首先因为是需要根据类目进行商品的显示，所以state需要额外添加一个关于filterCategory的初始数据用户过滤显示需要类目下的商品们，当然默认情况是没有过滤条件的。

```javascript
getInitialState: function () {
        return {
            goods: [{ id: "1", goods_name: "青草悠然", barcode: "0", plucode: "0", category_id: "1", category_name: "奶茶", price: "15", unit: "杯" }, { id: "2", goods_name: "燕麦兰香", barcode: "0", plucode: "0", category_id: "1", category_name: "奶茶", price: "12", unit: "杯" }, { id: "3", goods_name: "幽兰拿铁", barcode: "0", plucode: "0", category_id: "1", category_name: "奶茶", price: "15", unit: "杯" }, { id: "4", goods_name: "声声乌龙", barcode: "0", plucode: "0", category_id: "1", category_name: "奶茶", price: "14", unit: "杯" }, { id: "5", goods_name: "卡布奇诺", barcode: "0", plucode: "0", category_id: "2", category_name: "咖啡", price: "18", unit: "杯" }],
            category: [{ id: "1", parent_id: "0", category_name: "奶茶", open: "0" }, { id: "2", parent_id: "0", category_name: "咖啡", open: "0" }],
            flag: [{ key: 1, value: "常温" }, { key: 2, value: "加冰" }, { key: 3, value: "加热" }]，
            filterCategory: 0,
        };
    },
```

接下来我们还需要添加商品控件的显示逻辑，把filterCategory通过props一步一步传递给商品控件，并根据条件显示：

`<GoodsContainer filterCategory={this.state.filterCategory} goods={this.state.goods} category={this.state.category} flag={this.state.flag} />`

`<GoodsList filterCategory={this.props.filterCategory} goods={this.props.goods} />`

通过props传递给商品列表显示控件后，我们就可以修改控件的逻辑，让其根据条件来显示商品。这里传递的值是商品类目的id，这里有一点需要注意就是在map函数里面我们需要使用组件传递的props，也就是this.props.filterCategory在map函数直接使用时无法获取到数据的，我们需要通过bind(this)把对象传递进去。

```javascript
var GoodsList = React.createClass({
    render: function() {
        var goodsNode = this.props.goods.map(function(goods) {
            var filterCategory = this.props.filterCategory;
            if (filterCategory != 0 && filterCategory != goods.category_id) {return}
            return (
                <div key={goods.id} className="goods-detail btn btn-default">
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

我们在state定义里修改filterCategory可以查看到界面的变化，我们接下来要做的就是创建事件响应，我们根据语义将处理方法定义为handleCategoryClick，我们将这个方法添加到OrderBox控件里，这个方法仅有一个处理语句就是更新filterCategory，因为商品的显示我们已经修改成根据filterCategory来显示，所以在更新完filterCategory后React就会自动为我们更新界面了，这个方法只接受一个我们需要的参数就是类目ID，我们把这个方法通过props一步一步传递给商品列表控件：

```javascript
handleCategoryClick: function(category_id) {
        this.setState({filterCategory: category_id});
    },

....

<GoodsContainer onCategoryClick={this.handleCategoryClick} filterCategory={this.state.filterCategory} goods={this.state.goods} category={this.state.category} flag={this.state.flag} />

....

<CategoryList onCategoryClick={this.props.onCategoryClick} category={this.props.category}  />
```

最后我们修改CategoryList组件:

```javascript
var CategoryList = React.createClass({
    onClick: function(e) {
        var category_id = e.target.getAttribute("data-id");
        //console.log(category_id);
        this.props.onCategoryClick(category_id);
    },

    render: function() {
        var categoryNode = this.props.category.map(function(category) {
            return (<span onClick={this.onClick} data-id={category.id} key={category.id} className="btn btn-lg btn-success">{category.category_name}</span>);
        }.bind(this));
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

## 说明

在这节里面，需要特别注意的就是bind(this)的使用，在本实例里面很多函数我都用到了这个神奇的东东。