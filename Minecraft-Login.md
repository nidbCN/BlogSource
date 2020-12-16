title: Minecraft启动器正版登录原理
author: Gaein nidb
categories:
  - 软件教程
tags:
  - Minecraft
  - MC正版
  - 翻译
cover: 'https://s2.ax1x.com/2019/02/08/kNJj1g.md.jpg'
date: 2019-02-08 21:04:35
---
前阵子为了给服务器做个启动器，需要正版登录，于是研究了一下这方面的内容...结果百度上是千篇一律的：
"向MOJANG服务器POST账号密码"
那么我们的当前的问题是：
1、接收账号密码的服务器地址/IP是什么
2、POST的格式
至于如何POST数据，百度一搜一把
于是我研究了一下HeyoX的代码，结果...没看懂
又是一番艰苦卓绝的百度，终于发现了一些有用的东西
https://blessing.studio/minecraft-yggdrasil-api-third-party-implementation
原来MOJANG验证系统叫Yggdrasil啊...或许搜这个能有所帮助
于是，发现了神级WIKI，正式我需要的！
https://wiki.vg/Authentication
这里面详细的写了包括登录、刷新、登出等
以下是翻译内容("(?)是当前无法确定的翻译")：
从 Minecraft 1.6 起引入了一个名为yggdrasil的新认证方案，它完全取代了以前的认证系统。Mojang的另一个游戏——Scrolls，也使用这种身份验证方法。Mojang表示，每个人都应该使用这个认证系统进行自定义登录，但是不应该从用户那里收集凭证。
# 目录：
- 1.认证格式
- 2.错误
- 3.登录
 - 3.1.端点
 - 3.2.有效负载(?Payload)
 - 3.3.响应
- 4.刷新
 - 3.1.端点
 - 3.2.有效负载(?Payload)
 - 3.3.响应
- 5.生效(?Validate)
 - 3.1.端点
 - 3.2.有效负载(?Payload)
 - 3.3.响应
- 6.登出
 - 3.1.端点
 - 3.2.有效负载(?Payload)
 - 3.3.响应
- 7.失效
 - 3.1.端点
 - 3.2.有效负载(?Payload)
 - 3.3.响应
- 8.加入服务器

# 请求格式
所有发送到ygdrasil的请求都发送到以下服务器：
https://authserver.mojang.com
此外，它们还应遵守以下规则：
* 是POST请求吗？
* 将content-type头设置为"application/json"
* 包含一个JSON编码的字典作为有效负载(?Payload)
如果请求成功，服务器将响应：
* HTTP状态码200
* 在下面有一个符合规范的JSON编码字典
但是，如果请求失败，服务器将响应：
* 适当的非200 HTTP状态代码
* 遵循此格式（见下）的JSON编码字典：
{
"error":"错误的简短描述",
"errormessage":"可以向用户显示的较长描述",
"cause":"错误原因"	//可选
}

# 错误
以下是可能遇到的一些错误

| 错误 | 原因 | 错误信息 | 笔记 |
| ------ | ------ | ------ | ------ |
| Method Not Allowed |   | 请求中的方法不允许用于请求URL的资源(?) | 发送了除POST以外方法的请求 |
| Not Found |   | 服务器上未找到任何与URL相匹配的内容 | 发送到了不存在的端点 |
| ForbiddenOperationException | 用户迁移异常 | 无效的凭证。用户已迁移，请使用电子邮件作为用户名 |  |
| ForbiddenOperationException |  | 无效的凭证。用户名或密码错误 |  |
| ForbiddenOperationException |  | 无效的凭证 | 最近使用此用户名进行的登录尝试太多（详见/authenticate）。请注意，用户名和密码可能仍然有效！ |
| ForbiddenOperationException |  | 无效的令牌 | accessToken失效 |
| IllegalArgumentException |  | 访问令牌已经分配了一个配置文件 | 选择配置文件尚未生效 |
| IllegalArgumentException |  | 凭证空 |用户名/密码未提交 |
| IllegalArgumentException |  | 无效的salt版本 | ？？？ |
| Unsupported Media Type |  | 服务器拒绝回复请求，因为请求的格式不受请求方法的支持 | 数据未作为"application/json"提交 |

