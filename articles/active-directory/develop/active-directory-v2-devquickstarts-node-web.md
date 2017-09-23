---
title: "Azure Active Directory v2.0 Node.js Web 应用登录 | Microsoft Docs"
description: "了解如何生成使用 Microsoft 个人帐户和工作/学校帐户来让用户登录的 Node.js Web 应用。"
services: active-directory
documentationcenter: nodejs
author: navyasric
manager: mbaldwin
editor: 
ms.assetid: 1b889e72-f5c3-464a-af57-79abf5e2e147
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 05/13/2017
ms.author: nacanuma
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: f7479260c7c2e10f242b6d8e77170d4abe8634ac
ms.openlocfilehash: 6d49c742f72440e22830915c90de009d9188db2a
ms.contentlocale: zh-cn
ms.lasthandoff: 07/06/2017

---
# <a name="add-sign-in-to-a-nodejs-web-app"></a>向 Node.js Web 应用添加登录

> [!NOTE]
> 并非所有 Azure Active Directory 方案和功能都可与 v2.0 终结点配合使用。 若要确定是应该使用 v2.0 终结点还是应该使用 v1.0 终结点，请阅读 [v2.0 限制](active-directory-v2-limitations.md)。
> 

在本教程中，我们使用 Passport 执行以下任务：

* 在 Web 应用中，使用 Azure Active Directory (Azure AD) 和 v2.0 终结点让用户登录。
* 显示有关用户的信息。
* 从应用中注销用户。

**Passport** 是 Node.js 的身份验证中间件。 Passport 很灵活并且采用模块化结构，可以在不造成干扰的情况下放入任何基于 Express 的应用程序或 Resitify Web 应用程序。 在 Passport 中，有一套综合性策略支持使用用户名和密码、Facebook、Twitter 或其他选项进行身份验证。 我们针对 Azure AD 开发了一项策略。 本文介绍如何安装该模块及添加 Azure AD `passport-azure-ad` 插件。

## <a name="download"></a>下载
本教程的代码 [在 GitHub 上](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs)维护。 若要按照本教程操作，可以[下载 .zip 文件格式的应用框架](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/skeleton.zip)，或克隆该框架：

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

还可以在本教程结束时获得完整的应用程序。

## <a name="1-register-an-app"></a>1：注册应用
在 [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) 上创建新应用，或按照[以下详细步骤](active-directory-v2-app-registration.md)注册应用。 确保：

* 复制分配给应用的**应用程序 ID**。 在本教程中需要用到它。
* 为应用添加 **Web** 平台。
* 从门户复制**重定向 URI**。 必须使用默认的 URI 值 `urn:ietf:wg:oauth:2.0:oob`。

## <a name="2-add-prerequisities-to-your-directory"></a>2. 向目录添加先决条件
在命令提示符下更改目录，转到根文件夹（如果尚未位于此处）。 运行以下命令：

* `npm install express`
* `npm install ejs`
* `npm install ejs-locals`
* `npm install restify`
* `npm install mongoose`
* `npm install bunyan`
* `npm install assert-plus`
* `npm install passport`
* `npm install webfinger`
* `npm install body-parser`
* `npm install express-session`
* `npm install cookie-parser`

此外，我们在快速入门的框架中使用 `passport-azure-ad`：

* `npm install passport-azure-ad`

这将安装 `passport-azure-ad` 使用的库。

## <a name="3-set-up-your-app-to-use-the-passport-node-js-strategy"></a>3. 将应用设置为使用 passport-node-js 策略
将 Express 中间件设置为使用 OpenID Connect 身份验证协议。 使用 Passport 发出登录和注销请求、管理用户的会话、获取关于用户的信息，等等。

1.  在项目的根目录中，打开 Config.js 文件。 在 `exports.creds` 部分，输入应用的配置值。
  
  * `clientID`：在 Azure 门户中分配给应用的**应用程序 ID**。
  * `returnURL`：在门户中输入的**重定向 URI**。
  * `clientSecret`：在门户中生成的密码。

