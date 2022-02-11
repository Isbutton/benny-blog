### scheme url
- 手q内安卓和ios都可以通过scheme url拉起app
  ios内会有确认打开的弹窗；安卓内直接拉起app
- 微信安卓和ios都无法拉起
  微信禁止了scheme url，且现在没有流程可以申请scheme url的白名单
```
setTimeout(function() {
  window.location.href = "weishi://" //唤起APP
}, 800)

```

### Universal Link，App Links
微信屏蔽了Universal Link
https://www.jianshu.com/p/bf529236019b

https://yq.aliyun.com/articles/608583
Universal Links
优点:无缝跳转,用户体验好.可以在微信 QQ中跳转.不会被屏蔽.

缺点:需要服务器配合支持,iOS9之后版本才支持.

https://juejin.im/entry/5912bc79128fe10058695526
ios的universal link 和 安卓的App Links
都需要app侧进行一系列配置，以实现唤起app

现行微信内打开app的方法：
1. 使用应用宝页面跳转
2. 微视自己分享页面到微信，微信打开页面，页面顶部有【打开看看】的按钮，点击按钮，安卓和ios都会有确认弹窗，再打开微视app
3. 小程序跳回app


如果唤醒APP的话，只要你的APP有上架应用宝，然后用户点击的时候链接到应用宝下载页面，如果用户手机上有你的APP微信会弹框问你是否要去另外的APP，那么这样就可以直接跳到你的APP。

https://blog.windstone.cc/front-end/browser-env/hybrid/h52app.html


2. ios的universal link 和 安卓的App Links
都需要app侧进行一系列配置，以实现唤起app。可行性不高，意味着每个接入的app都需要进行配置。



这里同步一下微信内H5拉起app方案的可行性。
1. 使用scheme url：
微信禁止了scheme url，且现在没有流程可以申请scheme url的白名单。
已做过测试不可行。

2. universal link 
微信也禁止了universal link跳app

调研了一下针对微信的处理方法：
1. 使用应用宝applink跳转（在拉起app时会有【即将离开微信 打开XXX】的确认弹窗）
2. 提示用户在浏览器里打开（浏览器没有拉起app限制）
3. 小程序拉起app。

根据小程序跳回app的限制，以现在的操作路径：微视app => 微视小程序 => 腾讯充值小程序，腾讯充值小程序没有跳回app的能力。

以上，想在H5实现直接拉起微视app暂时走不通。

参考：

https://blog.windstone.cc/front-end/browser-env/hybrid/h52app.html
https://juejin.im/post/5b7efb2ee51d45388b6af96c#heading-10