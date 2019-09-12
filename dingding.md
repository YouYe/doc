# 如何快速开发钉钉微应用

## 钉钉微应用类型

#### 企业内部应用

企业内部开发是指“开发企业内部应用”供企业内部的人员使用。企业可以选择由企业内部的开发者进行开发，或者由企业授权定制服务商进行开发。

#### 第三方企业应用

第三方企业应用开发，是指开发者以钉钉、企业之外的第三方身份，基于钉钉的开放能力开发应用，并提供给钉钉上的其他组织使用。

> [钉钉应用市场](https://appcenter.dingtalk.com/)
			

## 快速创建一个钉钉微应用
+ 名称
+ logo
+ 简介
+ 开发方式
+ 首页链接（后缀加上 ?corpid=$CORPID$ 就可以在访问链接时拿到corpid，这个适用于第三方企业应用，企业内部开发应用肯定知道自己的corpid）
+ 服务器出口IP（开发方式为企业内部自主开发，一个IP只能供一个钉钉微应用使用，开发方式为授权给服务商开发，一个IP最多供三个钉钉微应用使用）

## 名词解释

+ CorpId：企业ID

+ AgentId：在创建应用时，系统会自动生成一个AgentId，可用于发送企业会话消息等场景。

+ AppKey：在创建应用时，系统会自动分配一对AppKey和AppSecret，该AppKey是应用开发过程中的唯一性标识。

+ AppSecret：AppSecret和上面AppKey一同生成，使用AppKey和AppSecret来换取access_token。

## 引入钉钉jsapi,拿到dd

#### npm安装和使用
```

	npm install dingtalk-jsapi --save
	
	
	import * as dd from 'dingtalk-jsapi';
	
```

#### 浏览器引入

```

<script src="https://g.alicdn.com/dingding/dingtalk-jsapi/2.7.13/dingtalk.open.js"></script>


```

## 免登流程

#### 前端拿到免登code码

```

dd.ready(function() {
    dd.runtime.permission.requestAuthCode({
        corpId: _config.corpId, // 企业id
        onSuccess: function (info) {
                  code = info.code // 通过该免登授权码跟应用服务器端可以获取用户身份
        }});
});


```

#### 服务端处理免登code码，返回员工信息

##### 企业内部应用免登

1.获取access_token

```

ajax.get("https://oapi.dingtalk.com/gettoken", {
    appkey: "",
    appsecret: ""
});

//返回值

{
    "errcode": 0,
    "errmsg": "ok",
    "access_token": "fw8ef8we8f76e6f7s8df8s"
}


```

2.获取用户userid

```

ajax.get("https://oapi.dingtalk.com/user/getuserinfo", {
    access_token: "",
    code: ""
});

//返回值

{
    "userid": "****",
    "sys_level": 1,
    "errmsg": "ok",
    "is_sys": true,
    "errcode": 0
}


```

3.拿到userid之后，企业内部应用是可以对企业的员工施行增删改查的

> [增删改查接口文档地址](https://ding-doc.dingtalk.com/doc#/serverapi2/ege851#AaRQe)


##### 第三方应用免登

1.计算签名

参数|说明
---|:--:
accessKey | 应用的appId
timestamp | 当前时间戳，单位是毫秒
signature | 通过appSecret计算出来的签名值

```

const crypto = require('crypto');
let timestamp = Math.ceil(new Date().getTime() / 1000);
let signatureContent = `${appSecret}${timestamp}`;
let hash = crypto.createHash('sha256');
hash.update(signatureContent);
let signature = hash.digest('base64');


```

2.获取员工信息

```

ajax.post("https://oapi.dingtalk.com/sns/getuserinfo_bycode?accessKey=1111&timestamp=11111&signature=dsaadad", {
    tmp_auth_code: ""
});

//返回值

{ 
    "errcode": 0,
    "errmsg": "ok",
    "user_info": {
        "nick": "张三", //用户在钉钉上面的昵称
        "openid": "liSii8KCxxxxx", //用户在当前开放应用内的唯一标识
        "unionid": "7Huu46kk" //用户在当前开放应用所属企业内的唯一标识
    }
}


```

## jsapi鉴权

> [鉴权文档](https://ding-doc.dingtalk.com/doc#/dev/uwa7vs)

> [jsapi功能总览](https://ding-doc.dingtalk.com/doc#/dev/swk0bg)