2.  在项目的根目录中，打开 App.js 文件。 若要调用 `passport-azure-ad` 随附的 OIDCStrategy 策略，可添加以下调用：

  ```JavaScript
  var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

  // Add some logging
  var log = bunyan.createLogger({
      name: 'Microsoft OIDC Example Web Application'
  });
  ```

3.  若要处理登录请求，可使用刚才引用的策略：

  ```JavaScript
  // Use the OIDCStrategy within Passport (section 2)
  //
  //   Strategies in Passport require a `validate` function. The function accepts
  //   credentials (in this case, an OpenID identifier), and invokes a callback
  //   with a user object.
  passport.use( new OIDCStrategy({
      callbackURL: config.creds.returnURL,
      realm: config.creds.realm,
      clientID: config.creds.clientID,
      clientSecret: config.creds.clientSecret,
      oidcIssuer: config.creds.issuer,
      identityMetadata: config.creds.identityMetadata,
      responseType: config.creds.responseType,
      responseMode: config.creds.responseMode,
      skipUserProfile: config.creds.skipUserProfile
      scope: config.creds.scope
    },
    function(iss, sub, profile, accessToken, refreshToken, done) {
      log.info('Example: Email address we received was: ', profile.email);
      // Asynchronous verification, for effect...
      process.nextTick(function () {
        findByEmail(profile.email, function(err, user) {
          if (err) {
            return done(err);
          }
          if (!user) {
            // "Auto-registration"
            users.push(profile);
            return done(null, profile);
          }
          return done(null, user);
        });
      });
    }
  ));
  ```

Passport 对其所有策略（Twitter、Facebook 等）都使用类似的模式。 所有策略编写器都遵循该模式。 向策略传递使用令牌和 `done` 作为参数的 `function()`。 策略会在完成所有任务后返回。 存储用户和令牌，这样一来，就不需要再次请求。

  > [!IMPORTANT]
  > 前面的代码会将所有可以进行身份验证的用户转到服务器。 这就是所谓的自动注册。 在生产服务器上，你会希望所有人都先经历你选择的注册过程，然后才能进入服务器。 通常会在消费类应用中看到这种模式。 应用可能会允许使用 Facebook 注册，但随后会要求输入其他信息。 如果在本教程中没有使用命令行程序，可从返回的令牌对象中提取电子邮件。 然后，可以要求用户输入其他信息。 由于这是测试服务器，可以直接将用户添加到内存中数据库。
  > 
  > 

4.  根据 Passport 的要求，添加用于跟踪已登录用户的方法。 这包括将用户信息序列化和反序列化：

  ```JavaScript

  // Passport session setup (section 2)

  //   To support persistent login sessions, Passport needs to be able to
  //   serialize users into, and deserialize users out of, the session. Typically,
  //   this is as simple as storing the user ID when serializing, and finding
  //   the user by ID when deserializing.
  passport.serializeUser(function(user, done) {
    done(null, user.email);
  });

  passport.deserializeUser(function(id, done) {
    findByEmail(id, function (err, user) {
      done(err, user);
    });
  });

  // Array to hold signed-in users
  var users = [];

  var findByEmail = function(email, fn) {
    for (var i = 0, len = users.length; i < len; i++) {
      var user = users[i];
      log.info('we are using user: ', user);
      if (user.email === email) {
        return fn(null, user);
      }
    }
    return fn(null, null);
  };
  ```

5.  添加加载 Express 引擎的代码。 使用 Express 提供的默认 /views 和 /routes 模式：

  ```JavaScript

  // Set up Express (section 2)

  var app = express();

  app.configure(function() {
    app.set('views', __dirname + '/views');
    app.set('view engine', 'ejs');
    app.use(express.logger());
    app.use(express.methodOverride());
    app.use(cookieParser());
    app.use(expressSession({ secret: 'keyboard cat', resave: true, saveUninitialized: false }));
    app.use(bodyParser.urlencoded({ extended : true }));
    // Initialize Passport!  Also use passport.session() middleware, to support
    // persistent login sessions (recommended).
    app.use(passport.initialize());
    app.use(passport.session());
    app.use(app.router);
    app.use(express.static(__dirname + '/../../public'));
  });

  ```

