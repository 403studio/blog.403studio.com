---
title: ThinkPHP钩子(Hook)使用
categories: 学习资料
tags: PHP ThinkPHP
layout: project
project: ThinkPHP
permalink: /:categories/ThinkPHP/:title
excerpt: ThinkPHP的钩子允许开发者，在特定的标签位调用定义的行为。有什么作用呢，通过Hooks标签位我们可以在特定的位置执行特定的程序。这也就意味着我们可以把一些通用的功能通过标签位来调用执行
---

* 目录
{:toc}

## 使用背景

最近在做一个小项目（小型店面管理系统）使用了ThinkPHP3.2作为项目的基础框架，里面就涉及了不少列表页面和编辑页面(action分别为list,edit)，写了两个控制器（OrderController、GoodsController）的列表页面就发现问题了

* 两个列表界面的布局排版都是一样的，区别在于显示的文字信息不相同，列表页显示的字段信息不相同
* list方法的代码逻辑基本是相同的都是从数据库查询信息，唯一不同点就是查询的表不一致

像这种明细代码逻辑基本雷同的地方如果每处都独立编写代码，其实也不麻烦，只需要编写好一处其他地方复制粘贴就可以了。但是这不符合编程的习惯（DRY：donot repeat yourself）,而且后期如果需要修改业务逻辑可能连带着要更改所有的地方。如果我们能够在同一个地方来处理代码那是最好的。

