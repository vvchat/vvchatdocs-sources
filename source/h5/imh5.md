## 使用前必读
请先了解JSSDK使用说明文档：[JSSDK文档](/jssdk/jssdk.html)


## 用户信息授权

#### 准备工作

**VVChat**登录是基于[OAuth2**VVChat**.0协议标准](http://oauth.net/2/)构建的OAuth2.0授权登录系统。

在进行**VVChat** OAuth2.0授权登录接入之前，在**VVChat**开放平台注册开发者帐号，并拥有一个已审核通过的应用，并获得相应的AppID和AppKey，申请**VVChat**登录且通过审核后，可开始接入流程。

**授权流程说明**

授权流程分三步：

1. 第三方发起**VVChat**授权登录请求，**VVChat**用户允许授权第三方应用后，**VVChat**会拉起应用，并且带上授权临时票据code参数；

2. 通过code参数加上AppID和AppKey等，通过API换取访问token；

3. 通过token进行接口调用，获取用户基本数据资源或帮助用户实现基本操作。（根据scope的不同可以获取到不同的权限。）

##### 第一步：请求CODE

调用JSSDK的方法（JSSDK的使用说明请参考**JSSDK文档**）

```
vvchat.call("auth",{app_id:"test",scope:scope,state:STATE},function(result){
 			
});
```
##### 参数说明

| 参数 | 是否必须 | 说明 |
| :--- | :--- | :--- |
| app\_id | 是 | 应用唯一ID，**VVChat**开发平台下发 |
| scope | 是 | 应用授权域，获取个人信息填写 snsapi\_userinfo |
| state | 否 | 用于保持请求与回调的状态，将原样返回第三方，该参数可以防止csrf攻击 |


用户点击授权后，**VVChat**客户端会被拉起，跳转至授权界面，用户在该界面点击允许或取消，将会在成功回调方法里将结果返回给调用方。

##### 返回说明

| 返回值 | 说明 |
| :--- | :--- |
| err_code | 0：用户同意  ，其他代号都表示错误 |
| code | 授权code，用来后续换取token用 |
| state | 第三方传入的安全状态码 |

##### 第二步：通过code获取token

获取第一步的code后，请求以下链接获取token：

```
http://BASE_URL/userservices/v2/authorization/token?app_id=APPID&app_key=APPKEY&code=CODE
```

##### 参数说明

| 参数 | 是否必须 | 说明 |
| :--- | :--- | :--- |
| app_id | 是 | 应用唯一标识 |
| app_key | 是 | 应用密钥 |
| code | 是 | 授权code |

返回说明

正确的返回：

```
{ 
"open_id":"OPENID",  
"scope":"SCOPE",
"token":"TOKEN",
"expire_in": 1522821617,
}
```

| 参数 | 说明 |
| :--- | :--- |
| open_id | 授权用户唯一标识 |
| scope | 授权作用域 |
| token | 接口调用凭证 |
| expire_in | 失效时间,在此时间点后token自动失效，<br/> 格式为10位数到秒的时间戳 int类型，类似：1522821617 |


**错误返回样例（http状态码为400）：**

```
{"err_code":400,"err_msg":"无效的code"}
```

##### 第三步：通过token调用接口

##### 获取用户个人信息 

###### 请求说明

http请求方式: GET

```
http://BASE_URL/userservices/v2/authorization/userinfo

请求头：
Authorization: TOKEN
```

##### 请求参数

| 参数 | 是否必须 | 作用域 |说明 |
| :--- | :--- | :--- |:--- |
| Authorization | 是 | header | 获取到的访问凭证（token） |

##### 返回说明

| 参数 | 说明 |
| :--- | :--- |
| open\_id | 用户open\_id, 唯一标识 |
| nickname | 用户昵称 |
| sex | 用户性别  女  男 |


## **VVChat**支付

***准备工作***

在进行**VVChat**支付接入之前，在**VVChat**开放平台注册开发者帐号，并拥有一个已审核通过的应用，并获得相应的AppID和AppKey，申请**VVChat**支付且通过审核后，可开始接入流程。

***支付流程说明***

支付流程分三步：

1. 商户调用**统一下单接口**获取到**imprest_code**

2. 商户用imprest_code调起**VVChat**支付，用户确认支付后完成支付

3. 商户后端将接收到支付通知

##### 第一步：调用统一下单接口，得到imprest_code

[跳转到接口说明](/publicapi/index.html#统一下单接口)

##### 第二步：调起**VVChat**支付

JSSDK调用方式如下：

```
vvchat.call("pay",{imprest_code: IMPREST_CODE,app_id: APPID},function(result){
 			
},function(err){
	
});
```

#### 参数说明

| 参数 | 说明 |
| :--- | :--- |
| app_id | 平台发放的app_id |
| imprest_code | 调用统一下单获取到的imprest_code |

#### 返回说明

| 参数 | 说明 |
| :--- | :--- |
| trade_no | 平台发放的app_id |


##### 第三步：商户收取后端支付通知

请查看[统一下单接口](/publicapi/index.html#统一下单接口)的回调说明

## 发送消息

***准备工作***

在进行**VVChat**发送消息接入之前，在**VVChat**开放平台注册开发者帐号，并拥有一个已审核通过的应用，并获得相应的AppID和AppKey，申请**VVChat**支付且通过审核后，可开始接入流程。

***发送消息流程说明***

发送消息流程分四步：

1. 获取需要发送消息的会话ID(session_id)和会话类型(session_type)

2. 获取发送消息到会话的授权码

3. 获取授权码对应的访问凭证（token）

4. 调用发送消息接口发送消息

##### 第一步： 获取session_id和session_type（目前两个途径可以获取到）

* 获取当前会话信息 （当前会话信息为用户点击商户网页时所在的聊天会话页）

```
vvchat.call("getSession",{},function(result){
   // session_type  1.单聊 2.群聊
   sessionId = result.session_id;
   sessionType = result.session_type;
},function(err){
 		
});
```

* 获取用户选择的会话信息 

此方法将会弹出会话选择界面，用户选择相应的会话后，返回此会话信息

```
vvchat.call("selectSession",{},function(result){
   sessionId = result.session_id;
   sessionType = result.session_type;
},function(err){
 	
});
```

##### 第二步：获取可以在会话里发送消息的授权码


```
scope= `sessionapi_sendmsg:${sessionId}:${sessionType}`
vvchat.call("auth",{app_id:"test",scope: scope,state:STATE},function(result){
	console.log("授权码:",result.code)
});
```

##### 第三步：获取访问凭证（token）

参考[访问凭证接口](/publicapi/index.html#获取访问凭证-token)

##### 第四步：调用发送消息接口发送消息

参考[发送消息接口](/publicapi/index.html#发送消息)

## **VVChat**基础分享

***准备工作***

在进行**VVChat**基础分享接入之前，在**VVChat**开放平台注册开发者帐号，并拥有一个已审核通过的应用，并获得相应的AppID和AppKey，申请**VVChat**支付且通过审核后，可开始接入流程。

***基础分享流程说明***

基础分享流程分三步：

1. 获取基础分享的所需的数据  


2. 获取到数据调起**VVChat**基础分享，用户确认分享后完成分享

3. 调起方会收到分享结果的回调

##### 第一步：请求CODE

调用JSSDK的方法（JSSDK的使用说明请参考**JSSDK文档**）

```
vvchat.call("share",{app_id:"test",share_title:share_title,sub_title:sub_title,share_url:share_url,thumb_url:thumb_url},function(result){

});
```

#### 参数说明

| 参数 |是否必须| 说明 |
| :--- |:---  | :--- |
| app_id |是| 平台发放的app_id |
| share_title |是| 分享标题 |
| sub_title |否| 分享副标题 |
| share_url |是| 分享的链接 |
| thumb_url |否| 预览图链接 |

#### 返回说明

| 参数 | 说明 |
| :--- | :--- |
| code | 分享完成后的code |
| error | 分享完成后的error |



## **VVChat**高级分享

***准备工作***

在进行**VVChat**高级分享接入之前，在**VVChat**开放平台注册开发者帐号，并拥有一个已审核通过的应用，并获得相应的AppID和AppKey，申请**VVChat**支付且通过审核后，可开始接入流程。

***高级分享流程说明***

高级分享流程分三步：

1. 获取高级分享的所需的数据 

2. 获取到数据调起**VVChat**高级分享方法，用户确认分享后完成分享

3. 调起方会收到分享结果的回调

##### 第一步：获取高级分享的所需的数据 

调用JSSDK的方法（JSSDK的使用说明请参考**JSSDK文档**）

```
vvchat.call("share",{app_id:"test",share_title:share_title,sub_title:sub_title,share_url:share_url,thumb_url:thumb_url,share_data_url:share_data_url},function(result){

});
```

#### 参数说明

| 参数 |是否必须| 说明 |
| :--- |:---  | :--- |
| app_id |是| 平台发放的app_id |
| share_title |是| 分享标题 |
| sub_title |否| 分享副标题 |
| share_url |是| 分享的链接 |
| thumb_url |否| 预览图链接 |
| share_ data_url |否| 自定义数据内容 |

#### 返回说明

| 参数 | 说明 |
| :--- | :--- |
| code | 分享完成后的code |
| error | 分享完成后的error |


