### WKWebView简介
自iOS8 以后，苹果推出了新框架 WebKit，提供了替换 UIWebView 的组件 WKWebView。各种 UIWebView 的性能问题没有了，速度更快了，占用内存少了，体验更好了，下面列举一些其它的优势:
1. 在性能、稳定性、功能方面有很大提升（加载速度，内存的提升谁用谁知道）
2. 更多的支持 HTML5 的特性
3. 官方宣称的高达60fps的滚动刷新率以及内置手势
4. Safari 相同的 JavaScript 引擎
5.将 UIWebViewDelegate 与 UIWebView 拆分成了14类与3个协议，包含该更细节功能的实现。
### JS调用iOS原生API
#### 结构
![](http://www.plantuml.com/plantuml/svg/XLRlJzj66Fxkl-94f2O19rAtRnP2cfYgZMZhDBJbm-Wg9px0nTZ8SSZGsedIf4lvbGmAh1027L3GHXaL6j24a7_6TmwV-Y_iFPyT7Cgq-m2-y_lUyxpF-zmvbcff62KJIPpwNiKviZG_CDZAhzFAeP36keLDD8fb1Pk2NNlUsj-nBxP9QOOiRZdbvn1hrtRjsXAjr4ZnLC2wuiKAuMu-aFEkJdQEwU-BzE0rsLn3tdfO2BCtrJafhScPZ2YdCfOfforHPP33SXgBiguRbcofXYvYFJiE6QsZvjNQ4TdQaYAngBF_b5PUY95Gr6vkqjbTCV9BbCmN_EMUA2aSmbCOkDnn1wFYrFEaNFIemDnxuW5t088KtZhx2rxOzXcvA4bV6meMlyLfIzP7D2pUCpIeKBndokDOxDUCh29-BbjoIixWi30XfyVa4OowrFk3JAENMvzzysLNRozFwklxMK0mt991yjwswjuBDXu-H7JrFJrP0H8mQQ_te8YgH5LTjPAgDUh2Isb37rP7igQhaXGCdJGW64_AMbQsy80yAG-cJNN2Wepn2Gspy2zaNT6moREU205YVDThSlr1b03JM0oHTrKwlS-Onc81-6JYkmms8Sy21x5_fg415khEthOSABrTUR1ml-wFcRhRzVV-1ro7gBVlxCeaAmfNvokSoK3tV6Lq9l4jmGpCazEZnFu6Q8to3O3NRVjwj68nkxu3XuWJy-JpG6wLIL9K1Mo8rKdSiQrF0fJnxUUVO7JjF1GoiHAqtwCCZmdsywgmu3IEGAH0DySxo6UaHncwk4TAEwIyq8wPC38-6xWSwChnDQLmo1tkFR3xApqvk3vythrG-OiSNzgDflFoG88JZv7TO2manED3_WWBxblBc436C5XCeF-KMlj5ykh0Fg_RZMLs_I0MmkDCTMJKOf7SpYWqOifJ0e3OjKLIEQ3L_DNqTZ91rqu_NCnV_P4dizkjHiDN1TPQHvTCZ8tVIFbmO90NJSyBP67b1bBAi2nZd45nFqftuxHKjYytshlw_Q3B2tQZCgH_4WS6z_ljyxVqh0uX7CHPpmTHFboi-tdjDo3waFvf76GXXJtk7n2oGnY_0WcrZfz1kVmQ6j9HJjKL8nVFuTIOQiLlj3KUQ6iyqCu8xsSKylcJVLvJ5NvrWqQaiKpNs0U1Ot4nUI2N3yBrhARTQ8btGAJW2O1owTmRMlopKt7MYCKpKbeT68HFpzNq4cZ10SZqnP0EEfADAg23bZokzVTU-ptiwTKPJndA5BYnpnsk-medpacTR5oIqcohMMLeGN_sUSFz9h38rkoCC_FUENmZHHxtHPtwFdtH52FTKVQXA9QH636YhQVhC1CHAPpQzTMG-9FuHEmHVqImlLDhkgcnM9GSiHc2mYBnUBJrQXrcOI6PwBhRXRfZFx1p-Y26s8mxL-9MvKMXtWufTQIf-XWAvKPLSECDJxk7ESVlS50Dt0WzePklxVhfjOC5xtWmv14JOnsmFHlJxNFIV0Qt0IG0hi2NaH7JyB3byspRhNqQFt8zv2OJ_vl67HJ8yU0OiPIMnG_QhY6xUUPxroQaU0ob1RAy8DJxsCDBQN0f0c8o8Q7sYO50xyVDi04gc4BAcC8cQtrrZcd3L_y5)

#### 添加监听代理和JS接口
在OC中添加监听的接口清单：以JS脚本的接口`showMobile`为例：
```objc
WKWebViewConfiguration *config = [[WKWebViewConfiguration alloc] init];
WKUserContentController *userCC = config.userContentController;
//MARK:在OC中添加监听的接口清单：JS脚本的接口名
[userCC addScriptMessageHandler:self name:@"showMobile"];
```
#### 设置WKUserContentController的代理

1. 设置代理类遵守`WKScriptMessageHandler`协议
```
@interface ViewController () <WKScriptMessageHandler>
```
2. 注册对JS接口监听，注入代理类
```
[userCC addScriptMessageHandler:self name:@"showMobile"];
```
3. 实现`WKUserContentController`代理的回调方法,响应JS接口事件
```
- (void)userContentController:(WKUserContentController *)userContentController didReceiveScriptMessage:(WKScriptMessage *)message {
    NSLog(@"%@",message.body);
}
```

#### JS脚本接口
js接口声明格式：
```
window.webkit.messageHandlers.接口名.postMessage('参数')
```
接口名: 在WKWebView中，当JS执行该接口时，OC会拦截预先监听的接口，并处理相关事件。

参数：object类型，多个参数时需要封装为集合类型来实现多参传递。

当OC拦截到该接口时，可以在`WKScriptMessageHandler`回调方法中的`WKScriptMessage`参数实例中获取该参数值: `message.body`。

三个例子：
1. JS无参调用OC<br/>
当无参调用OC时，参数必须为`null`
```js
window.webkit.messageHandlers.showMobile.postMessage(null)
```

2. JS传参调用OC<br/>
传递单个参数时，直接写入即可，例如：`xiao黄`
```js
window.webkit.messageHandlers.showName.postMessage('xiao黄')
```
传递多个参数时，需要封装为集合类型实现多参传递。<br/>
例如:当传递一个电话，一条信息，需要封装为`['13300001111','Go Climbing This Weekend !!!']`
```js
window.webkit.messageHandlers.showSendMsg.postMessage(['13300001111', 'Go Climbing This Weekend !!!'])
```
### iOS原生API调用JS脚本
在网页加载完成之后调用JS代码才会执行，因为这个时候html页面已经注入到webView中并且可以响应到对应方法。<br/>
例如调用JS函数`alertMobile()`：
```objc
[self.wkWebView evaluateJavaScript:@"alertMobile()" completionHandler:^(id _Nullable response, NSError * _Nullable error) {
                //TODO
                NSLog(@"%@ %@",response,error);
}];
```

#### 在OC中为JS定义属性/函数

* 当注入的类型字符串类型时，必须用`''`括起来。<br/>
* OC注入的参数为全局属性，在html中的JS脚本可以直接调用属性名来获取值。<br/>

通过NSString形式，编写JS脚本，通过以下两种方式注入网页

方式一：在初始化`WKWebView`时，通过配置`WKWebViewConfiguration>userContentController`注入JS脚本  。
```objc
//MARK:向网页中注入JS脚本例如，参数/函数等
WKUserScript *script = [[WKUserScript alloc] initWithSource:@"var number=0;"
                                                injectionTime:WKUserScriptInjectionTimeAtDocumentStart
                                        forMainFrameOnly:YES];
WKUserContentController *userCC = config.userContentController;
[userCC addUserScript:script];
```
方式二：使用WKWebView实例方法`evaluateJavaScript`动态注入JS脚本

```objc
[self.wkWebView evaluateJavaScript:@"var number=0;" completionHandler:nil];
```
#### iOS原生API调用JS函数
使用WKWebView实例方法`evaluateJavaScript`动态调用JS函数
```objc
[self.wkWebView evaluateJavaScript:@"alertSendMsg('18870707070','下午好！')" completionHandler:nil];
```


