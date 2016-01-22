#### 增加通知
让我们做一些改变让用户很容易的注意到，使用`chrome.notifications`在桌面显示一个通知，如下面：

![notification-example.png](./img/notification-example.png)

当桌面的通知被用户点击时，应该进入Todo App的应用试图。

##### 更新App的权限
在`manifest.json`添加`notifications`权限：
```
"permissions": ["storage", "alarms", "notifications"],
```

##### 更新后台脚本
在`background.js`文件里，重构`chrome.app.window.create()`回调函数使用一个独立的函数让你来调用：
```
function launch() {
  chrome.app.window.create('index.html', {
    id: 'main',
    bounds: { width: 620, height: 500 }
  });
}
chrome.app.runtime.onLaunched.addListener(launch);
```
##### 更新alarm 监听器
在`background.js`的顶部，新建一个变量用于创建alarm的监听器：
```
var dbName = 'todos-vanillajs';
```
`dbName`与`js/app.js`里的数据库名字一样，在其17行：
```
var todo = new Todo('todos-vanillajs');
```

##### 创建一个notification
并不是简单的将警告信息输出到控制台，更新`onAlarm`监听器来存储数据，通过`chrome.storage.local.get()`并且调用一个`showNotification`方法：
```
chrome.alarms.onAlarm.addListener(function( alarm ) {
  chrome.storage.local.get(dbName, showNotification);
});
```
添加`showNotification()`方法到`background.js`：
```
function showNotification(storedData) {
  var openTodos = 0;
  if ( storedData[dbName].todos ) {
    storedData[dbName].todos.forEach(function(todo) {
      if ( !todo.completed ) {
        openTodos++;
      }
    });
  }
  if (openTodos>0) {
    // Now create the notification
    chrome.notifications.create('reminder', {
        type: 'basic',
        iconUrl: 'icon_128.png',
        title: 'Don\'t forget!',
        message: 'You have '+openTodos+' things to do. Wake up, dude!'
     }, function(notificationId) {});
  }
}
```

`showNotification()`用于检查未完成的item项，如果至少有一个item没有被完成，将会通过`chrome.notifications.create()`来创建一个通知。

第一个参数是用来标识通知的名字，必须有一个id用于清除该通知或者与其进行交互，如果匹配了一个之前存在的通知，那么之前的将会被清除。

第二个参数是`NotificationObjects`对象，有很多的可选项用于弹出的通知，这里我们使用一个基本的：icon，标题，信息内容。其他类型的通知还可以包括图标列表以及进度指标。

第三个参数是可选的，这是应该的格式：
```
function(string notificationId) {...};
```

##### 处理通知交互
打开app当用户点击了notification，在`background.js`的末尾，创建一个事件处理函数`chrome.notifications.Onclick()`:
```
chrome.notifications.onClicked.addListener(function() {
  launch();
});
```
处理事件只是简单的调用了`launch()`方法，`chrome.app.window.create()`将会创建一个新的窗口如果Todo的窗体不存在，或者让已经打开的窗口获得焦点。

#### 启动你的App
检查你的App是否符合预期：
- 如果你没有任何的未完成项，讲不会弹出通知。
- 当你的App是关闭状态时，点击通知将会使窗口获得焦点。