* 问题一：页面布局我们可以使用通用模板，字段数据等信息通过参数来定义
* 问题二：因为共同的列表页每个控制器都需要有list方法，我们可以通过几个方法来实现DRY原则。方法一：每个控制器都编写list方法，但是list方法不提供具体的解决方案，它只需调用一个公共函数来处理业务逻辑，在公共函数可以通过全局变量来获取控制器名称，方法名称从而实例化控制器和模型从而获取数据（这个方法仍然需要为每个控制器添加相对应的方法，实际上每个控制的方法代码基本还是雷同的）；方法二：是用插件控制器，[插件控制器](http://www.kancloud.cn/manual/thinkphp/1725)的定义在文档上有定义，它通过由插件控制器来执行具体的逻辑来实现程序逻辑的转移从而实现通用功能（这个方法会需要修改URL传递而外的参数，所以也不是很理想）；方法三：添加标签位，使用行为来实现（个人认为是三个方法中最好的一个）

## ThinkPHP的CBD模式

>ThinkPHP引入了全新的CBD（核心Core+行为Behavior+驱动Driver）架构模式，从底层开始，框架就采用核心+行为+驱动的架构体系，核心保留了最关键的部分，并在重要位置设置了标签用以标记，其他功能都采用行为扩展和驱动的方式组合，开发人员可以根据自己的需要，对某个标签位置进行行为扩展或者替换，就可以方便的定制框架底层，也可以在应用层添加自己的标签位置和添加应用行为。而标签位置类似于AOP概念中的“切面”，行为都是围绕这个“切面”来进行编程。

官网所说的行为就是跟标签位以及Hooks息息相关的，通俗的来说，首先在配置文件中定义标签位所需要执行的类（行为），然后在整个程序执行的代码中在某个位置插入Hooks(及触发标签位执行标签位所对应的行为)。以核心代码自定义的action_begin标签举例来说

* 在/ThinkPHP/Mode/common.php中定义标签对应的行为

```
    'tags'  =>  array(
        'app_init'     =>  array(
            'Behavior\BuildLiteBehavior', // 生成运行Lite文件
        ),        
        'app_begin'     =>  array(
            'Behavior\ReadHtmlCacheBehavior', // 读取静态缓存
        ),
        'app_end'       =>  array(
            'Behavior\ShowPageTraceBehavior', // 页面Trace显示
        ),
        'view_parse'    =>  array(
            'Behavior\ParseTemplateBehavior', // 模板解析 支持PHP、内置模板引擎和第三方模板引擎
        ),
        'template_filter'=> array(
            'Behavior\ContentReplaceBehavior', // 模板输出替换
        ),
        'view_filter'   =>  array(
            'Behavior\WriteHtmlCacheBehavior', // 写入静态缓存
        ),
    ),
```

* 在代码中某处监听触发标签，例如在/ThinkPHP/Library/Think/App.class.php

```
    static public function run() {
        // 应用初始化标签
        Hook::listen('app_init');
        App::init();
        // 应用开始标签
        Hook::listen('app_begin');
        // Session初始化
        if(!IS_CLI){
            session(C('SESSION_OPTIONS'));
        }
        // 记录应用初始化时间
        G('initTime');
        App::exec();
        // 应用结束标签
        Hook::listen('app_end');
        return ;
    }
```

## 分析

标签位的实现查看代码发现还是挺容易理解的`Hook::listen`方法实际上就是根据传递的标签参数来查找所对应的类文件，每个类文件强制约定需要有一个run方法（这里ThinkPHP有个问题就是，实际上标签行为可以通过接口来约束必须实现run方法）。通过执行每个标签行为类的run方法来实现功能。
看了代码页发现有几个小问题
* 标签行为类实际上可以通过一个接口来约束必须实现run方法
* 对run方法的调用也存在些许问题，直接初始化行为类而后执行run方法，也没有做是否存在这个run方法的判断，没有做容错处理。

## 实现

### 实现标签位定义和配置

首先我们需要定义标签位的行为，自定义的标签位保存在/Appllication/Common/Conf/tags.php文件：

```
return array(
    'general_view_begin'=>array('Admin\\Behaviors\\ListViewBehavior'),
);
```

我们需要为控制器添加一个构造函数，在构造函数里添加标签位

```
    public function __construct()
    {
        parent::__construct();//调用父类构造函数
        $this->model = M(CONTROLLER_NAME);
        /**
         * @action 使用通用方法处理的action
         * @button 列表页面的button
         * @field 字段配置信息
         */
        $this->generalConfig = array(
            'action' => array('index', 'list'),
            'param' => array('title' => '商品管理', 'create_title' => '新增商品'),
        );
        \Think\Hook::listen('general_view_begin', $this);//添加标签位，并把当前对象作为参数传递过去
    }
```

### 实现标签位行为

根据上文定义的标签位配置，我们在/Application/Admin/Behaviors/ 目录下添加ListViewBehavior.class.php

```
namespace Admin\Behaviors;

class ListViewBehavior extends \Think\Behavior
{
    public function run(&$params)
    {
        if (in_array(ACTION_NAME, $params->generalConfig['action'])) {
            $controller = $params;
            $model = $params->model;
            $param = $params->generalConfig['param'];
            listview($controller, $model, $param);//这里就是通用的行为处理了
            exit;
        }
        return;
    }
}
```

通过上面的行为类就可以发现，如果传递的方法在我们的通用配置就执行我们的行为。这样在不修改核心代码以及不修改URL的方式下完成了通用方法的执行调用。

### 定义字段信息

当然为无论是上面三个方法中的哪一个，控制器都得先定义字段信息，通过字段定义我们就可以把列表页需要显示的字段针对每个控制器定义出来，例如：

```
public $fieldArr = array(
        array('field_name' => '', 'field' => 'id', 'listview' => false, 'editview' => true),
        array('field_name' => '商品名称', 'field' => 'goods_name', 'listview' => true, 'editview' => true, 'uiType' => 'text'),
        array('field_name' => '类别名称', 'field' => 'category_name', 'listview' => true, 'editview' => true, 'uiType' => 'category'),
        array('field_name' => '', 'field' => 'category_id', 'listview' => false, 'editview' => true, 'uiType' => 'hidden'),
        array('field_name' => '条形码', 'field' => 'barcode', 'listview' => true, 'editview' => true, 'uiType' => 'text'),
        array('field_name' => 'PLU码', 'field' => 'plucode', 'listview' => true, 'editview' => true, 'uiType' => 'text'),
        array('field_name' => '计量单位', 'field' => 'unit', 'listview' => true, 'editview' => true, 'uiType' => 'text'),
        array('field_name' => '参考单价', 'field' => 'price', 'listview' => true, 'editview' => true, 'uiType' => 'text'),
        array('field_name' => '商品LOGO', 'field' => 'logo', 'listview' => false, 'editview' => true, 'uiType' => 'text'),
    );

field_name:字段名称
field:对应数据库字段名
listview:是否在列表页显示
editview:是否在编辑页显示
uiType:编辑页面显示类型
```