title: VVChat开放平台文档
---
**VVChat**


## 获取访问token

下发指令需要先获取token，有效期一般为7200s秒 在没过期之前请做好缓存，不要频繁请求。


> 请求地址

GET /robotapi/v1/token?app_id=xxx&app_key=xxxx

返回格式：

```
{
	"token": "xxxxx",
	"expire_in": 7200
}
```

## 下发机器人指令接口
通过这个接口可以给机器人下发指令

> 请求地址

POST /robotapi/v1/message/send?robot_no=xxx&token=xxx

请求格式：

```
{
	"type": "cmd",
	"session_open_id": "xxxxx",
	"session_type": "2",
	"to_type": "2",
	"to": "8g8ZW-JHWSNvTvMCu9V9uF58NaIjmjy2",
	"content": {
		"cmd": "webview-group-open",
		"params": "{\"height\":\"50%\",\"url\":\"\"}"
	}
}
```

type: 固定为cmd

session_type: 执行指令的会话的类型 1. 单聊 2. 群聊

session_open_id: 执行指令的会话的open_id 如果单聊则为 个人的open_id 如果群里则为 群的open_id

to_type：接收者类型 1. 单聊 2. 群聊

to: 接收者open_id 单聊为个人open_id 群聊为群组open_id

content:

>cmd: 具体指令标示
	
>params：指令参数

## 指令集说明

## send-text （发送文本消息）

cmd: send-text

params:
  
>text: 文本消息内容

## webview-group-open（打开群聊里的webview）

cmd： webview-group-open

params:

> height:  weiview的高度 可以传百分比或固定高度 例如 50% 或 234 （注意固定高度不要带单位）

> url: webview 打开的url地址

## webview-group-close（关闭群聊里的webview）

cmd： webview-group-close

params:

> 无

## webview-group-refresh（刷新群聊里的webview）

cmd： webview-group-refresh

params:

> height:  weiview的高度 可以传百分比或固定高度 例如 50% 或 234 （注意固定高度不要带单位）（不传为保持原来的高度）

> url: webview 打开的url地址

