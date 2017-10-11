---
title: "Node.js web 应用程序登录的 azure Active Directory v2.0 |Microsoft 文档"
description: "了解如何构建 Node.js web 应用，使用个人 Microsoft 帐户和工作或学校帐户登录用户进行签名。"
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
ms.openlocfilehash: 6d49c742f72440e22830915c90de009d9188db2a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="add-sign-in-to-a-nodejs-web-app"></a>将登录添加到 Node.js web 应用

> [!NOTE]
> 并非所有 Azure Active Directory 方案和功能都处理 v2.0 终结点。 若要确定是否应使用 v2.0 终结点或 v1.0 终结点，请阅读有关[v2.0 限制](active-directory-v2-limitations.md)。
> 

在本教程中，我们使用 Passport 来执行以下任务：

* 在 web 应用中，在登录用户通过使用 Azure Active Directory (Azure AD) 和 v2.0 终结点。
* 显示有关用户的信息。
* 用户从应用程序中注销。

**Passport**是 Node.js 身份验证中间件。 灵活且模块化 Passport 可以轻松放入任何基于 Express 的或 restify web 应用程序。 在 Passport，一组全面的策略通过使用用户名和密码、 Facebook、 Twitter 或其他选项来支持身份验证。 Azure ad，我们开发了一个策略。 在本文中，我们向你展示如何安装该模块，并将 Azure AD`passport-azure-ad`插件。

## <a name="download"></a>下载
对于本教程代码维护[GitHub 上](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs)。 若要遵循本教程，您可以[下载.zip 文件形式的应用程序的框架](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/skeleton.zip)或克隆该骨架：

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

你还可以获取完成本教程末尾的应用程序。

## <a name="1-register-an-app"></a>1： 注册某个应用程序
创建新的应用程序在[apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)，或按照[这些详细步骤](active-directory-v2-app-registration.md)注册应用程序。 请确保：

* 复制**应用程序 Id**分配给你的应用程序。 对于本教程中需要它。
* 添加**Web**平台，用于你的应用程序。
* 复制**重定向 URI**从门户。 你必须使用的默认 URI 值`urn:ietf:wg:oauth:2.0:oob`。

## <a name="2-add-prerequisities-to-your-directory"></a>2： 将 prerequisities 添加到你的目录
在命令提示符下，将目录更改转到根文件夹，如果你尚不存在。 运行以下命令：

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

此外，我们使用`passport-azure-ad`在的主干中的快速入门：

* `npm install passport-azure-ad`

这将安装这些库的`passport-azure-ad`使用。

## <a name="3-set-up-your-app-to-use-the-passport-node-js-strategy"></a>3： 将你的应用程序设置为使用 passport 节点 js 策略
将 Express 中间件设置为使用 OpenID Connect 身份验证协议。 使用 Passport 发出登录和注销请求、 管理用户的会话和获取有关用户，以及其他用途的信息。

1.  在项目的根目录，打开 Config.js 文件。 在`exports.creds`部分中，输入你的应用的配置值。
  
  * `clientID`:**应用程序 Id** ，分配给你在 Azure 门户中的应用。
  * `returnURL`:**重定向 URI**在门户中输入。
  * `clientSecret`: 在门户中生成的密钥。

2.  在项目根中，打开 App.js 文件。 若要调用 OIDCStrategy stratey，它具有`passport-azure-ad`，添加以下调用：

  ```JavaScript
  var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

  // Add some logging
  var log = bunyan.createLogger({
      name: 'Microsoft OIDC Example Web Application'
  });
  ```

3.  若要处理登录请求，使用刚刚提到的策略：

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

Passport 使用类似的模式为所有其策略 （Twitter 和 Facebook 等）。 所有策略写入器都遵循模式。 传递策略`function()`，它使用令牌和`done`作为参数。 完成了所有其任务后，将返回策略。 存储用户并隐藏令牌，因此无需再次请求它。

  > [!IMPORTANT]
  > 前面的代码将任何用户都可以进行身份验证转到你的服务器。 这称为自动注册。 在生产服务器上，你不想要让任何人都必须先经历你选择的注册过程。 这通常是在使用者应用中看到的模式。 应用程序可能会允许您向 Facebook 注册，但它，然后要求你输入其他信息。 如果您没有在本教程中使用命令行程序，则无法从返回的令牌对象中提取电子邮件。 然后，你可能会要求用户输入其他信息。 由于这是测试服务器，你可以直接对内存中的数据库添加用户。
  > 
  > 

