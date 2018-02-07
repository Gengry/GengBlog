---
title: sourcetree免登陆授权
date: 2018-02-07 13:51:03
tags:
- sourcetree
categories:
- 工具
---

git桌面客户端sourcetree需要注册登录，但是sourcetree官网注册的验证码是走的google，国内无法完成注册。  
<!--more-->
安装后修改配置文件：C:\Users\lenovo\AppData\Local\Atlassian\SourceTree\accounts.json
```json
[
  {
    "$id": "1",
    "$type": "SourceTree.Api.Host.Identity.Model.IdentityAccount, SourceTree.Api.Host.Identity",
    "Authenticate": true,
    "HostInstance": {
      "$id": "2",
      "$type": "SourceTree.Host.Atlassianaccount.AtlassianAccountInstance, SourceTree.Host.AtlassianAccount",
      "Host": {
        "$id": "3",
        "$type": "SourceTree.Host.Atlassianaccount.AtlassianAccountHost, SourceTree.Host.AtlassianAccount",
        "Id": "atlassian account"
      },
      "BaseUrl": "https://id.atlassian.com/"
    },
    "Credentials": {
      "$id": "4",
      "$type": "SourceTree.Model.BasicAuthCredentials, SourceTree.Api.Account",
      "Username": "",
      "Email": null
    },
    "IsDefault": false
  }
]
```

<blockquote class="blockquote-center">今天最好的表现，是明天最低的要求。</blockquote>