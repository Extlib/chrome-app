通过这个步骤你将学习到：
- 如何从外部应用程序加载资源并将它们添加到DOM通过XHR和ObjectURLs。

完成这个步骤大概需要的时间：20分钟

#### CSP是如何影响使用外部资源
Chrome应用程序平台迫使应用程序完全符合安全策略(CSP)的内容。你不能直接加载Chrome应用程序包以外的DOM资源(如图片、字体、和CSS）。

如果你想显示外部image在你的应用程序,您需要通过XMLHttpRequest请求它,把它变成一个Blob,并创建一个ObjectURL。这ObjectURL可以添加到DOM,因为它指向的是应用程序上下文中一个内存中的项目。

#### 为item显示缩略图
