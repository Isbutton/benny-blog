## 背景
之前没有考虑到国外用户。
在做活动时发现加拿大（UTC-6）用户在活动已经开启的时候无法正常参与。

## 分析
前端是根据页面直出的服务器时间来判断的，因为使用的是服务器时间，这一步没有问题
```
<script>
    var _serverTime='<!--#echo var="DATE_GMT" -->'
</script>
```

直出后的是格林威治时间
```
<script>
    var _serverTime='Friday, 11-Feb-2022 09:20:58 GMT'
</script>
```

而前端活动时间配置的是北京时间，但是没有指定UTC+8时区。而东八区和西六区之间有14h的时差。
```
"ActivityBegin": "2022-02-04 00:00:00",//活动开始时间
"ActivityPrice": "7100",
"ActivityNum": "GOODS2201230957002627",
"ActivityEnd": "2022-02-10 23:59:59"
```

比如：
此时北京（东八区）时间为2022-02-04 08:00:00，活动已经开始。
格林威治时间为2022-02-04 00:00:00
而实际此时西六区的时间为2022-02-03 18:00:00。

西六区此时打开页面，服务器为2022-02-03 18:00:00，使用2022-02-03 18:00:00和开始时间2022-02-04 08:00:00做对比，就会认为活动还未开始。`


## 解决
所以在活动时间配置需要指定为UTC+8时区
```
/**
 * 解决国外用户时间判断问题
 * @param {} bjTime 北京时间，格式为2022-02-04 00:00:00 或 2022/02/04 00:00:00
 * return 转换为格林威治标准时间时间戳
 */
export function UTC8TimeToTimeStamp(bjTime) {
  return new Date(`${bjTime.replace(/-/g, "/")} GMT+08:00`).getTime()
}

/**
 * 北京时间转换为本地时间格式
 * @param {} bjTime 北京时间，格式为2022-02-04 00:00:00 或 2022/02/04 00:00:00
 * return 转换为本地时间格式2022-2-4 00:00:00
 */
export function UTC8TimeToLocalTimeFormat(bjTime) {
  let date = new Date(UTC8TimeToTimeStamp(bjTime))
  return `${date.getFullYear()}-${date.getMonth() + 1}-${date.getDate()} ${date.getHours()}:${date.getMinutes()}:${date.getSeconds()}`
}
```

其中，容易被忽略的：
`getTime()`获取的是格林威治标准时间时间戳
而`getFullYear()`等方法获取的是本地时间。


## 其他处理方式
1. 当然，也可以通过`getTimezoneOffset()`方法来获取当前时区和UTC的时差，单位为分钟。

2. 也可以在配置时间的时候配置为国际标准UTC时间格式
```
"OfflineTime": "2098-12-31T16:00:00.000Z",
"OnlineTime": "2020-06-17T16:00:00.000Z"
```
