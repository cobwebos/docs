### <a name="server-auth"></a>如何： 使用提供程序 （服务器流） 进行身份验证
若要让移动应用管理你的应用程序中的身份验证过程，必须使用标识提供程序注册您的应用程序。 然后在你的 Azure 应用程序服务，你需要配置的应用程序 ID 和机密提供商提供。
有关详细信息，请参阅教程[向您的应用程序添加身份验证](../articles/app-service-mobile/app-service-mobile-cordova-get-started-users.md)。

注册标识提供程序之后，调用`.login()`方法替换为你的提供商的名称。 例如，若要使用 Facebook 登录下面的代码：

```
client.login("facebook").done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```

提供程序有效的值为 aad、 facebook、 google、 microsoftaccount 和 twitter。

> [!NOTE]
> 目前无法通过服务器流处理 Google 身份验证。  若要使用 Google 进行身份验证，必须使用[客户端流方法](#client-auth)。

在这种情况下，Azure App Service 管理的 OAuth 2.0 身份验证流。  它显示所选提供程序的登录页，并在与标识提供程序的成功登录后生成的应用程序服务身份验证令牌。 Login 函数完成后，返回公开用户 ID 和应用程序服务的 JSON 对象身份验证令牌中的 userId 和 authenticationToken 字段中，分别。 此令牌可以缓存并重用直至其过期。

###<a name="client-auth"></a>如何： 使用提供程序 （客户端流式处理） 进行身份验证

你的应用程序可以还独立联系标识提供程序，然后为你的 App Service 中进行身份验证提供返回的令牌。 此客户端流，可以为用户提供单一登录体验或者从标识提供程序检索其他用户数据。

#### <a name="social-authentication-basic-example"></a>社交身份验证的基本示例

此示例使用 Facebook 客户端 SDK 进行身份验证：

```
client.login(
     "facebook",
     {"access_token": token})
.done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});

```
此示例假定由相应的提供程序 SDK 提供的令牌存储在令牌变量。

#### <a name="microsoft-account-example"></a>Microsoft 帐户示例

下面的示例使用 Live SDK，通过使用 Microsoft 帐户来支持单点登录 Windows 应用商店应用：

```
WL.login({ scope: "wl.basic"}).then(function (result) {
      client.login(
            "microsoftaccount",
            {"authenticationToken": result.session.authentication_token})
      .done(function(results){
            alert("You are now logged in as: " + results.userId);
      },
      function(error){
            alert("Error: " + err);
      });
});

```

此示例从 Live Connect，它通过调用 login 函数提供到您的应用程序服务获取一个令牌。

###<a name="auth-getinfo"></a>如何： 获取有关已通过身份验证的用户信息

可以从检索的身份验证信息`/.auth/me`与任何 AJAX 库调用的终结点使用 HTTP。  确保你设置`X-ZUMO-AUTH`到您的身份验证标记的标头。  身份验证令牌存储在`client.currentUser.mobileServiceAuthenticationToken`。  例如，若要使用提取 API:

```
var url = client.applicationUrl + '/.auth/me';
var headers = new Headers();
headers.append('X-ZUMO-AUTH', client.currentUser.mobileServiceAuthenticationToken);
fetch(url, { headers: headers })
    .then(function (data) {
        return data.json()
    }).then(function (user) {
        // The user object contains the claims for the authenticated user
    });
```

提取是可用作[npm 包](https://www.npmjs.com/package/whatwg-fetch)或从浏览器下载[CDNJS](https://cdnjs.com/libraries/fetch)。 你还可以使用 jQuery 或另一个 AJAX API 提取信息。  JSON 对象形式接收数据。
