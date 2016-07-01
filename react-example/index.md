---
title: React学习实例
date: 2016-07-01
project: react-example
excerpt: 学习使用React，本实例没有太多深层次的研究。仅提供本人在学习React中经验，以及个人项目中实际线上使用的一个缩减版本的实例
---

## React介绍和Web前端开发的几次变革

[React][React]是Facebook开源的一款构建用户交互界面的Javascript库，自发布依赖改变了很多开发者对前端开发的理解。在React取得成功后又发布了React Native用于构建统一跨平台的原生应用。笔者从2009年参加工作算是经历的Web前端的几次变革：

* 2008，2009年正好是处在变革一个时期，那个时候Ajax开始展示其强大的功能。这个时候的Web国内很多还停留在信息的展示，Html+CSS。而Javascript在这个时期仅仅是提供一些有限的交互体验，使用比较多的如事件处理。此时国内的浏览器主流仍然是IE，这个对开发者来说犹如噩梦的浏览器霸占着各个桌面，Firefox因为有Firebug这个强大的CSS调试工具成为了我个人日常使用浏览器

* Ajax以及各种Javascript库盛行的年代，这个时期Web界面已经从单纯的展示信息转变成一个友好的用户交互界面。Google的Gmail产品，以及雅虎等大型互联网公司更是推动了Ajax以及Javascript的发展，直接或间接导致了Jquery，Dojo，Prototype等很多Javascript库的出现，Jquery更是一直持续发展备受中小项目开发的青睐。因为开源在这个时候大行其道，这个时候的浏览器开始群雄逐鹿，国内涌现了大批使用开源内核的浏览器（也有自己开发的）而IE浏览器在国内更是出现了IE678并存的一个局面。Javascript在这个时期在Web开发中占据了很大的比重，它提供了一个交互性友好界面漂亮的Web页面，CSS3在这个事情已经长露头角。此时的谷歌浏览器因为其简洁的界面成为了笔者的日常使用浏览器。

* Node以及React等新兴技术的兴起，新兴技术的兴起直接让Javascript成为了一门前后端通用的语言，特别是Node以及NPM的广泛使用更是让Javascript推向了一个新的高度。在上两个阶段Javascript在前端界面的作用基本还是停留在原生的DOM操作以及事件处理上，唯一不同的是随着时间的发展Javascript所能提供的操作和事件处理越来越完善，但是其在Web前端开发的本质作用并没有发生实质并的突破和发展。React的出现至少是让我眼前一亮，Vitual Dom以及State的提出让我们摆脱了Javascript不断去操作DOM，不断去更新各个元素的状态样式。在交互性多的页面React简直可以说是解放了我们，我们唯一要做的就是更新State而界面的更新逻辑则交给React自动替我们完成。

我自己使用2015年开始接触React，并在2016年在改造一个现有的小型项目的时候将React引入。目前React给我的感觉就是我无需关心界面怎么交互更新，我只需要控制更新State数据至于界面React这个小伙子会替我做好的。在本案例中我将为大家展示一个点单界面，改造之前是使用jQuery的，因为交互逻辑比较多在jQuery时DOM操作过于复杂，所以在遇到React后将其进行了改造。改造的界面如下：

![界面展示](/react-example/img/panel.png)

## 准备工作

React开发环境的搭建，只需要引入react.js（React核心）React-dom.js（Dom处理）browser.js（jsx语法解析），至于其他的构建工具可以开正式环境中引入使用。在本实例中将使用Node来安装React，并使用[Babel][Babel]对jsx进行解析（Babel可以理解为Javascript编译器，能够将jsx语法编译成Javascript）。

```bash
cd workDir
npm init

npm install --save react react-dom babel-preset-react
npm install -g babel
```

这样就可以初始化环境了，如果没有**接触过NPM或者不想使用Babel，也可以把案例中的代码做出调整将：**

```javascript
<script type="text/javascript" src="/workDir/node_modules/react/dist/react.js"></script>
                
<script type="text/javascript" src="/workDir/node_modules/react-dom/dist/react-dom.js"></script>
                
<script type="text/javascript" src="/workDir/lib/build.js"></script>
```
**改成：**

```javascript
<script type="text/javascript" src="/lib/react.js"></script>
                
<script type="text/javascript" src="/lib/react-dom.js"></script>
                
<script type="text/javascript" src="/lib/browser.js"></script>

<script type="text/babel" src="/lib/build.js"></script>
```

* 使用jsx语法就需要，将type类型修改成type="text/babel"
* react.js,react-dom.js,browser.js可以去网络上下载，很多CDN都有提供这个三个文件的下载和引用
* 本人还是建议第一种方式搭建环境，另外也可以将Webpack引入构建整个环境。因为此实例仅仅是为爱好者提供一个简单的案例，所以只使用了Babel将jsx语法进行编译，并没有引入Webpack
* 如果还是无法成功搭建环境的话可以访问[React 官方文档][React docs]获取信息

## 相关资源

[本实例Git](https://github.com/403studio/react-example)

[React 官网][React]

[React 官方文档][React docs]

[Babel 官网][Babel]

[React]: https://facebook.github.io/react/
[React docs]: https://facebook.github.io/react/docs/getting-started.html
[Babel]: https://babeljs.io/

