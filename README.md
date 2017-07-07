### JS调用iOS原生API
#### 结构
![](http://www.plantuml.com/plantuml/svg/XLNlJzj66Fxkl-94f2O6kzA-Mb6aYQdQYBPEGri-L5jr99VWrjZ8Tc3LsgcbIKSf-R6sb3M4a0wsH1f5L6YK9eJyCxwp-TH_ONS-Ep4jrTulyPtVzvxdVPxt75krDGGowGoQ-q55o_Zf-ioiLsgGvhwGHRgDJ32FO0wPWjDxv7LsdTCshgtWwevRVqHpdTwUqtjAcZsyUYmWFHVa2l597i1zqyUxX-IFAkc-mjkR8DYF2t7sflL4oMhGiYGuPzacpDgIoY3pC8iagEk63MtLq2Ma5nTeXNSmFDyym3ixIa8MtSu3qdmi9MAYCzmcQtjIubSHhvV3xIaHb_VfKvnokU87e-BsIxY-6b2Xw-299roX4BZyskrKWhJsMtnQKxunSaZw3cLjg1Sq95qpDDgXTCs42qYQrenYJle2sd0EMYWkBCBiRLX0O4AzFiiqUhxp-RTVJIIJl2YL-aK0DFoMgUJ9aUh12nOtRmBouXqvsg0aw6AqFmKIQavKTTNEgFQy3wza3Jsl5egchv8IJLqoQ39QWbeHscW6Bi7PhAakshHYOL53BFrBgESqPFAgUqA463zrJ6uwYX9XAii0lscH-nt6L9OZy9dqznOoQPrD9uZzc8P6iS1ayRBjIEkZpgEDXtr_pDG_RZhv2RWBebv-iYyJpEMuEbyZowBkXSheJE9BaXbO86T0YP8AdGyl3nYR3c8coaMTlsNndAYKv-MAEpYW_292BbpWOoctB5Bz2zTsSRqoobaqh90tlPTaut1CAHxpmx-2Ux-He-vuxeChsVm77vuvWw7xlAlGouQ0Cs0i5C3pOs741V-jXl8sCFBKNe7yM_Cwg_XbrpdfEuDdxEe3biCJJRKmxsVoKGAnWWdl21J5wLLniqjQfVFxxKoQR1w_Frq__xE4rzhUO13AGlUyWpEcnjRlkBu_Cykx9YTbNDdu02can2T1u7XkeqJQ9tZua8ydgNQSFaCYjRfpjZN22wqYpohEe3a-c2gNyiDNgdpa7lNnrXckhNd35gCIrJ8LXF_fO9dUsegxyixT_rj9t4s9Rhz37W-bnAJ8lYUhTI0b3D5xqA0h2U3oiTD_4PD-bkv9Kz9FW1xlzeP-gIoB-82j0AMTk7fLz5usw2ekPD8Jdqs0IVa6cwaVPS1Ml6c5gyg702Gl6A43JTLlWzZol4hx-q1-V_2M-LMdob5vgFfa-nNLRAn9O2G3BfW8wHIQIwv4DGvXkIiSLQ26NvzsJbwJjtsgE_UKEHGnzVrf8pHZz8OD-TWI3WOCcHqTDXyEZ3AQne-yfOmowVyjymuVqk6CrFWH_Ftb3Sh05EQCEyXahhUUC5vyzpy0)
#### 前提OC监听
在OC中添加监听的接口清单：以JS脚本的接口`showMobile`为例：
```objc
WKWebViewConfiguration *config = [[WKWebViewConfiguration alloc] init];
WKUserContentController *userCC = config.userContentController;
//MARK:在OC中添加监听的接口清单：JS脚本的接口名
[userCC addScriptMessageHandler:self name:@"showMobile"];
```
#### JS脚本接口
js接口声明格式：
```
window.webkit.messageHandlers.接口名.postMessage(参数)
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


