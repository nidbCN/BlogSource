## 使用微软 Azure AD 作为UWP+ASP.NET Core 应用程序的身份认证

> 说实话这玩意我研究了确实挺长时间，配置还算是挺复杂的...
> 参考： [Sign-in a user with the Microsoft Identity Platform in a WPF Desktop application and call an ASP.NET Core Web API - Code Samples | Microsoft Docs](https://docs.microsoft.com/zh-cn/samples/azure-samples/active-directory-dotnet-native-aspnetcore-v2/1-desktop-app-calls-web-api/)

### 配置Azure AD

在 [Azure AD默认目录](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) 左侧的应用注册中创建引用程序：
* SimpleTest.Client
* SimpleTest.Backend

分别用于客户端和服务端，创建的时候“受支持的帐户类型”选择“任何组织目录(任何 Azure AD 目录 - 多租户)中的帐户和个人 Microsoft 帐户(例如，Skype、Xbox)”，其它默认即可。

#### 配置Azure AD

##### 配置服务端应用

###### 公开API

点击左边的“公开API”，貌似会直接在上面生成一个API url，如果没有的话点一下最上面的条，生成一个。

点击“添加范围”按钮，添加一个范围。根据实际需求写名称即可，“谁能同意”选择“管理员和用户”。

##### 配置客户端应用

###### 身份验证

点击左边的“身份验证”，然后点击“添加平台”，选择“移动和桌面应用”，添加一个 "http://localhost" url。

###### API权限

点击左边的“API权限”，然后点击“添加权限”，选择“我的API”，点击刚刚添加的服务端的 "SimpleTest.Backend" ，勾选添加的权限 "access_as_user"

#### 配置项目

首先克隆微软提供的示例项目：

```bash
git clone https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2.git
```

##### 配置服务端

