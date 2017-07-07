### WKWebView简介
自iOS8 以后，苹果推出了新框架 WebKit，提供了替换 UIWebView 的组件 WKWebView。各种 UIWebView 的性能问题没有了，速度更快了，占用内存少了，体验更好了，下面列举一些其它的优势:
1. 在性能、稳定性、功能方面有很大提升（加载速度，内存的提升谁用谁知道）
2. 更多的支持 HTML5 的特性
3. 官方宣称的高达60fps的滚动刷新率以及内置手势
4. Safari 相同的 JavaScript 引擎
5.将 UIWebViewDelegate 与 UIWebView 拆分成了14类与3个协议，包含该更细节功能的实现。
### JS调用iOS原生API
#### 结构
![](http://www.plantuml.com/plantuml/svg/XLRlJzj66Fxkl-94f2O1EzA-MX7In5HjH5kdeIqVrgvouYEu61iv3gngEr6QT9HVOL1WXH3ee0tQAAD20veGyi_upk5J_uMzvxE3ePbs7y1tzzuzp_kypxrWQxQEKJgLnfaVD3n9bkS7Xzh53Lev4BBOiB65Hh2YOajmwi_RjLtdV8UKfydYjhlq76AT-fvJNwQLEfav4R2X-h52j9SFvBvlaDqZ-liYtNzDjjQHlnuLecodEYTdTIMV5vLCthQKh2rg379OoM9HCGpJLcpDD4Hi5CRWHFkmTRb-IBQtvPWKTsjFQUM568l4dTOMdTqJOx_4oNmfMEwBaz81V4M1oorlC2fkeqYMPdmgCFU_UC0jW22bTsvjmG_REINdPVaRKyNYTpXhAqPEn-9jKuSCnTkMCeR50TqigE8NYgrab3oE2kDATbJ9OTIZtHbY6htS_lpRhtgIINwel_-HW61uAOFaoOxg_WORz-yZklQ17gy22PXqrljGJ5FZcg7PQSqUyU3bh6aCQxc2vQaaXqCdJ0Z64ufUK6myg4meGrbB6xVXnDYuZbdubugXwjZYfvu88MByrYjo0s6K45D9GkHzbKxL65D92i6dKz_diGNdR70G-sMPEc2XtaAtvL3gdSp3YGTvVyhKksuW-HzmrqJjVhCdawAgN9slSJuFrGkKCPZ4NO8Pc2-dJudz3D4QuHS0hsxt-hGaoMdimYFYn7pvV92kCicgfe8DiJQ1hrqRa01b0li5BnXTEGz5BAo6xVSWpsF2zRmiBRZDGn0fLCsnQ-JpyeCyNNn3ohjaQQ4JCsxc0pRG7EZgqHMbQCGRNX_O-vKUxryzFhylLFuYHnTEi-M-t9VXnMFaD1aB6V7uI32YWhThc-F870QB2VIVShisGrxjEsSDfxd2sWzY8JpEqd8ZDelaTaQHdAKy5034gI-IoZwj5Y-dTj8fkdxoyNp-yeyYcTrfDvk1Ah3MFhnWOcp-HfOE1eTuqlIiH1PMRo1bJDisnnWKzwF04TsD8jqv8wrd_97GnPhJO5YqlEHSR7OGWrhHbGMdMRbnjPzim4f6xQDda09lBNSDDAaPgZcPcCIPKSrEt2XL8bIgHAX4CLwZE9pdNyvPNLDvEmP-fBb2rrcJvrXS81x89O5mew3hDsJsJI-7NVtnV8FElQKpVrvNaOcxU4hAQuD3yEVaScePfE80PEhyhW7L8PjLG7VEtl6uzjzlEljmfvzd8cEg3y5X_Txm75KwTeyRPFE2b6VRhIf32tkktnzUdsUHxTbfT_g3U_1MZZtkZxkD6dtH4cEzSTRyPvQG63FZxQSRC1CHAPquZRM8-BFuHEmJVq9mlLjlUKSbAKuEsGn1Oh548jv-jG6pg91EzNpMWtgb7zdRkoSXDkFUaBdzU58eUKrA0-cQCOeYaoCQsE26Jxq7EidxCgW6BeAQqAtNOAqhw_W6z91p5iO6O7Dhoc4lTiKLGFqx0w0yFMs0GNdXc9L3_eSI1AxjxB1sS4M0eo5Jql6m7QJVSN7duNxo281IElM_nxWnGsTyE9PlHY_WUnqpahqYc94dGMQEW6teb1-4aezzl8mE_HCGqoaPSHk3fo3G_pzes8JSB25ZFiGMSrHrZcNCL_y5)

#### 添加监听代理和JS接口
在OC中添加监听的接口清单：以JS脚本的接口`showMobile`为例：
```objc
WKWebViewConfiguration *config = [[WKWebViewConfiguration alloc] init];
WKUserContentController *userCC = config.userContentController;
//MARK:在OC中添加监听的接口清单：JS脚本的接口名
[userCC addScriptMessageHandler:self name:@"showMobile"];
```
#### 设置userCC的WKScriptMessageHandler代理类
1. 在WK视图控制器的接口处添加该代理
```
@interface ViewController () <WKScriptMessageHandler>
```
2. 在添加js接口监听时，注入代理控制器
```
[userCC addScriptMessageHandler:self name:@"showMobile"];
```
3. 实现代理的回调方法,响应JS接口事件
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