# 登录
使用用户的密码进行登录

## 端点

``` 
/authenticate
```

## 有效负载

```
{
    "agent": {                       //默认是Minecraft
        "name": "Minecraft",         //为了登录MOJANG的另外一款游戏Scrolls，应该使用"Scrolls"
        "version": 1                 //数字可能会增长
				     //未来的普通客户端(?by the vanilla client in the future)
    },
    "username": "MOJANG账户名",      //可以是电子邮件或者玩家名
				     //未迁移的账户（使用玩家名）
    "password": "MOJANG账户的密码",
    "clientToken": "客户端标识符(client identifier)",//可选
    "requestUser": true              //可选，默认: false; 选择true将在响应中加入用户对象
}
```
clientToken应该是随机生成的标识符，并且对于每个请求都必须是相同的。Vanilla启动程序在第一次运行时生成一个随机的（版本4）的uuid并保存它，在随后的每个请求中都重复使用它。如果它被省略，服务器将基于Java的UUID.toString()生成一个随机令牌，然后由客户端存储。但是，这也会使该用户以前获得的所有客户端访问令牌失效。

## 响应

```
{
    "accessToken": "随机访问令牌（access token）",      // 十六进制或JSON-Web-Token (未确认) [正常的accessToken能够在payload of the JWT中找到(第二个由 '.'分割的Base64编码的 JSON object),在关键词 "yggt"中]
    "clientToken": "客户端标识符(client identifier)",        // 与接收到的相同
    "availableProfiles": [                     // 只有在收到agent字段时才出现
        {
            "agent": "minecraft",              // 大概和以前相同的值
            "id": "配置文件标识符",        // 十六进制
            "name": "玩家名",
            "userId": "十六进制字符",
            "createdAt": 数值,        // （账户创建时间）自1970年1月1日起毫秒 
            "legacyProfile": true 或者 false,    // 即使失败也存在
            "suspended": true 或者 false,        // 很可能是否
            "paid": true 或者 false,             // 很可能是是
            "migrated": true 或者 false,         // 即使是迁移账号也是否...?  (https://bugs.mojang.com/browse/WEB-1461)（作者不确定）
            "legacy": true or false            // 只有当为是时才会出现在应中。默认为false。对新的legacyProfile而言是多余的。。。 
        }
    ],
    "selectedProfile": {                       // 只有在收到agent字段时才出现
        "id": "无短线的uuid",
        "name": "游戏名",
        "userId": "十六进制字符",
        "createdAt": 1325376000000,
        "legacyProfile": true 或者 false,
        "suspended": true 或者 false,
        "paid": true 或者 false,
        "migrated": true 或者 false,
        "legacy": true 或者 false
    },
    "user": {                                  // 仅当请求负载中的requestUser为true时才存在
        "id": "user identifier",               // 十六进制
        "email": "user@email.example",         // 为迁移账户的 Hashed(?)（作者不确定） 值
        "username": "user@email.example",      // 未迁移帐户的常规名称，迁移帐户的电子邮件
        "registerIp": "198.51.100.*",          // 最后一次登录的IP地址
        "migratedFrom": "minecraft.net",
        "migratedAt": 1420070400000,
        "registeredAt": 1325376000000,         // 或许比配置文件创造的时间稍早一些
        "passwordChangedAt": 1569888000000,
        "dateOfBirth": -2208988800000,
        "suspended": false,
        "blocked": false,
        "secured": true,
        "migrated": false,                     // 甚至在migratedAt和migratedFrom存在时似乎也是否...
        "emailVerified": true,
        "legacyUser": false,
        "verifiedByParent": false,
        "properties": [
            {
                "name": "preferredLanguage",   // 或许不是所有账户都存在此项
                "value": "en"                  // Java locale 格式 (https://docs.oracle.com/javase/8/docs/api/java/util/Locale.html#toString--)
            },
            {
                "name": "twitch_access_token", // 只有连接了twitch账户时才存在 (see https://account.mojang.com/me/settings)
                "value": "twitch oauth token"  // OAuth 2.0 Token; alphanumerical; e.g. https://api.twitch.tv/kraken?oauth_token=[...]
                                               // Twitch API的文档: https://github.com/justintv/Twitch-API
            }
        ]
    }
}
```
未完待续
Note: 如果用户希望保持登录状态，强烈建议您保存accesToken而不是密码本身。

