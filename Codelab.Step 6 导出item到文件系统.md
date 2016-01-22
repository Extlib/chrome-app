通过这个步骤你将学习到：

- 如何引用外部文件系统中的文件
- 怎样输出到文件系统

完成这个步骤大概需要的时间：20分钟

#### 导出Todo项
这个步骤会给app增加一个导出按钮，当被点击时，当前的todo项将被作为文本文件储存到外存，如果文件已经存在了，文件将会被覆盖，否则将会创建一个新的文件。

##### 更新权限
文件系统权限为只读访问可以请求为一个字符串,或一个对象附加属性。例如:
```
// Read only
"permissions": ["fileSystem"]

// Read and write
"permissions": [{"fileSystem": ["write"]}]

// Read, write, autocomplate previous input, and select folder directories instead of files
"permissions": [{"fileSystem": ["write", "retainEntries", "directory"]}]
```
你只要读写权限，在`manifest.json`文件增加`{fileSystem: [ "write" ] } `权限：
```
"permissions": ["storage", "alarms", "notifications", "webview",
                 "<all_urls>", { "fileSystem": ["write"] } ],
```

##### 更新HTML视图
在index.html添加一个导出按钮和一个显示消息的div：
```
<footer id="info">
  <button id="toggleAlarm">Activate alarm</button>
  <button id="exportToDisk">Export to disk</button>
  <div id="status"></div>
  ...
</footer>
```
同样在`index.html`链接`export.js`脚本：
```
...
<script src="js/alarms.js"></script>
<script src="js/export.js"></script>
```

##### 创建导出脚本文件
创建一个名为`export.js`的文件并保存在js文件夹下：
```
(function() {

  var dbName = 'todos-vanillajs';

  var savedFileEntry, fileDisplayPath;

  function getTodosAsText(callback) {
  }

  function exportToFileEntry(fileEntry) {
  }

  function doExportToDisk() {
  }

  document.getElementById('exportToDisk').addEventListener('click', doExportToDisk);

})();
```
现在创建了一个导出按钮的监听器以及`getTodosAsText()`, `exportToFileEntry()`, and `doExportToDisk()`。

##### 从文本中得到Item项
更新getTodosAsText(),使它从chrome.storage读取待办事项。和生成本地的一个文本：
```
function getTodosAsText(callback) {
  chrome.storage.local.get(dbName, function(storedData) {
    var text = '';

    if ( storedData[dbName].todos ) {
      storedData[dbName].todos.forEach(function(todo) {
          text += '- ';
          if ( todo.completed ) {
            text += '[DONE] ';
          }
          text += todo.title;
          text += '\n';
        }, '');
    }

    callback(text);

  }.bind(this));
}
```

##### 选择一个文件
用` chrome.fileSystem.chooseEntry()`来更新的你的`doExportToDisk()`方法，允许用户选择一个文件：
```
function doExportToDisk() {

  if (savedFileEntry) {

    exportToFileEntry(savedFileEntry);

  } else {

    chrome.fileSystem.chooseEntry( {
      type: 'saveFile',
      suggestedName: 'todos.txt',
      accepts: [ { description: 'Text files (*.txt)',
                   extensions: ['txt']} ],
      acceptsAllTypes: true
    }, exportToFileEntry);

  }
}
```
chrome.fileSystem.chooseEntry()的第一个参数是一个对象，第二个是一个回调函数。
