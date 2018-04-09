### 用户信息授权

#### 准备工作

移动应用**VVChat**登录是基于[OAuth2.0协议标准](http://oauth.net/2/)构建的OAuth2.0授权登录系统。

在进行**VVChat** OAuth2.0授权登录接入之前，在**VVChat**开放平台注册开发者帐号，并拥有一个已审核通过的移动应用，并获得相应的AppID和AppKey，申请**VVChat**登录且通过审核后，可开始接入流程。

#### 授权流程说明

授权流程分三步：

1. 第三方发起**VVChat**授权登录请求，**VVChat**用户允许授权第三方应用后，**VVChat**会拉起应用，并且带上授权临时票据code参数；

2. 通过code参数加上AppID和AppKey等，通过API换取访问token；

3. 通过token进行接口调用，获取用户基本数据资源或帮助用户实现基本操作。（根据scope的不同可以获取到不同的权限。）

##### 第一步：请求CODE

开发者需要配合使用**VVChat**开放平台提供的SDK进行授权登录请求接入。正确接入SDK后并拥有相关授权域（scope，

）权限后，开发者移动应用会在终端本地拉起**VVChat**应用进行授权登录，**VVChat**用户确认后**VVChat**将拉起开发者移动应用，并带上授权临时票据（code）。

Android平台应用授权登录接入代码示例（请参考Android接入指南）：

```
// 构建登录请求对象
LoginRequest loginRequest = new LoginRequest();
loginRequest.setAppId(appId);
loginRequest.setScope("snsapi_userinfo");
loginRequest.setState("test");
// 发起登录请求
SDKUtils.SendRequest(loginRequest, context);
```

##### 参数说明

| 参数 | 是否必须 | 说明 |
| :--- | :--- | :--- |
| app\_id | 是 | 应用唯一ID，**VVChat**开发平台下发 |
| scope | 是 | 应用授权域，获取个人信息填写 snsapi\_userinfo |
| state | 否 | 用于保持请求与回调的状态，将原样返回第三方，该参数可以防止csrf攻击 |

**可拉起**VVChat**打开授权登录页：**

![](/images/Screenshot_2018-01-04-20-40-13.png)

用户点击授权后，**VVChat**客户端会被拉起，跳转至授权界面，用户在该界面点击允许或取消，SDK将授权code传递给调用方。

##### 返回说明

| 返回值 | 说明 |
| :--- | :--- |
| err\_code | 0：用户同意  ，其他代号都表示错误 |
| code | 授权code，用来后续换取token用 |
| state | 第三方传入的安全状态码 |

##### 第二步：通过code获取token

获取第一步的code后，请求以下链接获取token：

```
https://www.xxxxxxx.com/userservices/v2/authorization/token?app_id=APPID&app_key=APPKEY&code=CODE
```

##### 参数说明

| 参数 | 是否必须 | 说明 |
| :--- | :--- | :--- |
| app\_id | 是 | 应用唯一标识 |
| app\_key | 是 | 应用密钥 |
| code | 是 | 授权code |

返回说明

正确的返回：

```
{ 
"open_id":"OPENID",  
"scope":"SCOPE",
"token":"TOKEN"
}
```

| 参数 | 说明 |
| :--- | :--- |
| open_id | 授权用户唯一标识 |
| scope | 授权作用域 |
| token | 接口调用凭证 |

**错误返回样例（http状态码为400）：**

```
{"err_code":400,"err_msg":"无效的code"}
```

##### 第三步：通过access_token调用接口

##### 获取用户个人信息 

###### 请求说明

http请求方式: GET

```
https://xxxx//userservices/v2/authorization/userinfo

请求头：
Authorization: TOEKN
```

##### 返回说明

| 参数 | 说明 |
| :--- | :--- |
| open\_id | 用户open\_id, 唯一标识 |
| nickname | 用户昵称 |
| sex | 用户性别  女  男 |



**VVChat**