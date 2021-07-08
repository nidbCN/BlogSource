title: C#实现使用微软账户集成登录
author: Gaein nidb
categories:
  - 软件教程
tags:
  - CSharp
  - 身份认证
  - dotNET
date: 2021-07-09 04:49:35
---

## 版权声明

> "[Microsoft account integrated sign in via C#](https://anduin.aiursoft.com/post/2020/3/18/microsoft-account-integrated-sign-in-via-c#)" by [Anduin Xue](https://anduin.aiursoft.com/), used under [CC AS 4.0](http://creativecommons.org/licenses/by-sa/4.0/), ‎translate from original.
>  
> 本文翻译自 [Anduin Xue](https://anduin.aiursoft.com/) 的 "[Microsoft account integrated sign in via C#](https://anduin.aiursoft.com/post/2020/3/18/microsoft-account-integrated-sign-in-via-c#)" ，依据 [CC AS 4.0](https://creativecommons.org/licenses/by-sa/4.0/deed.zh) 协议授权。

---

下面的简单代码说明了如何构建一个支持使用 Microsoft 账户的 OAuth 身份认证。

在开始打代码之前，我们需要在你的 Azure portal 中先创建一个App。 [https://portal.azure.com](https://portal.azure.com)

![azure portal](https://img.cdn.gaein.cn/website_used/blog/MS-Account-Integrated-SignIn-via-CSsharp/01.webp)

这个名字是你的App显示的名字。选中让你的应用可以访问任何组织账号和个人账号的那项。至于重定向URI，它必须是你的服务器重定向地址，比如：

```
https://gateway.aiursoft.com/debug
```

![创建应用](https://img.cdn.gaein.cn/website_used/blog/MS-Account-Integrated-SignIn-via-CSsharp/02.webp)

创建完应用后，在这里复制你的 application id：

![app id](https://img.cdn.gaein.cn/website_used/blog/MS-Account-Integrated-SignIn-via-CSsharp/03.webp)

然后创建一个 secret。

![secret](https://img.cdn.gaein.cn/website_used/blog/MS-Account-Integrated-SignIn-via-CSsharp/04.webp)

别忘了复制那个 secret。

运行下面说的代码需要一些像 `AiurUrl` 和 `Aiursoft.XelNaga.Services.HTTPService` 这样的类。运行下面这行命令来获取：

```bash
$ dotnet add package Aiursoft.XelNaga
```

为了让用户登录，我们需要重定向到微软的登录门户。复制以下的代码：

```csharp
public string GetBindRedirectLink()
{
    return new AiurUrl("https://login.microsoftonline.com", $"/{_tenant}/oauth2/v2.0/authorize", new MicrosoftAuthAddressModel
    {
        ClientId = _clientId,
        RedirectUri = "https://gateway.aiursoft.com/debug",
        ResponseType = "code",
        Scope = "user.read",
        State = ""
    }).ToString();
}
```

```csharp
public class MicrosoftAuthAddressModel
{
    [FromQuery(Name = "client_id")]
    public string ClientId { get; set; }
    [FromQuery(Name = "redirect_uri")]
    public string RedirectUri { get; set; }
    [FromQuery(Name = "state")]
    public string State { get; set; }
    [FromQuery(Name = "scope")]
    public string Scope { get; set; }
    [FromQuery(Name = "response_type")]
    public string ResponseType { get; set; }
}
```

只需要返回给浏览器一个`redirect result` 和从 `GetRedirectLink()` 函数获取到的重定向链接。

用户成功的登录了他的账户的时候，将会带着一个码重定向回到你设置的 `RedirectUri`。这个码正式我们需要的。

通过这个码，你可以调用 `GetUserDetail()` 方法来获取用户的信息。

```cs
public async Task<IUserDetail> GetUserDetail(string code)
{
    var token = await GetAccessToken(_clientId, _clientSecret, code);
    return await GetUserInfo(token);
}
```

`_clientId` 和 `_clientSecret` 是你创建这个 app 时候复制的。

```cs
private async Task<string> GetAccessToken(string clientId, string clientSecret, string code)
{
    var apiAddress = "https://login.microsoftonline.com" + $"/{_tenant}/oauth2/v2.0/token";
    var url = new AiurUrl(apiAddress, new { });
    var form = new AiurUrl(string.Empty, new MicrosoftAccessTokenAddressModel
    {
        ClientId = clientId,
        ClientSecret = clientSecret,
        Code = code,
        Scope = "user.read",
        RedirectUri = "https://gateway.aiursoft.com/debug",
        GrantType = "authorization_code"
    });
    try
    {
        var json = await _http.Post(url, form, false);
        var response = JsonConvert.DeserializeObject<AccessTokenResponse>(json);
        if (string.IsNullOrWhiteSpace(response.AccessToken))
        {
            throw new AiurAPIModelException(ErrorType.Unauthorized, "Invalid Microsoft crenditial");
        }
        return response.AccessToken;
    }
    catch (WebException)
    {
        throw new AiurAPIModelException(ErrorType.Unauthorized, "Invalid Microsoft API response");
    }
}
```

```cs
public class MicrosoftAccessTokenAddressModel
{
    [FromQuery(Name = "client_id")]
    public string ClientId { get; set; }
    [FromQuery(Name = "client_secret")]
    public string ClientSecret { get; set; }
    [FromQuery(Name = "code")]
    public string Code { get; set; }
    [FromQuery(Name = "scope")]
    public string Scope { get; set; }
    [FromQuery(Name = "grant_type")]
    public string GrantType { get; set; }
    [FromQuery(Name = "redirect_uri")]
    public string RedirectUri { get; set; }
}
```

`GetAccessToken()` 方法能够帮助你获取一个有效的能够让你下载到用户信息的 access token。

你拿到了 access token 之后，只需要调用 `GetUserInfo()` 即可。下面我们将调用 graph API 来获取当前用户的信息。

```cs
private async Task<MicrosoftUserDetail> GetUserInfo(string accessToken)
{
    var apiAddress = "https://graph.microsoft.com/v1.0/me";
    var request = new HttpRequestMessage(HttpMethod.Get, apiAddress);

    request.Headers.Add("Authorization", $"Bearer {accessToken}");
    var response = await _client.SendAsync(request);
    if (response.IsSuccessStatusCode)
    {
        var json = await response.Content.ReadAsStringAsync();
        var user = JsonConvert.DeserializeObject<MicrosoftUserDetail>(json);
        return user;
    }
    else
    {
        throw new WebException(response.ReasonPhrase);
    }
}
```

返回的用户是一个 `MicrosoftUserDetail` 的实例。

```cs
public class MicrosoftUserDetail : IUserDetail
{
    [JsonProperty("id")]
    public string Id { get; set; }
    [JsonProperty("displayName")]
    public string DisplayName{ get; set; }
    [JsonProperty("userPrincipalName")]
    public string UserPrincipalName { get; set; }
    [JsonProperty("jobTitle")]
    public string JobTitle{ get; set; }
}
```

Id 是唯一的，可以使用这个来认证你的用户。

最后，构建一个个性化的网页，然后你的app就行力。

![网页](https://img.cdn.gaein.cn/website_used/blog/MS-Account-Integrated-SignIn-via-CSsharp/05.webp)

源代码在这：

[https://github.com/AiursoftWeb/Nexus/blob/master/Pylon/Services/Authentication/ToMicrosoftServer/MicrosoftService.cs](https://github.com/AiursoftWeb/Nexus/blob/master/Pylon/Services/Authentication/ToMicrosoftServer/MicrosoftService.cs)

This work is licensed under a [Creative Commons Attribution-ShareAlike 4.0 International License](http://creativecommons.org/licenses/by-sa/4.0/)