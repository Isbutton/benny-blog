### whistle

#### 遇到的问题
1. Method为CONNECT，配置代理未生效
```
Method为CONNECT | Host为Tunnel To
很可能是因为没有开启https 【Capture HTTPS CONNECTs】
```


2. 修改请求回包，配置的代理针对一个域名下的某个请求，通过请求的参数来区分
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

3. 替换整个请求回包
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
   