目前每个账户仅有一个配置文件，但是未来可能会有多个配置文件。如果用户试图登录到没有随附Minecraft许可证的有效Mojang帐户，则身份验证将成功，但响应将不包含selectedProfile字段，并且availableProfiles数组将为空。

Some instances in the wild have been observed of Mojang returning a flat null for failed refresh attempts against legacy accounts. It's not clear what the actual error tied to the null response is and it is extremely rare, but implementations should be wary of null output from the response.

This endpoint is severely rate-limited: multiple /authenticate requests for the same account in a short amount of time (think 3 requests in a few seconds), even with the correct password, will eventually lead to an Invalid credentials. response. This error clears up a few seconds later.
Refresh

Refreshes a valid accessToken. It can be used to keep a user logged in between gaming sessions and is preferred over storing the user's password in a file (see lastlogin).
Endpoint

/refresh

Payload

{
    "accessToken": "valid accessToken",
    "clientToken": "client identifier",  // This needs to be identical to the one used
                                         // to obtain the accessToken in the first place
    "selectedProfile": {                 // optional; sending it will result in an error
        "id": "profile identifier",      // hexadecimal
        "name": "player name"
    },
    "requestUser": true                  // optional; default: false; true adds the user object to the response
}

Note: The provided accessToken gets invalidated.
Response

{
    "accessToken": "random access token",      // hexadecimal
    "clientToken": "client identifier",        // identical to the one received
    "selectedProfile": {
        "id": "profile identifier",            // hexadecimal
        "name": "player name"
    },
    "user": {                                  // only present if requestUser was true in the request payload
        "id": "user identifier",               // hexadecimal
        "properties": [
            {
                "name": "preferredLanguage",   // might not be present for all accounts
                "value": "en"                  // Java locale format (https://docs.oracle.com/javase/8/docs/api/java/util/Locale.html#toString--)
            },
            {
                "name": "twitch_access_token", // only present if a twitch account is associated (see https://account.mojang.com/me/settings)
                "value": "twitch oauth token"  // OAuth 2.0 Token; alphanumerical; e.g. https://api.twitch.tv/kraken?oauth_token=[...]
                                               // the Twitch API is documented here: https://github.com/justintv/Twitch-API
            }
        ]
    }
}

Validate

Checks if an accessToken is usable for authentication with a Minecraft server. The Minecraft Launcher (as of version 1.6.13) calls this endpoint on startup to verify that its saved token is still usable, and calls /refresh if this returns an error.

Note that an accessToken may be unusable for authentication with a Minecraft server, but still be good enough for /refresh. This mainly happens when one has used another client (e.g. played Minecraft on another PC with the same account). It seems only the most recently obtained accessToken for a given account can reliably be used for authentication (the next-to-last token also seems to remain valid, but don't rely on it).

/validate may be called with or without a clientToken. If a clientToken is provided, it should match the one used to obtain the accessToken. The Minecraft Launcher does send a clientToken to /validate.
Endpoint

/validate

Payload

{
    "accessToken": "valid accessToken",
    "clientToken": "associated clientToken" // optional, see above
}

Response

Returns an empty payload (204 No Content) if successful, an error JSON with status 403 Forbidden otherwise.
Signout

Invalidates accessTokens using an account's username and password.
Endpoint

/signout

Payload

{
    "username": "mojang account name",
    "password": "mojang account password"
}

Response

Returns an empty payload if successful.
Invalidate

Invalidates accessTokens using a client/access token pair.
Endpoint

/invalidate

Payload

{
    "accessToken": "valid accessToken",
    "clientToken": "client identifier"   // This needs to be identical to the one used
                                         // to obtain the accessToken in the first place
}

Response

Returns an empty payload if successful.
Joining a Server

See Protocol Encryption#Authentication 
