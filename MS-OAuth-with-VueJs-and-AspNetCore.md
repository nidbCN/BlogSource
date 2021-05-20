> 发现教程，咕咕咕：https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/

# 使用微软的OAuth保护Vue + ASP.NET Core Web应用。

> 目标：用户使用微软的OAuth授权登录后，可以访问被保护的API（返回 HTTP 200 而不是 HTTP 401）。

## 后端: ASP.NET Core

### 创建项目

使用 Visual Studio 或者 .NET CLI 新建 ASP.NET Core Web API 项目。

### 配置项目

点击 "Project" > "Manage NuGet Packages"，安装NuGet包 `Microsoft.Identity.Web`

同意许可协议，安装包。

打开 `StarUp.cs` 在 `ConfigureServices` 方法中加入：

```cs
            services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
                .AddMicrosoftIdentityWebApi(Configuration.GetSection("AzureAd"));
```

在 `Configure` 方法中的 `UseEndpoints` 前加入中间件身份认证和授权，注意顺序和位置：

```cs
            app.UseAuthentication();    // 认证
            app.UseAuthorization();     // 授权
```

删掉项目模板中的 `WeatherForecast` 类和控制器，创建新控制器，比如 `CompaniesController` ：

```cs
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;
using System;
using Microsoft.AspNetCore.Authorization;

namespace MicrosoftOAuthSimple.Backend.Controllers
{
    [ApiController]
    [Route("[controller]")]
    public class CompaniesController : ControllerBase
    {
        private readonly ILogger<CompaniesController> _logger;

        public CompaniesController(ILogger<CompaniesController> logger)
        {
            // 依赖注入
            _logger = logger ??
                      throw new ArgumentNullException(nameof(logger));
        }

        [Authorize] // 受到保护的API
        [HttpGet]   // HTTP 方法
        public IActionResult Get() =>
            Ok(new[]
            {
                new CompanyDto()
                {
                    Name = "Microsoft",
                    WebSite = new Uri("https://www.microsoft.com"),
                    Description = "巨硬",
                },
                new CompanyDto()
                {
                    Name = "ALiBaBa",
                    WebSite = new Uri("https://www.alibabagroup.com/"),
                    Description = "福报公司",   // 全世界无产者，联合起来！
                }
            });
    }

    // 注意，这个Dto类实际上不应该在 Controller类文件 里面定义，此处是为了方便，应该在单独的Model或者Dtos文件夹里定义对应的类。
    public class CompanyDto
    {
        public string Name { get; set; }
        public Uri WebSite { get; set; }
        public string Description { get; set; }
    }
}
```

## Azure AD

### 创建应用

在Azure AD中的 “默认目录” > “管理” > “引用注册” 中点击 “新注册”，注册一个新应用程序：
* 名称：MicrosoftOAuthSimple （随便写）

其它默认，点击注册即可。

### 配置本地项目

前面我们已经完成了大部分操作，现在要做的是在 `appsettings.json` 里面添加 `Azure AD` 的信息，在根节点下加入：

```json
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "qualified.domain.name",
    "ClientId": "xxxxxxxx-xxxx-xxxx-xxxx-bd17a760d97a",
    "TenantId": "xxxxxxxx-xxxx-xxxx-xxxx-898e4d31964b"
  },
```
"ClientId" 与 "TenantId" 填写Azure上注册的应用中“概述”页的ClientId和租户ID即可，如果是任何账号都可以的话那么填写 `common`。

### 配置前端应用

点击左侧的“身份验证”，“添加平台”，并选择“单页应用程序”
* 重定向的URL暂时填写 `http://localhost:8080`

然后点击“配置”按钮

### 配置公开API

在公开API处，添加一个作用域，配置中随意填写即可。

> 注意：范围名称需要是英文。

## 前端: VueJs + MSAL.js 2.0

### 创建项目

使用 vue cli 创建项目：

```bash
vue create frontend
```

创建过程中的所有选项，均使用默认配置即可。

进入 `frontend` 目录后，打开 `package.json` 文件，更改依赖：

```cs
  "dependencies": {
    "@azure/core-http": "1.1.8",
    "@azure/msal-browser": "^2.3.1",
    "core-js": "^3.6.5",
    "tiny-emitter": "^2.1.0",
    "vue": "^3.0.2",
    "vuex": "^4.0.0-beta.4"
  },
  "devDependencies": {
    "@vue/cli-plugin-babel": "~4.5.0",
    "@vue/cli-plugin-eslint": "~4.5.0",
    "@vue/cli-service": "~4.5.0",
    "@vue/compiler-sfc": "^3.0.0",
    "babel-eslint": "^10.1.0",
    "eslint": "^6.7.2",
    "eslint-plugin-vue": "^7.0.0-0",
    "node-sass": "^4.9.0",
    "sass-loader": "^10.0.3"
  },
```

> 注意：如果版本有所冲突，尽量使用新版就行了（

运行：

```bash
npm install
```

更新依赖库

打开 `src/main.js` 文件，更改为：

```js
import { createApp } from 'vue';
import App from './App.vue';
import Emitter from 'tiny-emitter';
import store from './store';

const app = createApp(App);
app.config.globalProperties.$msalInstance = {};
app.config.globalProperties.$emitter = new Emitter();

app.use(store).mount('#app');
```

新建一个名为“store” 的目录，并在目录中创建 `index.js` ，写入：

```js
import { createStore } from 'vuex';

const store = createStore({
  state() {
    return {
      msalConfig: {
        auth: {
          clientId: 'xxxxxxxx-xxxx-xxxx-xxxx-b036dd6927fb',     // clientId
          authority:
            'https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-898e4d31964b',  // tenantId
        },
        cache: {
          cacheLocation: 'localStorage',
        },
      },
      accessToken: ''
    };
  },
  mutations :{
    setAccessToken(state, token){
      state.accessToken = token;
    }
  }
});

export default store;
```

> 注意：如果允许账户是所有账号的话authority要写 `common`。


https://devblogs.microsoft.com/azure-sdk/vue-js-user-authentication/
https://docs.microsoft.com/zh-cn/azure/active-directory/develop/quickstart-v2-aspnet-core-web-api