4.  添加方法，用于跟踪的用户登录，根据 Passport 的要求。 这包括序列化和反序列化用户的信息：

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

5.  添加代码加载 Express 引擎。 使用的默认 /views 和 /routes 模式 Express 提供：

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

6.  添加发布将路由将实际登录请求发送到该移交`passport-azure-ad`引擎：

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

## <a name="4-use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>4： 使用 Passport 向 Azure AD 发出登录和注销请求
现在将您的应用程序设置为使用 OpenID Connect 身份验证协议与 v2.0 终结点通信。 `passport-azure-ad`策略将负责创建身份验证消息、 验证从 Azure AD 的令牌和维护用户会话的所有详细信息。 所有剩下所要做旨在为你的用户提供一种方法来登录和注销，并收集有关已登录的用户的详细信息。

1.  添加**默认**，**登录**，**帐户**，和**注销**到 App.js 文件的方法：

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
    
    * `/`路由将重定向到 index.ejs 视图。 （如果存在），它将在请求中传递用户。
    * `/account`首先路由*可确保你进行身份验证*（你实现，在下面的代码）。 然后，它将在请求中传递用户。 这是以便您可以获取有关用户的详细信息。
    * `/login`路由调用你`azuread-openidconnect`从身份验证器`passport-azuread`。 如果不成功，它将用户重定向回`/login`。
    * `/logout`路由调用 logout.ejs 视图 （和路由）。 这清除 cookie，并随后将返回给 index.ejs 用户。

2.  添加**EnsureAuthenticated**上文中使用的方法`/account`:

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

3.  在 App.js，创建服务器：

  ```JavaScript

  app.listen(3000);

  ```


## <a name="5-create-the-views-and-routes-in-express-that-you-show-your-user-on-the-website"></a>5： 在网站上显示你的用户的 Express 中创建的视图和路由
添加的路由和向用户显示信息的视图。 路由和视图还处理`/logout`和`/login`你创建的路由。

1. 在根目录下，创建`/routes/index.js`路由。

  ```JavaScript

  /*
  * GET home page.
  */

  exports.index = function(req, res){
    res.render('index', { title: 'Express' });
  };
  ```

2.  在根目录下，创建`/routes/user.js`路由。

  ```JavaScript

  /*
  * GET users listing.
  */

  exports.list = function(req, res){
    res.send("respond with a resource");
  };
  ```

  `/routes/index.js`和`/routes/user.js`请求传递到你的视图，包括用户，如果存在的简单路由。

3.  在根目录下，创建`/views/index.ejs`视图。 此页将调用你**登录**和**注销**方法。 你还使用`/views/index.ejs`视图以捕获帐户信息。 你可以使用条件性`if (!user)`作为在请求中传递的用户。 它是已登录用户的证据。

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

4.  在根目录下，创建`/views/account.ejs`视图。 `/views/account.ejs`视图允许你查看其他信息，`passport-azuread`将放置在用户请求。

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

5.  添加布局。 在根目录下，创建`/views/layout.ejs`视图。

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

6.  若要生成并运行你的应用程序，运行`node app.js`。 然后，请转到`http://localhost:3000`。

7.  使用个人 Microsoft 帐户或工作或学校帐户登录。 请注意，用户的标识反映 /account 列表中。 

你现在可以使用行业标准协议保护的 web 应用。 通过使用其个人和工作单位或学校帐户，可以验证用户身份应用程序中。

## <a name="next-steps"></a>后续步骤
对于引用，已完成的示例 （无需你的配置值） 提供作为[.zip 文件](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/complete.zip)。 你还可以将其克隆到从 GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

接下来，你可以移到更高级的主题。 你可能想要尝试：

[通过使用 v2.0 终结点保护 Node.js web API](active-directory-v2-devquickstarts-node-api.md)

下面是一些其他资源：

* [Azure AD v2.0 开发人员指南](active-directory-appmodel-v2-overview.md)
* [堆栈溢出"azure active directory"标记](http://stackoverflow.com/questions/tagged/azure-active-directory)

### <a name="get-security-updates-for-our-products"></a>获取我们的产品的安全更新
我们鼓励你注册以使用安全事件发生时通知。 上[Microsoft 技术的安全通知](https://technet.microsoft.com/security/dd252948)页上，安全通报警报订阅。

