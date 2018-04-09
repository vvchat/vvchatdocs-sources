title: VVChat开放平台文档
---
**VVChat**

## 签名通用规则
**VVChat**签名生成的通用步骤如下：

第一步，设所有发送或者接收到的数据为集合M，将集合M内非空参数值的参数按照参数名ASCII码从小到大排序（字典序），使用URL键值对的格式（即key1=value1&key2=value2…）拼接成字符串stringA。

特别注意以下重要规则：

1. ◆ 参数名ASCII码从小到大排序（字典序）；
2. ◆ 如果参数的值为空不参与签名；
3. ◆ 参数名区分大小写；
4. ◆ 验证调用返回或**VVChat**主动通知签名时，传送的sign参数不参与签名，将生成的签名与该sign值作校验。
5. ◆ 支付接口可能增加字段，验证签名时必须支持增加的扩展字段

第二步，在stringA最后拼接上key得到stringSignTemp字符串，并对stringSignTemp进行MD5运算，再将得到的字符串所有字符转换为大写，得到sign值signValue。

举例：

```
假设传送的参数如下：
app_id： qyxd930ea5d5a258f4f
store_no： 10000100
title： test
amount： 1
nonce_str： ibuaiVcKdpRxkhJA
```

第一步：对参数按照key=value的格式，并按照参数名ASCII字典序排序如下：

```
stringA="amount=1&app_id=qyxd930ea5d5a258f4f&nonce_str=ibuaiVcKdpRxkhJA&store_no=10000100
&title=test";
```

第二步：拼接API密钥：

```
stringSignTemp=stringA+"&key=192006250b4c09247ec02edce69f6a2d" //注：key为平台下发的
sign=MD5(stringSignTemp).toUpperCase()="9A0A8659F005D6984697E2CA0A9CF3B7" //注：MD5签名方式
```

##基础签名

需要基础签名的接口需要将公共参数设置到http的header里

#### 公共参数

公用参数是在 http的header里,所有接口都需要带公共参数

| 字段名 | 变量名 | 类型 | 描述 |
| :--- | :--- | :--- | :--- |
| 应用ID | app\_id | string | 应用ID由平台下发 |
| 随机码 | noncestr | string | 随机字符串码 |
| 时间戳 | timestamp | string | 时间戳 10位 到秒 |
| 签名 | sign | string | 签名字符串，如果是基础签名就是基础签名的sign，如果是联合签名就是联合签名出来的sign（联合签名类似xxxxx.xxxxxxx这种形式） |

***注意： 基础签名不是key=value的形式 是直接拼接的值***

```
sign = MD5( appKey + noncestr + timestamp).toUpperCase()
```

***一般安全级别的接口，可能只需要基础签名即可。***



## 数据签名

***安全级别中等的接口，需要基础签名即可。***

数据签名规则与通用签名规则一致,签名的数据为接口请求数据

## 联合签名
***安全级别要求高的接口，需要进行联合签名。***


第一步：

```
stringA="amount=1&app_id=qyxd930ea5d5a258f4f&nonce_str=ibuaiVcKdpRxkhJA&store_no=10000100
&title=test";
dataAndBaseSign = MD5 (stringA+"&key=192006250b4c09247ec02edce69f6a2d"+"&basesign=dgce5thdy8t3t6hk89grd3d5").toUpperCase()
//注：key为平台下发的
// basesign 为基础数据签名所得
```

第二步：

```
sign = basesign+"." + dataAndBaseSign  //基础签名 + "." + 数据基础混合签名
// 注意中间需要用“.”隔开
```

### 举例说明 （以此例子所示签名方式为准）

以代付接口参数为例：

app\_id=test

app\_key=123456

基础签名：

```
baseSign = MD5( appKey + noncestr + timestamp).toUpperCase()
```

数据联合签名:

