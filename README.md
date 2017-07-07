### WKWebView简介
自iOS8 以后，苹果推出了新框架 WebKit，提供了替换 UIWebView 的组件 WKWebView。各种 UIWebView 的性能问题没有了，速度更快了，占用内存少了，体验更好了，下面列举一些其它的优势:
1. 在性能、稳定性、功能方面有很大提升（加载速度，内存的提升谁用谁知道）
2. 更多的支持 HTML5 的特性
3. 官方宣称的高达60fps的滚动刷新率以及内置手势
4. Safari 相同的 JavaScript 引擎
5. 将 UIWebViewDelegate 与 UIWebView 拆分成了14类与3个协议，包含该更细节功能的实现。
### JS调用iOS原生API
#### 结构
![](http://www.plantuml.com/plantuml/svg/XLPjJzjM5FxkNt55f2O19rAtRnv2cfYg3TGnZMtvCBhAYI_1O6paE6HexKGfwLBUaa61bVSEA6YZZ0eDQ4B8d_6z3f_w5tQkhvqub6ht0_ZQvzpddESyvznOggLXbEXDuEGFAiwHnRcU_lhq6jqu4b9Ojx29Xh2iO5Emoy_g1xlsvGufJf65RQVq36pjyfvTNgGRPL8u4x2kUBP2k9qlvBojaDqJ-iS2FNn5DbUHzpuiXDcNhLaffScPZ2WdCvOffonHPP23SWgBiguRbcofXYvYFJiA7lNZslNgCTdUbYAng7FmX6uy5oEXg5tRf3DxOkJNA9dB-wyxeYH_14zXYEMEkrWeJcMQb0fUAB3tdhZ17O0W-JVEmRndjdDEBelIruQ2nMznofBrj8R5UuO66OhtJ7aKYzsQaLN4BsHBJieP71R6vDI8dCQeJUthPnozsFxycw_QEZkvKrVNBmA2vQOCb7SsMFS-iFNm8Q8hx-ZfCWG1cyRx3XHHbQYggrP2jOPSU2bbw8DgEckwB4b1qt43ZF6uh6Lb2_V8ut9_obJ7BF0O7TCmC_zIrXKDczphiH08Z9_Q3AuxY1A8D1P3vEqMdJnWaSPY0VX4x_SPR8AV1GfY_qn30opKdhtjTI3rHkR1nFsyFupKFQwxyoDmBQJUVh9Bawmed9txE9E1wldCw8pYMumPc4Ud5nBx6mXBmHfE0-yGs6q7Ut75OdPb5xg8H-Rnvw7SofEag0he4AlZkENOZqIHSMqyShX30vbkJLzFxf2NXtxPMjNLRsJD59PKVHXa3qN_JWKc1WtplgpghlBwT6r2-ip3XPBuklT71sfA7eLCh0HrFvpXDa4XNUVddUenL2SWez4MqZBIS8Okx9FYBYdDDsp6Z8pF0ambkdpI32aSSfSxWFP-ewU7pQxt1jB6t-JaogxMd1U74emQZEmgYq92t3xahx3WVhMCCMGCWhO5-c-nVb00zkoBYbrTOdCFCHDkPwhf8OjPyZv2eRGfJmW0OfSNoCOXfpxHIrVFtb_ENVyvJMPswjMgpmgygnzVCJBMVoUbevv-dZIzo9FvvHj8ISEoZ546nHj1kXkdnP9zjTuurQy7NPgtgni3-YTnYA2lsxvuGyyhOC91dBNf8ChxopNVh_45I1_GFuq3BII_ptKBHBOGusSWeVh9KqYNpxy17ULKNJ5oyHnEZgXM_4PPuu6onWFbZF1wHi6VFzaNPLNXTmPmHAfBT9NTH1oBayaDENrWhcSrxKP9_CwKWgq7-TBPrxJmLolbh18BvwIuqjCFbzxrv2AGmH78vEM03aIIzIs01pAvNVsV_SPtEDDBD9uqb0cGOvUxNFdbJvtJ2bc_8iMPUcsBeGK5skKjzpPYblMPAMVgdNFqMeeyweewbGFwl2P6sgFiYYgKa1WnelKdQx0J4ScVsPMLaFYp-5Zi47z2SBnJhhckiLYK7BCTWiGYyNYq_d8DTc4XqTjsjmsrntvaZVeWXjYEIrVoneIR5EfiuL97cggFe51kI0KvtX2rsyqvVdi0Qo17A0hTV6LNpfeI5hp-OCXf4sCTi3qTqvqBKdiAum0S05VWemmPCGqFMdwS3Rqrsl43sODlelT_tRY40Zuy2ZUmf9R53vgo8NlJJFckJaZX178BkFbMgFEHXvZKO2u2Pg9NGescQGBuWR410LOyIudtMEDK_rVOe06acKBIcC0cKyhMBAEIl_qF)

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


