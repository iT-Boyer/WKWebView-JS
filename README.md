### WKWebView简介
自iOS8 以后，苹果推出了新框架 WebKit，提供了替换 UIWebView 的组件 WKWebView。各种 UIWebView 的性能问题没有了，速度更快了，占用内存少了，体验更好了，下面列举一些其它的优势:
1. 在性能、稳定性、功能方面有很大提升（加载速度，内存的提升谁用谁知道）
2. 更多的支持 HTML5 的特性
3. 官方宣称的高达60fps的滚动刷新率以及内置手势
4. Safari 相同的 JavaScript 引擎
5.将 UIWebViewDelegate 与 UIWebView 拆分成了14类与3个协议，包含该更细节功能的实现。
### JS调用iOS原生API
#### 结构
![](http://www.plantuml.com/plantuml/svg/XLPjJzjM5FxkNt55f2O19rAtRnv2cfYg3TGnZMtvCBhAYIzWC3Po73AqTgAKT2bloQ30odi75BHHHeK6D27ap_XUXq_z2plNrpOEfThz0BxsEVSvvpdFEJUMQcaOfRfJEFs3YlDaORQhjp6rIjSFXGpMBMoYGImhs1JiohF6_evziKrAasH-ooa_0rkxicjN5kXwXHHF1QmhdgqGRUKBEM-hPEUO_Z5F3rwHZHNalOyAKVPbSqRAQ78sAyhfh6NA6KjK6MI_dC6YhEk69LkgeOjOpus0H-EeVhLoHBQsf5WYxkm_eUlFnLWaRjStwFIk6FirJcOB_kksE2aSmbCKOhdZBXQAKvqYvQ8N2koz9svm1o18uOspF-UPRP-HYvBqjQ5WyLkSiMHzGCFYFKE33CLxfZo2nKxDo2dY5x8bf-KiZWgZScPO7i2eHUtfPHozsFhycwzQsjkvKqV7BmA2vQOCbBS7h7iVs7hu4D7bT_Ha2OA0JV2-3SLK9QxggfLIhK4NNieOUhywa3DTbgImwPW1ndXCrdAoXRlaCRatOwgZ5dYCZ6gOcNyfwug6JUxrM0W5na-z3gupZ1AAD950vEqcdTXdaIOI8VXKz_TPR8AV1GfY_qn30opKchljTIZr8FDmudxU7qRg7jVP_X6u9b9lFzcbILOKpivzdCr2zNncT4RnBSOCpAFJ2udz3OKro0-0k6uxrmihaR2hEz14F32FFW_aLfeaHLL0XbWTmqt7VYI8h6lZSSCT0cIwENKriKrU7lXLQnPNhv4pCrXIzI5GFTJyEnM8w3VC-xAgkyBhqRLnwJCF5ohYozwV7EXQyIXYOYKi-w4ijmdhwAemvzIEe3WX5OqqaPQLXh9qVe-KTaXvBh0PDR8-2p2KwDBnTKZHYBlS-RFx6pqvk6vwRnwj_qsEB-rQtNbn8C6amSYkiIWanEqZ_eeAxbVB64L6FqXReF-M6lj5OCy-hzgrHJRs43FXTgOwC6WnIzv6A39WokC2WDYLUR9-mAbFTTELq_SNirT_Jf7fxKQjvhC2xnf7buoCjTz9-R2hboTDpmjaRka6KjgmB6E4GV4-aEucQQbiNwu5f_hre8jpTcszJ_ya2H7qTDhdR-XP5KmuYBCw5KPvVx7g-mLVWFG-_TCaq48AUrotG6GJCNu64ceSFuLq-VZhqr5UrHKZdypZzB1g9M-KDHagQp9KpXYlPnpy-PDzNb4LVcK0HwIsI5VOHSInE9dSaDC7vdfEqswKn6jCATnva2wTUKsBVpKppWenVqPAorszSELTJIm05no0J5pqwS0ZMTi4TE2IspN-sGk-mvbUdicqeOo36ZlSvQglSEASLCdQ9IbDD-gR32sipmvlkNSHisnCJpgJxvp3rrBiKKVSgUxJvtKnrXfd5rInZCIO4MyyMOMTY4ZXrAukHyIVnSTYc_WJWkETIjrrJIJYv8Zj42GMIoRZZPUhi8iAgUwMkoseDV4ZwzC72SHsNBcIDoNSf51x4vKwqbHz64NoWogeyOQctMREyxi3M0CrGatenYkxUdgjOC5h3uOyO6Ai0xOdOxfzJkfFOHg00-0AV98X8w7XVikFCv1Rq8qVg1vyKzt_wyOL5FBXKRY1fRKSVX2eXknECTsxCY75OqWjvEKPeVP77bvQWw48YAbk2GKTStsy7nQR3c35SvHuWmM7-h_0-WsWp1JInZWscKusPnYH_Er_)

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
```objc
@interface ViewController () <WKScriptMessageHandler>
```
2. 注册对JS接口监听，注入代理类
```objc
[userCC addScriptMessageHandler:self name:@"showMobile"];
```
3. 实现`WKUserContentController`代理的回调方法,响应JS接口事件
```objc
- (void)userContentController:(WKUserContentController *)userContentController didReceiveScriptMessage:(WKScriptMessage *)message {
    NSLog(@"%@",message.body);
}
```

#### JS脚本接口
js接口声明格式：
```js
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