```
// 按照 ASSCI排序请求参数：
dataSignString = "amount=1000&in_open_id=xd8wjr9jr02kjf823jse94kio8&notify_url=http://www.test.com/callback&out_open_id=lJsDBB01QzGpBKOC7uaZB6D0QGZWBMCS&out_order_no=2334234343zz&title=test"
// 拼接appkey和基础签名
dataSign = dataSignString + "&key=123456" + "&basesign=" + baseSign

// 获得联合签名
sign = basesign + "." + MD5(dataSign).toUpperCase()
```

纯数据签名：

```
// 按照 ASSCI排序请求参数：
onlyDataSignString = agentpay_no=ds99fjjwekwerjfm&app_id=test&out_order_no=lJsDBB01QzGpBKOC7uaZB6D0QGZWBMCS&status=1&time=1517928240

// 拼接appkey
onlyDataSign = onlyDataSignString + "&key=123456"
// 获得数据签名
onlySign = MD5(onlyDataSign).toUpperCase()
```



## 接口基础说明

#### 测试基地址: http://dev.vvchat.im

#### 测试数据

app\_id: test

app\_key: 123456

out\_open\_id:  lJsDBB01QzGpBKOC7uaZB6D0QGZWBMCS  // 出款账户

***注意：接口使用那种签名会有标记***

## 统一下单接口

**数据签名**

> 请求地址

POST /paybusapi/v1/pay/unifiedorder

> 请求参数


