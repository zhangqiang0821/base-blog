---
title: 微信授权和jssdk部分接口的笔记
date: 2018-07-18 10:18:38
tags: [微信,jssdk]
categories: js
---

### 微信授权和jssdk部分接口的笔记

#### 准备工作
- 微信接口测试号 [申请地址](https://mp.weixin.qq.com/debug/cgi-bin/sandboxinfo?action=showinfo&t=sandbox/index)
- 微信开发者工具

<!-- more -->

####  授权登录
##### 流程
- 前端引导用户授权（跳转授权链接）
```js
var APPID = 'wx763ca0ce2d0b75e3'  // 公众号的appid
var REDIRECT_URI = 'http://127.0.0.1:8081/VDS/wechat/auth.html' // 重定向的页面，请求微信授权页面后，微信服务端重定向的页面，微信会在这个页面的url后面加上code

var SCOPE = 'snsapi_userinfo'
var STATE = 'ddgr56'
var AUTH_URL = 'https://open.weixin.qq.com/connect/oauth2/authorize?appid='
+ APPID + '&redirect_uri=' + REDIRECT_URI + '?url=$URL' + '&response_type=code&scope=' + SCOPE + '&state=' + STATE + '#wechat_redirect'

// 授权
function auth(url) {
  window.location.href = getAuthUrl(url)
}
```
- 上一步返回一个code，把code传回服务端
- 服务端通过code，请求微信服务器，拿到 access_token 和  openid
- 一般，服务端返回openid给前端，授权成功

##### **注意**
- 公众号配置网页授权域名，此域名必须是客户端配置的回调地址的域名，只需要域名，不能带‘http’
	- ![Alt text](./1531876963902.png)
- 正式的公众号不能用ip

#### 微信jssdk使用前置条件
1.  配置接口安全域名
![Alt text](./1531879830281.png)
此域名是需要使用微信jssdk的页面的域名
2.  通过config接口注入权限验证配置
```js
wx.config({
    debug: true, // 开启调试模式,调用的所有api的返回值会在客户端alert出来，若要查看传入的参数，可以在pc端打开，参数信息会通过log打出，仅在pc端时才会打印。
    appId: '', // 必填，公众号的唯一标识
    timestamp: , // 必填，生成签名的时间戳
    nonceStr: '', // 必填，生成签名的随机串
    signature: '',// 必填，签名
    jsApiList: [] // 必填，需要使用的JS接口列表
});
```
3. 获取signature
- 前端传递3个参数到后端
    timestamp 时间戳
    nonceStr:  随机字符串
    url: 需要调用jssdk接口的页面url
 - 后端先请求微信服务器获取jsapi_ticket（每次签名时，都需要这个参数，所以，这个参数需要服务端保存起来，过期前不用每次请求）
- 后端对前端传回的参数进行签名（算法查文档），得到signature，返回给前端


#### **注意**
- 获取jsapi_ticket，需要一个参数   access_token， 但是这个 access_token和授权登录的 access_token不一样！！！！！ 通过下面的接口获得
```js
https://api.weixin.qq.com/cgi-bin/token?grant_type=client_credential&appid=${appId}&secret=${secret}
```
- 关于nonceStr参数，前端是大写S  后端是小写s ！！！！！


#### 定位 （wx.getLocation）

```js
wx.getLocation({
type: 'wgs84', // 默认为wgs84的gps坐标，如果要返回直接给openLocation用的火星坐标，可传入'gcj02'
success: function (res) {
var latitude = res.latitude; // 纬度，浮点数，范围为90 ~ -90
var longitude = res.longitude; // 经度，浮点数，范围为180 ~ -180。
var speed = res.speed; // 速度，以米/每秒计
var accuracy = res.accuracy; // 位置精度
}
});
```

#### **注意事项**
- 关于type参数，如果需要使用第三方地图服务反向获取地址，需要正确设置这个值（具体看第三方地图服务需要的坐标类型）
- 如果要获取地址，可以使用腾讯位置服务的 [逆地址解析](http://lbs.qq.com/webservice_v1/guide-gcoder.html)
- 上面接口需要使用jsonp请求，并设置  output 为 ‘jsonp’ ！！


#### 获取图片

```js
wx.chooseImage({
count: 1, // 默认9
sizeType: ['original', 'compressed'], // 可以指定是原图还是压缩图，默认二者都有
sourceType: ['album', 'camera'], // 可以指定来源是相册还是相机，默认二者都有
success: function (res) {
var localIds = res.localIds; // 返回选定照片的本地ID列表，localId可以作为img标签的src属性显示图片
}
});
```

#### 获取图片接口返回的数据转base64
```
wx.getLocalImgData({
localId: '', // 图片的localID
success: function (res) {
var localData = res.localData; // localData是图片的base64数据，可以用img标签显示
}
});
```

- 在ios上面，这个接口返回的是base64的图片，可以直接显示
- ios上面返回的头部是 data:image/jgp   可以替换成  data:image/jpeg 防止出现兼容问题
- android上，返回的数据不包含头部，需要手动添加  ```data:image/jpeg;base64,```
- android上，返回的数据可能包含换行，需要去掉换行
```js
wx.getLocalImgData({
                localId: localIds[i],
                success: function (res) {
                    var localData = res.localData;
                    if (localData.indexOf('data:image') != 0) {
                        localData = 'data:image/jpeg;base64,' +  localData
                    }
                    localData = localData.replace(/\r|\n/g, '').replace('data:image/jgp', 'data:image/jpeg')
                    showImage(localData)
                }
            });
```
