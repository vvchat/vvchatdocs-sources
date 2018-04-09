title: VVChat文档
---


## 安装使用

**JSSDK Demo下载**

[JSSDK Demo](/resources/resource.html)

 **页面引入**
 
```
<script src="http://xxxxxx/vvchatjssdk-1.0.0.js"></script>

```

**通过config进行配置**

```
vvchat.config({
	debug: true
});

```

**通过ready接口处理成功验证**

```
vvchat.ready(function(){
    //把相关接口放在ready函数中调用来确保正确执行。对于用户触发时才调用的接口，则可以直接调用，不需要放在ready函数中。
});

```

**接口调用说明**

所有js方法都有以下通用参数：

1. method: 方法名
2. options: 调用参数
3. success：接口调用成功时执行的回调函数。
4. error：接口调用失败时执行的回调函数。返回参数结构为 {"err_code":400,"err_msg":"失败"}
5. complete：接口调用完成时执行的回调函数，无论成功或失败都会执行。

**举例说明**

```
vvchat.call(METHOD,{},function(result){
 	
},function(error){

},function(data){

});
```

## 授权接口


> 方法名

auth

> 参数

| 参数   |      类型      |  参考值 | 说明 |
|----------|:-------------:|------:|------:|
| app_id   |  string       | test| 平台发放的appid|
| scope |    string   |   snsapi_userinfo| 权限 snsapi_userinfo(获取用户信息),sessionapi_sendmsg（获取会话发送消息权限） |

> 成功返回

| 参数   |      类型      |  参考值 | 说明 |
|----------|:-------------:|------:|------:|
| code   |  string       | xxxxx| 授权码（调用**获取访问凭证API**可以获取到对应的token）|


## 获取当前会话信息

当前h5在会话里被打开，此接口能获取到当前会话信息

> 方法名

getSession

> 参数

无

> 成功返回

| 参数   |      类型      |  参考值 | 说明 |
|----------|:-------------:|------:|------:|
| session_id   |  string       | IqxDpc-s6S_9RaXMmag39YVH7W810Z57| 会话唯一标示|
| session_type   |  int       | 2 | 会话类型： 1.单聊 2.群聊|

## 获取选中的会话信息

调用此方法将弹出会话选择列表，用户选择了某个会话确认后，将获取到此会话的信息

> 方法名

getSession

> 参数

无

> 成功返回

| 参数   |      类型      |  参考值 | 说明 |
|----------|:-------------:|------:|------:|
| session_id   |  string       | IqxDpc-s6S_9RaXMmag39YVH7W810Z57| 会话唯一标示|
| session_type   |  int       | 2 | 会话类型： 1.单聊 2.群聊|

## 获取会话的授权码
弹出授权页面，用户确认授权后将获取到授权码

> 方法名

auth

> 参数

| 参数   |      类型      |  参考值 | 说明 |
|----------|:-------------:|------:|------:|
| app_id   |  string       | test| 平台发放的appid|
| scope |    string   |   sessionapi_sendmsg:SESSION_ID:SESSION_TYPE| SESSION_ID和SESSION_TYPE为返回的会话信息 |

> 成功返回

| 参数   |      类型      |  参考值 | 说明 |
|----------|:-------------:|------:|------:|
| code   |  string       | xxxxx| 授权码（调用**获取访问凭证API**可以获取到对应的token）|

## 支付

弹出支付付款页面，用户支付成功后回调成功方法

> 方法名

pay

> 参数

| 参数   |      类型      |  参考值 | 说明 |
|----------|:-------------:|------:|------:|
| imprest_code   |  string       | xxxxxxx| 预付款编号 |

> 成功返回

| 参数   |      类型      |  参考值 | 说明 |
|----------|:-------------:|------:|------:|
| trade_no   |  string       | xxxxx | 交易编号 |
