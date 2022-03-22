### whistle

#### 使用点
##### 1. Method为CONNECT，配置代理未生效
```
Method为CONNECT | Host为Tunnel To
很可能是因为没有开启https 【Capture HTTPS CONNECTs】
```


##### 2. 修改请求回包，配置的代理针对一个域名下的某个请求，通过请求的参数来区分
`区分改请求是GET还是POST`
```
POST
# /wechat_query/ reqScript://{post_specify_param.js} 

post_specify_param.js

if (body.split("&")[5].split("=")[1] === 'activity_recomm') {
  rules.push(url + ' resBody://{res}');
  values['res'] = '{"ret" : 0,"msg" : "ok","recomm_info" : {...}}'
} 
```

```
GET
# /get_wzry_zone_data.php/ resScript://{get_specify_param.js}


get_specify_param.js

rules.push(url + ' resBody://{res}');
var queryStr = parseUrl(url).query;
var queries = parseQuery(queryStr);
if (queries.action == "getMainHeroAllFeatureRank") {
  values['res'] = '{}'
}
```

```
JSONP
# /wechat_query/ resScript://{jsonp_specify_param.js} 


var queryStr = parseUrl(url).query;
var queries = parseQuery(queryStr);

// _h5pay25455
var jsonpMethod = queries.format.substr(6)

if (queries.cmd == 26) {
    rules.push(url + ' resBody://{res}');
    var res = {"ret" : 0,"msg" : "ok","recomm_info" : {...}}
}

values['res'] = jsonpMethod + '(' + JSON.stringify(res) + ')'
```

##### 3. 替换整个请求回包
```
# https://XXX/h5/store/XXX.php  resReplace://{whole_replace.js}

whole_replace.js

/^\{.*\}$/ig: {"ret":"0","newList":[null,null,null]}


# /XXX/h5/ads/XXX.json/ resBody://{whole.js}

whole.js
{
  "adsId": 43,
  "type": 3,
  "fileType": "json",
  "title": "XXX",
  "name": "store_wzry_zone",
  "data": [
    {}
  ]
}
```
   
##### 4. 在匹配pattern的页面中插入vConsole
安装whistle.inspect插件
```
页面地址 whistle.inspect://
```

```
页面地址 log://
```
可以在whistle的`Tools`里的console台看到页面打印信息

##### 5. 页面跳转
```
/XX.XX.com/XXX/ redirect://https://baidu.com
```

如果跳转地址里有hash路由，则
<pre>
``` testUrl
https://XX.XX.com/h5/store/index.shtml?XXX=XXX#/detail?XXX=XXX
```
/XX.XX.com/XXX/ redirect://{testUrl}
</pre>



##### 6. 模拟回包里的CSP
```
页面地址 resHeaders://{test-resHeaders.json}
```

test-resHeaders.json
```
Content-Security-Policy: default-src 'self' 'unsafe-inline' 'unsafe-eval' webcompt: weixin: data: jsbridge: http://*.sogou.com wss://*.qq.com uniwebview:;report-uri https://XX;img-src * data:;media-src h5tenvideo: http://*.qq.com https://*.qq.com wss://*.qq.com
```

##### 连上whistle后苹果支付无法拉起
```
**.apple.com disable://intercept #屏蔽iap支付抓包
**.icloud.com disable://intercept
```
更多苹果域名可参考：https://github.com/alibaba/lightproxy/issues/149