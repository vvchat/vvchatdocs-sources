title: VVChat开放平台文档
---
**VVChat**

## 事件结构

```
{
	"event": "join-group-for-invite",
	"version": "v1",
	"data": {
		"robot": {
			"app_id": "qiyunxin",
			"open_id": "CWYlCXOccx73NnVGntzatmSLEzgoFMTO",
			"robot_no": "xds3345"
		},
		"session": {
			"session_open_id": "hwWj3RPotzas_nQwGHTqwB0KnUy8hLBp",
			"session_type": "2"
		},
		"trigger": {
			"open_id": "CWYlCXOccx73NnVGntzatmSLEzgoFMTO",
			"target": "user"
		}
	}
}
```

event: 事件名称

version: 事件版本

data：事件数据

    robot：为机器人信息
    	app_id: 机器人绑定的app_id
    	open_id： 机器人绑定的open_id
    	robot_no：机器人的唯一编号
    
    session: 触发事件的会话信息（群或个人）
      session_type: 会话类型 1. 单聊 2. 群聊
      session_open_id: 会话open_id 如果是个人就是个人的open_id 如果是群组就是群的open_id
      
    trigger: 触发事件的对象
      target: 目标类型 user：为用户
      open_id： 如果target=user open_id即为个人open_id  
      
      
## session-open 

用户打开了机器人所在的群的会话

```
{
	"event": "session-open",
	"version": "v1",
	"data": {
		"robot": {
			"app_id": "qiyunxin",
			"open_id": "CWYlCXOccx73NnVGntzatmSLEzgoFMTO",
			"robot_no": "xds3345"
		},
		"session": {
			"session_open_id": "hwWj3RPotzas_nQwGHTqwB0KnUy8hLBp",
			"session_type": "2"
		},
		"trigger": {
			"open_id": "CWYlCXOccx73NnVGntzatmSLEzgoFMTO",
			"target": "user"
		}
	}
}
```
      

## join-group-for-invite 
机器人被拉入群

```
{
	"event": "join-group-for-invite",
	"version": "v1",
	"data": {
		"robot": {
			"app_id": "qiyunxin",
			"open_id": "CWYlCXOccx73NnVGntzatmSLEzgoFMTO",
			"robot_no": "xds3345"
		},
		"session": {
			"session_open_id": "hwWj3RPotzas_nQwGHTqwB0KnUy8hLBp",
			"session_type": "2"
		},
		"trigger": {
			"open_id": "CWYlCXOccx73NnVGntzatmSLEzgoFMTO",
			"target": "user"
		}
	}
}
```

## at (@)
机器人被AT(@)

```
{
	"event": "at",
	"version": "v1",
	"data": {
		"content": {
			"text": "您好"
		},
		"robot": {
			"app_id": "test",
			"open_id": "lJsDBB01QzGpBKOC7uaZB6D0QGZWBMCS",
			"robot_no": "xds2323"
		},
		"session": {
			"session_open_id": "8g8ZW-JHWSNvTvMCu9V9uF58NaIjmjy2",
			"session_type": "2"
		},
		"trigger": {
			"open_id": "T8UDxMFSYBeU-N4jCCy0PIYk7KavHYnU",
			"target": "user"
		}
	}
}
```

content->text: 为用户at所发的消息









