 Chrome App的组成组件：
 
- `manifest` 文件告诉浏览器他是什么，怎样启动它，需要哪些额外的权限。
- `后台脚本(background script)`用于创建事件页面，负责管理应用的生命周期。 
- 所有的代码都必须包括在Chrome应用程序包。这包括HTML、javascript、CSS和原生客户端模块。
- 所有的`icon`和其他资源文件都必须包含在包中。

#### Step 0: 创建一个Hello World
首先创建你的manifest文件来描述你的App:
```
{
	"name": "hello world",
	"description": "My Fisrt Chrome App",
	"version": "0.1",
	"manifest_version": 2,
	"app": {
		"background": {
			"scripts": ["background.js"]
		}
	},
	"icon": {
		"16": "caculator-16.png",
		"128": "caculator-128.png"
	}
}
```
**注意:** Chrome App必须使用manifest version 2

#### Step 1: 创建后台脚本
创建一个叫`background.js` 的后台脚本文件
```
chrome.app.runtime.onLaunched.addListener(function() {
	chrome.app.window.create('window.html', {
		'outerBounds': {
			'width': 400,
			'height': 500
		}
	});
});
```
当App启动的时候`onLaunched`事件将被激活,将会创建一个指定大小的窗口.后台脚本将包含一些其他的监听器,窗口,发送信息,加载数据等.通过事件你可以来管理你的App.

#### Step 2: 创建一个窗口页面
创建window.html文件:
```
<! DOCTYPE html>
<html>
	<head>
	
	</head>
	<body>
		<div>hello, world!</div>
	</body>
</html>
```

#### Step 3: 创建一个图标
复制这些文件到你的App文件夹

- [calculator-16.png](https://developer.chrome.com/static/images/calculator-16.png)
- [calculator-128.png](https://developer.chrome.com/static/images/calculator-128.png)

#### Step 4: 启动你的App
##### 开启标志
许多的Chrome应用程序api仍处于试验阶段,所以你应该使实验api,这样您就可以试一试:

- 在地址栏输入 chrome://flags
- 找到实验性扩展程序APIs并开启
- 重启Chrome

#### 加载你的App
加载应用程序,打开应用程序和扩展管理页面点击设置图标,选择工具>扩展。确保开发人员模式复选框被选中。点击加载打开扩展按钮,导航到您的应用程序的文件夹并单击OK。好了,现在你可以引导你的App了.

#### 通过命令行来加载你的App
-  `--load-and-launch-app=/path/to/app/ ` 从给定的路径安装打开应用程序,并启动它。如果应用程序已经运行加载与更新的内容。
- `--app-id=你的App id` 它不重启任何之前运行应用程序,但是app的内容是最新的了.

