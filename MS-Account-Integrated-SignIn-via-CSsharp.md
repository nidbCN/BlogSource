## 版权声明

> "[Microsoft account integrated sign in via C#](https://anduin.aiursoft.com/post/2020/3/18/microsoft-account-integrated-sign-in-via-c#)" by [Anduin Xue](https://anduin.aiursoft.com/), used under [CC AS 4.0](http://creativecommons.org/licenses/by-sa/4.0/) / ‎translate from original.
>  
> 本文翻译自 [Anduin Xue](https://anduin.aiursoft.com/) 的 "[Microsoft account integrated sign in via C#](https://anduin.aiursoft.com/post/2020/3/18/microsoft-account-integrated-sign-in-via-c#)" ，依据 [CC AS 4.0](https://creativecommons.org/licenses/by-sa/4.0/deed.zh) 协议授权。

---

下面的简单代码说明了如何构建一个支持使用 Microsoft 账户的 OAuth 身份认证。

在开始打代码之前，我们需要在你的 Azure portal 中先创建一个App。 [https://portal.azure.com](https://portal.azure.com)

这个名字是你的App显示的名字。选中让你的应用可以访问任何组织账号和个人账号的那项。至于重定向URI，它必须是你的服务器重定向地址，比如：

```
https://gateway.aiursoft.com/debug
```

创建完应用后，在这里复制你的 application id：

然后创建一个 secret。

别忘了复制那个 secret。