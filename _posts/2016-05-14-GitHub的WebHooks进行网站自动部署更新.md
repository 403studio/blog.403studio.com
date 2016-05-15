---
title: GitHub的Webhooks进行网站自动部署更新
categories: 学习资料
tags: GitHub Webhooks
---

## Webhooks

>
Webhooks allow you to build or set up integrations which subscribe to certain events on GitHub.com. When one of those events is triggered, we'll send a HTTP POST payload to the webhook's configured URL. Webhooks can be used to update an external issue tracker, trigger CI builds, update a backup mirror, or even deploy to your production server. You're only limited by your imagination.

GitHub为软件开发代码托管提供了很多非常便捷的工具，例如GitHub Pages可以实现项目网站，Webhooks可以实现自动部署。

本人的博客网站使用的GitHub作为代码托管（其实是有点多此一举的，并不需要托管在GitHub上，只是为了好玩），每次在本地添加了blog我就需要commit然后sysn，接下来我需要登陆到博客托管的ECS云服务器上通过命令行git pull等同步拉取新的博客文章。这样的话非常麻烦，但我又想折腾GitHub，于是就找寻GitHub自动部署的相关资料，最后就发现了Webhooks。

我们可以通过Webhooks订阅特定events，每当订阅的events触发后那么你在Webhooks上配置的url就会接收到HTTP POST请求，就是平时大家说的钩子。通俗点说就是当某些事件发生时，那么GitHub就会发送HTTP POST请求到指定的URL。既然有HTTP POST请求那么我们就可以做相应的处理例如更新代码等。

* 第一步：设定Webhooks

进入GitHub上的项目仓库 => Settings => Webhooks & services => Add webhooks => 填写响应事件URL（http://api.example.com/webhooks） => 选择需要订阅的时间（默认是push event）

你也可以设置Sercrt，设置了Sercrt后在服务器端你会需要对这个进行处理。设置完成后可以进行测试，在页面的下方有Deliver按钮进行分发测试（分发测试发送的是ping event）。当然你在没有配置我服务器响应信息的时候测试肯定是没有响应内容的。

* 第二步：服务器端响应处理

服务器端要做的工作就是更新我们的代码，这里直接使用shell比较方便。服务器接收到事件然后调用shell更新代码，直接新建一个deploy.sh(这里本人一开始是在Windows下使用的Sublime编辑的shell脚本然后上传的，发现有编码问题shell无法运行，所以建议直接在Linux上编辑)。编写完后可以先测试这个脚本是否运行正常。

```bash
#! /bin/bash
SITE_PATH='/myjob/blog'
USER='www'
USERGROUP='www'

cd $SITE_PATH
git reset --hard origin/master
git clean -f
git pull
git checkout master
chown -R $USER:$USERGROUP $SITE_PATH
```

npm上有个[github-webhook-handler](https://github.com/rvagg/github-webhook-handler)处理Webhooks请求，我们直接拿来使用就可以了。同时我们还需要一个forever工具来让我们的nodejs脚本在后台运行。

```bash
cd /myjob
mkdir webhooks
npm init
npm install -save github-webhook-handler
npm install -g forever
```
准备好工作目录后，就可以编写处理响应代码了index.js

```javascript
var http = require('http');
var spawn = require('child_process').spawn;
var createHandler = require('github-webhook-handler');
var handler = createHandler({path: '/webhooks', secret: 'hhua1120'});

http.createServer(function(req, res) {
	handler(req, res, function(err) {
		res.statusCode = 404;
		res.end('This location is forbidden');
	})
}).listen(8889);

handler.on('error', function(err) {
	console.error('Error:', err.message);
});

handler.on('push', function(event) {
	console.log('Received a push event fro %s to %s', event.payload.repository.name, event.payload.ref);

	runCommand('sh', ['./deploy.sh'], function(txt) {
		console.log(txt);
	});
});
// 这是为了响应ping event使用的，测试通过后可以将这段代码注释掉
handler.on('ping', function(event) {
	console.log('Received a ping event fro %s to %s', event.payload.repository.name, event.payload.ref);

	runCommand('sh', ['./deploy.sh'], function(txt) {
		console.log(txt);
	});
});

function runCommand(cmd, args, callback) {
	console.log('Running command');
	var child = spawn(cmd, args);
	var response = '';

	child.stdout.on('data', function(buffer) {response += buffer.toString();});
	child.stdout.on('end', function(){callback(response);});
}
```

在正式部署前可以在命令行进行测试`node index.js`，可以在GitHub设置Webhooks界面发送测试请求(ping event)查看服务器端的时间响应。测试通过后我们就可以使用forever正式部署这个应用脚本了`forever start index.js`

* 第三步：配置Nginx如果需要的话

在使用github-webhooks-handler时我们使用的是8889端口，如果在Github Webhooks里URL我们填写时http://api.example.com/webhooks那么我们需要都端口代理，使用Nginx是一个很好的选择。如何安装Nginx可以查看[搭建LNMP环境（Linux+Nginx+PHP）](http://blog.403studio.com/%E5%AD%A6%E4%B9%A0%E8%B5%84%E6%96%99/2016/04/27/%E6%90%AD%E5%BB%BALNMP%E7%8E%AF%E5%A2%83-Linux+Nginx+php.html)。找到Nginx配置文件添加server块重启就OK了。
```
server {
        listen          80;
        server_name     api.403studio.com;
        location =/webhooks {
            proxy_pass  http://127.0.0.1:8889;
        }
    }
```





## 相关资源

[GitHub Webhooks](https://developer.github.com/webhooks/)

[使用Github的webhooks进行网站自动化部署](http://aotu.io/notes/2016/01/07/auto-deploy-website-by-webhooks-of-github/)

[使用 GitHub / GitLab 的 Webhooks 进行网站自动化部署](http://www.lovelucy.info/auto-deploy-website-by-webhooks-of-github-and-gitlab.html)