| 字段名 | 变量名 | 必填 | 类型 | 示例值 | 描述 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 应用ID | app\_id | 是 | string | test | 平台发放 |
| 店铺编号 | store\_no | 是 | string | s123 | 平台发放 |
| 随机字符串 | nonce\_str | 是 | string | Qdki7sdj | 随机字符串不长于32位 |
| 签名 | sign | 是 | string | C28SD98FS98DS77DF98S7DS99DF76SD | 签名，详见签名说明 |
| 签名类型 | sign\_type | 是 | string | MD5 | 目前仅支持MD5 |
| 第三方交易编号 | out\_trade\_no | 是 | string | 2017928373488 | 第三方系统内部交易编码， |
| 交易金额 | amount | 是 | int | 100 | 订单交易金额，单位分 |
| 通知地址 | notify\_url | 是 | string | [http://xxxx.com](http://xxxx.com) | 异步通知地址 |
| 场景类型 | scene\_type | 是 | string | APP | 场景类型,目前支持 APP |
| 交易标题 | title | 是 | string | 小米电视机 | 交易标题 |
| 交易备注 | remark | 否 | string | 备注 | 备注 |

> 错误返回 400

当http状态为400的时候 将会有如下数据

| 字段名 | 变量名 | 类型 | 示例值 | 描述 |
| :--- | :--- | :--- | :--- | :--- |
| 错误代号 | err\_code | int | 400 | 400一般是指传给服务器的数据格式错误 |
| 错误描述 | err\_msg | string | 数据格式有误！ | 错误信息描述 |

> 成功返回 200

当http状态为200时 将会有如下数据

| 字段名 | 变量名 | 类型 | 示例值 | 描述 |
| :--- | :--- | :--- | :--- | :--- |
| 应用ID | app_id | string | test | 调用接口提交的应用ID |
| 店铺编号 | store_no | string | s1234 | 调用接口提交的店铺编号 |
| 随机码 | nonce_str | string | IDS86ZD89ZD | 平台返回的随机码 |
| 场景类型 | scene_type | string | APP | 调用接口提交的场景类型 |
| 预付款编号 | imprest_code | string | d87sd8d7f99djf98vjkdnjjdf23 | 预付款编号，后续支付的时候需要用到 |
| 签名 | sign | string | zd8s7f6jdk4dnf0sj | 平台返回的签名，用户校验返回的数据真伪性 |

> 通知返回

第三方业务处理成功后返回 success  \(不区分大小写\)

| 字段名 | 变量名 | 类型 | 示例值 | 描述 |
| :--- | :--- | :--- | :--- | :--- |
| 应用ID | app\_id | string | test | 平台appid |
| 交易编号 | trade\_no | string | 201712023384923834 | 唯一交易编号 |
| 第三方交易编号 | out\_trade\_no | string | xxxxxxx | 第三方平台的交易编号 |
| 用户openid | open\_id | string | xxxxxxxxxxx | 用户唯一标示 |
| 交易时间 | trade\_time | string | 1519631690 | 10位时间戳 |
| 付款时间 | pay\_time | string | 1519631690 | 10位时间戳 |
| 交易金额 | amount | string | 100 | 单位分 |
| 签名 | sign | string | xxxxxxxxxxxxxxxxxxx | 平台返回的签名，用户校验返回的数据真伪性 |

## 查询交易信息

**无需签名**

> 请求地址

GET  /paybusapi/v1/pay/unifiedorder

> 请求参数

| 参数   |      类型      |      作用域      | 参考值 | 说明 |
|----------|:-------------:|------:|------:|------:|
| imprest_code   |  string       | query|xxxxx| 预付款编号 imprest_code,out_trade_no二选一
| out_trade_no   |  string       | query|xxxxx| 第三方交易号 imprest_code,out_trade_no二选一

> 成功返回 200


| 参数   |      类型      |  参考值 | 说明 |
|----------|:-------------:|------:|------:|
| open_id   |  string       | eyJhbGciOiJSUzI1NiIsInR5cCI6xxxxx| 下单用户唯一标示  |
| store_no |    string   |   xxxx | 店铺编号 |
| out_trade_no |    string   |   xxxxx |第三方唯一编号 |
| trade_no |    string   |   xxxx | 平台唯一交易编号 |
| imprest_code |    string   |   xxxx | 预付款编号 |
| amount |    int64   |   1000 | 交易金额，单位分 |
| title |    string   |   xxxx | 交易标题 |
| remark |    string   |   xxxx | 交易备注 |
| notify_url |    string   |   http://www.xxx.com | 回调地址 |
| scene_type |    string   |   APP | 场景类型 APP,WEB |
| status |    int   |   1 | 0.待支付 1.已支付 |


> 错误返回 400

| 参数   |      类型      |  参考值 | 说明 |
|----------|:-------------:|------:|------:|
| err_code   |  int       | 400| 错误代号  |
| err_msg |    string   |   请求失败！ | 错误信息 |

## 代付接口
**联合签名**

将代付账号的**VVChat**余额转向指定的用户**VVChat**账户

> 请求地址

POST /paybusapi/v1/app/agentpay

> 请求参数

| 字段名 | 变量名 | 必填 | 类型 | 示例值 | 描述 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 第三方订单号 | out\_order\_no | 是 | string | 201809282376 | 第三方商户系统里的订单编号 |
| 入款open\_id | in\_open\_id | 是 | string | xd8wjr9jr02kjf823jse94kio8 | 入款open\_id ，即收款人的open\_id |
| 出款open\_id | out\_open\_id | 是 | string | lJsDBB01QzGpBKOC7uaZB6D0QGZWBMCS | 出款open\_id，即出款人的open\_id，此open\_id 需要跟appid绑定才有效 |
| 代付金额 | amount | 是 | int64 | 1000 | 代付金额（单位：分） |
| 通知地址 | notify_url | 是 | string | [http://www.test.com/callback](http://www.test.com/callback) | 通知地址 |
| 标题 | title | 是 | string | 游戏代付 | 标题 |
| 备注 | remark | 否 | string | 这是备注 | 备注 |

> 异步通知返回参数

注意 ：回调可能存在重复请求情况，第三方需要做除重处理。 第三方做好返回数据的校验，防止出现恶意请求。

| 字段名 | 变量名 | 类型 | 描述 |
| :--- | :--- | :--- | :--- |
| 应用ID | app\_id | string | 应用ID |
| 第三方订单号 | out\_order\_no | string | 第三方订单号 |
| 代付编号 | agentpay\_no | string | 代付唯一编号 |
| 代付状态 | status | int | 0.等待代付 1.代付成功 2.代付失败 |
| 代付时间 | time | int | 当前UTC时间戳，从1970年1月1日0点0 分0 秒开始到现在的秒数\(String\) |
| 签名 | sign | string | 签名数据，详见签名算法 |


## 获取访问凭证(token)

**无需签名**

获取用户调用接口的凭证，拥有此凭证后才能调用特定的接口

http状态码为200 将返回成功返回的参数。http状态码为400 将返回错误返回参数

> 请求地址

GET  /userservices/v2/authorization/token?app_id=APPID&app_key=APPKEY&code=CODE

> 请求参数

| 参数   |      类型      |      作用域      | 参考值 | 说明 |
|----------|:-------------:|------:|------:|------:|
| app_id   |  string       | query|test| 平台生成  |
| app_key |    string   |   query |xxxxx| 平台生成 |

> 成功返回 200

| 参数   |      类型      |  参考值 | 说明 |
|----------|:-------------:|------:|------:|
| open_id   |  string       | IqxDpc-s6S_9RaXMmag39YVH7W810Z57| 用户唯一标示  |
| scope |    string   |   snsapi_userinfo | 权限 |
| token |    string   |   eyJhbGciOiJSUzI1NiIsInR5cCI6xxxxx | 访问凭证 |
| expire_in   |  int       | 1522821617 | 失效时间,在此时间点后token自动失效，<br/>格式为10位数到秒的时间戳 int类型，类似：1522821617  |

> 错误返回 400

| 参数   |      类型      |  参考值 | 说明 |
|----------|:-------------:|------:|------:|
| err_code   |  int       | 400| 错误代号  |
| err_msg |    string   |   请求失败！ | 错误信息 |

## 获取用户资料

**无需签名**

获取用户的资料，包括唯一标示，头像，昵称等等

> 请求地址

GET  /userservices/v2/authorization/userinfo

> 请求参数

| 参数   |      类型      |      作用域      | 参考值 | 说明 |
|----------|:-------------:|------:|------:|------:|
| Authorization   |  string       | header|eyJhbGciOiJSUzI1NiIsInR5cCI6xxxxx| 访问凭证token 

> 成功返回 200


| 参数   |      类型      |  参考值 | 说明 |
|----------|:-------------:|------:|------:|
| open_id   |  string       | eyJhbGciOiJSUzI1NiIsInR5cCI6xxxxx| 用户唯一标示  |
| nickname |    string   |   小明 | 错误信息 |
| avatar |    string   |   http://xxxxx.com/avator/xxxx | 头像 |
| sex |    string   |   男 | 错误信息 |



> 错误返回 400

| 参数   |      类型      |  参考值 | 说明 |
|----------|:-------------:|------:|------:|
| err_code   |  int       | 400| 错误代号  |
| err_msg |    string   |   请求失败！ | 错误信息 |


## 发送消息

**无需签名**

可以向用户发送消息

http状态码为200 将返回成功返回的参数。http状态码为400 将返回错误返回参数

> 请求地址

POST  /messageapi/v1/authorization/sendmsg

Content-Type: application/json
> 请求参数

| 参数   |      类型      |      作用域      | 参考值 | 说明 |
|----------|:-------------:|------:|------:|------:|
| Authorization   |  string       | header|eyJhbGciOiJSUzI1NiIsInR5cCI6xxxxx| 平台生成  |
| type |    string   |   body |text| 消息类型 |
| content |    string   |   body |this is msg!| 消息内容 |

> 成功返回 200

无需处理返回参数 判断http状态码为200 必定成功 否则失败。

> 错误返回 400

| 参数   |      类型      |  参考值 | 说明 |
|----------|:-------------:|------:|------:|
| err_code   |  int       | 400| 错误代号  |
| err_msg |    string   |   请求失败！ | 错误信息 |