6.  添加 POST 路由，以便将实际登录请求递交到 `passport-azure-ad` 引擎：

  ```JavaScript

  // Auth routes (section 3)

  // GET /auth/openid
  //   Use passport.authenticate() as route middleware to authenticate the
  //   request. The first step in OpenID authentication involves redirecting
  //   the user to the user's OpenID provider. After authenticating, the OpenID
  //   provider redirects the user back to this application at
  //   /auth/openid/return.

  app.get('/auth/openid',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {
      log.info('Authentication was called in the sample');
      res.redirect('/');
    });

  // GET /auth/openid/return
  //   Use passport.authenticate() as route middleware to authenticate the
  //   request. If authentication fails, the user is redirected back to the
  //   sign-in page. Otherwise, the primary route function is called.
  //   In this example, it redirects the user to the home page.
  app.get('/auth/openid/return',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {

      res.redirect('/');
    });

  // POST /auth/openid/return
  //   Use passport.authenticate() as route middleware to authenticate the
  //   request. If authentication fails, the user is redirected back to the
  //   sign-in page. Otherwise, the primary route function is called. 
  //   In this example, it redirects the user to the home page.

  app.post('/auth/openid/return',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {

      res.redirect('/');
    });
  ```

## <a name="4-use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>4. 使用 Passport 向 Azure AD 发出登录和注销请求
现在，应用已设置为使用 OpenID Connect 身份验证协议与 v2.0 终结点通信。 `passport-azure-ad` 策略负责处理有关创建身份验证消息、验证 Azure AD 提供的令牌以及保留用户会话的所有细节。 剩下需要做的就是为用户提供登录和注销方式，以及收集有关已登录用户的详细信息。

1.  在 App.js 文件中添加 **default**、**login**、**account** 和 **logout** 方法：

  ```JavaScript

  //Routes (section 4)

  app.get('/', function(req, res){
    res.render('index', { user: req.user });
  });

  app.get('/account', ensureAuthenticated, function(req, res){
    res.render('account', { user: req.user });
  });

  app.get('/login',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {
      log.info('Login was called in the sample');
      res.redirect('/');
  });

  app.get('/logout', function(req, res){
    req.logout();
    res.redirect('/');
  });

  ```

  以下是详细信息：
    
    * `/` 路由重定向到 index.ejs 视图。 它在请求中传递用户（如果存在）。
    * `/account` 路由首先*确保用户进行身份验证*（在下面的代码中实现）。 然后，它在请求中传递用户。 这样一来，可以获取有关用户的详细信息。
    * `/login` 路由从 `passport-azuread` 调用 `azuread-openidconnect` 验证器。 如果不成功，它会将用户重定向回 `/login`。
    * `/logout` 路由调用 logout.ejs 视图（和路由）。 此操作会清除 Cookie，然后将用户返回到 index.ejs。

2.  添加之前在 `/account` 中使用的 **EnsureAuthenticated** 方法：

  ```JavaScript

  // Route middleware to ensure the user is authenticated (section 4)

  //   Use this route middleware on any resource that needs to be protected. If
  //   the request is authenticated (typically via a persistent login session),
  //   the request proceeds. Otherwise, the user is redirected to the
  //   sign-in page.
  function ensureAuthenticated(req, res, next) {
    if (req.isAuthenticated()) { return next(); }
    res.redirect('/login')
  }

  ```

3.  在 App.js 中，创建服务器：

  ```JavaScript

  app.listen(3000);

  ```


## <a name="5-create-the-views-and-routes-in-express-that-you-show-your-user-on-the-website"></a>5. 在 Express 中创建在网站上向用户显示的视图和路由
添加向用户显示信息的路由和视图。 路由和视图还会处理创建的 `/logout` 和 `/login` 路由。

1. 在根目录下创建 `/routes/index.js` 路由。

  ```JavaScript

  /*
  * GET home page.
  */

  exports.index = function(req, res){
    res.render('index', { title: 'Express' });
  };
  ```

2.  在根目录下创建 `/routes/user.js` 路由。

  ```JavaScript

  /*
  * GET users listing.
  */

  exports.list = function(req, res){
    res.send("respond with a resource");
  };
  ```

  `/routes/index.js` 和 `/routes/user.js` 是简单路由，可将请求传递到视图（包括用户，如果存在）。

3.  在根目录下创建 `/views/index.ejs` 视图。 此页将调用 **login** 和 **logout** 方法。 还可使用 `/views/index.ejs` 视图捕获帐户信息。 可使用条件性 `if (!user)` 作为要在请求中传递的用户。 它是已让用户登录的证据。

  ```JavaScript
  <% if (!user) { %>
      <h2>Welcome! Please sign in.</h2>
      <a href="/login">Sign in</a>
  <% } else { %>
      <h2>Hello, <%= user.displayName %>.</h2>
      <a href="/account">Account info</a></br>
      <a href="/logout">Sign out</a>
  <% } %>
  ```

4.  在根目录下创建 `/views/account.ejs` 视图。 `/views/account.ejs` 视图允许查看 `passport-azuread` 放在用户请求中的其他信息。

  ```Javascript
  <% if (!user) { %>
      <h2>Welcome! Please sign in.</h2>
      <a href="/login">Sign in</a>
  <% } else { %>
  <p>displayName: <%= user.displayName %></p>
  <p>givenName: <%= user.name.givenName %></p>
  <p>familyName: <%= user.name.familyName %></p>
  <p>UPN: <%= user._json.upn %></p>
  <p>Profile ID: <%= user.id %></p>
  <p>Full Claimes</p>
  <%- JSON.stringify(user) %>
  <p></p>
  <a href="/logout">Sign out</a>
  <% } %>
  ```

5.  添加布局。 在根目录下创建 `/views/layout.ejs` 视图。

  ```HTML

  <!DOCTYPE html>
  <html>
      <head>
          <title>Passport-OpenID Example</title>
      </head>
      <body>
          <% if (!user) { %>
              <p>
              <a href="/">Home</a> |
              <a href="/login">Sign in</a>
              </p>
          <% } else { %>
              <p>
              <a href="/">Home</a> |
              <a href="/account">Account</a> |
              <a href="/logout">Sign out</a>
              </p>
          <% } %>
          <%- body %>
      </body>
  </html>
  ```

6.  若要生成并运行应用，请运行 `node app.js`。 然后，转到 `http://localhost:3000`。

7.  使用 Microsoft 个人帐户或工作/学校帐户登录。 请注意，用户的标识在 /account 列表中反映。 

现在拥有使用行业标准协议进行保护的 Web 应用。 可使用用户的个人和工作/学校帐户，在应用中验证用户的身份。

## <a name="next-steps"></a>后续步骤
某个 [.zip 文件](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/complete.zip)提供了完整的示例（不包括配置值）供参考。 还可以从 GitHub 中进行克隆：

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

接下来，可以转到更高级的主题。 可能需要：

[使用 v2.0 终结点保护 Node.js Web API](active-directory-v2-devquickstarts-node-api.md)

下面是一些其他资源：

* [Azure AD v2.0 开发人员指南](active-directory-appmodel-v2-overview.md)
* [Stack Overflow“azure-active-directory”标记](http://stackoverflow.com/questions/tagged/azure-active-directory)

### <a name="get-security-updates-for-our-products"></a>获取关于我们产品的安全更新
我们建议注册，以便在发生安全事件时获得通知。 在 [Microsoft 技术安全通知](https://technet.microsoft.com/security/dd252948)页上订阅安全顾问警报。


