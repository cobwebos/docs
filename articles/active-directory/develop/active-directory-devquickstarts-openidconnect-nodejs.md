---
title: "使用 Node.js 进行 Azure AD 登录和注销入门 | Microsoft Docs"
description: "了解如何构建用于登录的与 Azure AD 集成的 Node.js Express MVC Web 应用。"
services: active-directory
documentationcenter: nodejs
author: xerners
manager: mbaldwin
editor: 
ms.assetid: 81deecec-dbe2-4e75-8bc0-cf3788645f99
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/07/2017
ms.author: xerners
translationtype: Human Translation
ms.sourcegitcommit: 8c4e33a63f39d22c336efd9d77def098bd4fa0df
ms.openlocfilehash: 8d91d551cbb508485ca27d77b23eb319293e4237
ms.lasthandoff: 04/20/2017


---
# <a name="nodejs-web-app-sign-in-and-sign-out-with-azure-ad"></a>使用 Azure AD 进行 Node.js Web 应用登录和注销
此处，我们使用 Passport 进行以下操作：

* 通过 Azure Active Directory (Azure AD) 将用户登录到应用。
* 显示有关用户的信息。
* 从应用中注销用户。

Passport 是 Node.js 的身份验证中间件。 Passport 很灵活并且采用模块化结构，可以在不造成干扰的情况下放入任何基于 Express 的应用程序或 Resitify Web 应用程序。 一套综合性策略支持使用用户名和密码、Facebook、Twitter 等进行身份验证。 我们针对 Microsoft Azure Active Directory 开发了一个策略。 我们将安装此模块，然后添加 Microsoft Azure Active Directory `passport-azure-ad` 插件。

为此，请采取以下步骤：

1. 注册应用。
2. 将应用设置为使用 `passport-azure-ad` 策略。
3. 使用 Passport 向 Azure AD 发出登录和注销请求。
4. 打印有关用户的数据。

本教程的代码 [在 GitHub 上](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS)维护。  若要遵照该代码，可以[下载 .zip 文件格式的应用骨架](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip)，或克隆该骨架：

```git clone --branch skeleton https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

本教程末尾也提供完成的应用程序。

## <a name="step-1-register-an-app"></a>步骤 1：注册应用
1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 在页面顶部的菜单中选择你的帐户。 在“目录”列表下选择要注册应用程序的 Active Directory 租户。

3. 在屏幕左侧的菜单中，选择“更多服务”，然后选择“Azure Active Directory”。

4. 选择“应用注册”，然后选择“添加”。

5. 根据提示创建一个 **Web 应用程序**和/或 **WebAPI**。
  * 应用程序的“名称”向用户描述应用程序。

  * “登录 URL”是应用的基本 URL。  框架的默认值是 `http://localhost:3000/auth/openid/return`` 。

6. 注册后，Azure AD 会为应用分配唯一的应用程序 ID。 在后续部分中需用到此值，因此，请从应用程序页复制此值。
7. 从应用程序的“设置” -> “属性”页中，更新应用 ID URI。 “应用程序 ID URI”是应用程序的唯一标识符。 约定使用的格式是 `https://<tenant-domain>/<app-name>`，例如：`https://contoso.onmicrosoft.com/my-first-aad-app`。

## <a name="step-2-add-prerequisites-to-your-directory"></a>步骤 2：向目录添加先决条件
1. 在命令行中，将目录更改为根文件夹（如果尚未这样做），然后运行以下命令：

    * `npm install express`
    * `npm install ejs`
    * `npm install ejs-locals`
    * `npm install restify`
    * `npm install mongoose`
    * `npm install bunyan`
    * `npm install assert-plus`
    * `npm install passport`

2. 此外，还需要 `passport-azure-ad`:
    * `npm install passport-azure-ad`

这将安装 `passport-azure-ad` 依赖的库。

## <a name="step-3-set-up-your-app-to-use-the-passport-node-js-strategy"></a>步骤 3：将应用设置为使用 passport-node-js 策略
此处，我们将 Express 配置为使用 OpenID Connect 身份验证协议。  Passport 用于执行各种操作，包括发出登录和注销请求、管理用户的会话和获取有关用户的信息。

1. 首先，打开位于项目根目录中的 `config.js` 文件，然后在 `exports.creds` 部分输入应用的配置值。

  * `clientID` 是在注册门户中为应用分配的**应用程序 ID**。

  * `returnURL` 是在门户中输入的**重定向 URI**。

  * `clientSecret` 是在门户中生成的密码。

2. 接下来，打开项目根目录中的 `app.js` 文件。 然后添加以下调用，调用随 `passport-azure-ad` 附带的 `OIDCStrategy` 策略。

    ```JavaScript
    var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

    // add a logger

    var log = bunyan.createLogger({
    name: 'Microsoft OIDC Example Web Application'
    });
    ```

3. 然后，使用我们刚刚提到的策略来处理登录请求。

    ```JavaScript
    // Use the OIDCStrategy within Passport. (Section 2)
    //
    //   Strategies in passport require a `validate` function that accepts
    //   credentials (in this case, an OpenID identifier), and invokes a callback
    //   with a user object.
    passport.use(new OIDCStrategy({
        callbackURL: config.creds.returnURL,
        realm: config.creds.realm,
        clientID: config.creds.clientID,
        clientSecret: config.creds.clientSecret,
        oidcIssuer: config.creds.issuer,
        identityMetadata: config.creds.identityMetadata,
        skipUserProfile: config.creds.skipUserProfile,
        responseType: config.creds.responseType,
        responseMode: config.creds.responseMode
    },
    function(iss, sub, profile, accessToken, refreshToken, done) {
        if (!profile.email) {
        return done(new Error("No email found"), null);
        }
        // asynchronous verification, for effect...
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
Passport 使用适用于它的所有策略（Twitter、Facebook 等），所有策略写入器都依循类似的模式。 查看该策略，你会发现，我们已将它作为一个函数来传递，其中包含一个令牌和一个用作参数的 done。 策略完成其工作后将返回。 然后，我们需要存储用户并隐藏令牌，因此不需要再次请求它。

    > [!IMPORTANT]
    > The previous code takes any user that happens to authenticate to our server. This is known as auto-registration. We recommend that you don't let anyone authenticate to a production server without first having them register via a process that you decide on. This is usually the pattern you see in consumer apps, which allow you to register with Facebook but then ask you to provide additional information. If this weren't a sample application, we could have extracted the user's email address from the token object that is returned and then asked the user to fill out additional information. Because this is a test server, we add them to the in-memory database.


4. 接下来，让我们添加方法，以便能够根据 Passport 的要求，跟踪已登录的用户。 这些方法包括将用户信息序列化和反序列化。

    ```JavaScript

            // Passport session setup. (Section 2)

            //   To support persistent sign-in sessions, Passport needs to be able to
            //   serialize users into the session and deserialize them out of the session. Typically,
            //   this is done simply by storing the user ID when serializing and finding
            //   the user by ID when deserializing.
            passport.serializeUser(function(user, done) {
            done(null, user.email);
            });

            passport.deserializeUser(function(id, done) {
            findByEmail(id, function (err, user) {
                done(err, user);
            });
            });

            // array to hold signed-in users
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

5.  接下来，让我们添加可加载 Express 引擎的代码。 在此处，我们使用了 Express 提供的默认 /views 和 /routes 模式。

    ```JavaScript

        // configure Express (section 2)

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

6. 最后，让我们添加路由，以便将实际登录请求递交到 `passport-azure-ad` 引擎：

       
       ```JavaScript
 
        // Our Auth routes (section 3)

        // GET /auth/openid
        //   Use passport.authenticate() as route middleware to authenticate the
        //   request. The first step in OpenID authentication involves redirecting
        //   the user to their OpenID provider. After authenticating, the OpenID
        //   provider redirects the user back to this application at
        //   /auth/openid/return.
        app.get('/auth/openid',
        passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
        function(req, res) {
            log.info('Authentication was called in the Sample');
            res.redirect('/');
        });

            // GET /auth/openid/return
            //   Use passport.authenticate() as route middleware to authenticate the
            //   request. If authentication fails, the user is redirected back to the
            //   sign-in page. Otherwise, the primary route function is called,
            //   which, in this example, redirects the user to the home page.
            app.get('/auth/openid/return',
              passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
              function(req, res) {
                log.info('We received a return from AzureAD.');
                res.redirect('/');
              });

            // POST /auth/openid/return
            //   Use passport.authenticate() as route middleware to authenticate the
            //   request. If authentication fails, the user is redirected back to the
            //   sign-in page. Otherwise, the primary route function is called,
            //   which, in this example, redirects the user to the home page.
            app.post('/auth/openid/return',
              passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
              function(req, res) {
                log.info('We received a return from AzureAD.');
                res.redirect('/');
              });
       ```


## <a name="step-4-use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>步骤4：使用 Passport 向 Azure AD 发出登录和注销请求
现在，应用已正确配置为使用 OpenID Connect 身份验证协议与终结点通信。  `passport-azure-ad` 已处理有关创建身份验证消息、验证 Azure AD 提供的令牌以及保留用户会话的所有细节。 剩下的就是为用户提供登录和注销方式，以及收集有关已登录用户的其他信息。

1. 首先，让我们向 `app.js` 文件添加默认方法、登录方法、帐户方法和注销方法：

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
            log.info('Login was called in the Sample');
            res.redirect('/');
        });

        app.get('/logout', function(req, res){
          req.logout();
          res.redirect('/');
        });

    ```

2.  我们详细探讨一下：

  * `/` 路由将重定向到 index.ejs 视图，并在请求中传递用户（如果存在）。
  * `/account` 路由首先确保*我们已经过身份验证*（我们将在以下示例进行实现），然后在请求中传递用户，以便我们可以获取有关该用户的其他信息。
  * `/login` 路由将从 `passport-azuread` 调用 azuread-openidconnect 身份验证器。 如果不成功，它会将用户重定向回 /login。
  * `/logout` 路由只是调用 logout.ejs（和路由），以便清除 Cookie 并将用户返回到 index.ejs。

3. 对于 `app.js` 的最后一个部分，让我们添加前述 `/account` 中使用的 **EnsureAuthenticated** 方法。

    ```JavaScript

        // Simple route middleware to ensure user is authenticated. (section 4)

        //   Use this route middleware on any resource that needs to be protected. If
        //   the request is authenticated (typically via a persistent sign-in session),
        //   the request proceeds. Otherwise, the user is redirected to the
        //   sign-in page.
        function ensureAuthenticated(req, res, next) {
          if (req.isAuthenticated()) { return next(); }
          res.redirect('/login')
        }
    ```

4. 最后，在 `app.js` 中创建服务器本身：

```JavaScript

        app.listen(3000);

```


## <a name="step-5-to-display-our-user-in-the-website-create-the-views-and-routes-in-express"></a>步骤 5：若要在网站中显示用户，请在 Express 中创建视图与路由
现在，`app.js` 完成。 只需添加路由和视图即可，两者将向用户显示我们获取的信息，并处理我们创建的 `/logout` 和 `/login` 路由。

1. 在根目录下创建 `/routes/index.js` 路由。

    ```JavaScript
                /*
                 * GET home page.
                 */

                exports.index = function(req, res){
                  res.render('index', { title: 'Express' });
                };
    ```

2. 在根目录下创建 `/routes/user.js` 路由。

                ```JavaScript
                /*
                 * GET users listing.
                 */

                exports.list = function(req, res){
                  res.send("respond with a resource");
                };
                ```

 这些路由会将请求传递到我们的视图，包括用户（如果存在）。

3. 在根目录下创建 `/views/index.ejs` 视图。 这是一个简单的页面，它将调用我们的登录和注销方法，并允许我们捕获帐户信息。 请注意，我们可以使用条件性 `if (!user)`，因为在请求中传递的用户是我们拥有已登录用户的证明。

    ```JavaScript
    <% if (!user) { %>
        <h2>Welcome! Please log in.</h2>
        <a href="/login">Log In</a>
    <% } else { %>
        <h2>Hello, <%= user.displayName %>.</h2>
        <a href="/account">Account Info</a></br>
        <a href="/logout">Log Out</a>
    <% } %>
    ```

4. 在根目录下创建 `/views/account.ejs` 视图，以便能够查看 `passport-azuread` 放置在用户请求中的其他信息。

    ```Javascript
    <% if (!user) { %>
        <h2>Welcome! Please log in.</h2>
        <a href="/login">Log In</a>
    <% } else { %>
    <p>displayName: <%= user.displayName %></p>
    <p>givenName: <%= user.name.givenName %></p>
    <p>familyName: <%= user.name.familyName %></p>
    <p>UPN: <%= user._json.upn %></p>
    <p>Profile ID: <%= user.id %></p>
  ##Next steps  <p>Full Claimes</p>
    <%- JSON.stringify(user) %>
    <p></p>
    <a href="/logout">Log Out</a>
    <% } %>
    ```

5. 可以通过添加布局，使视图变得美观。 在根目录下创建“/views/layout.ejs”视图。

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
                <a href="/login">Log In</a>
                </p>
            <% } else { %>
                <p>
                <a href="/">Home</a> |
                <a href="/account">Account</a> |
                <a href="/logout">Log Out</a>
                </p>
            <% } %>
            <%- body %>
        </body>
    </html>
    ```

##<a name="next-steps"></a>后续步骤
最后，生成并运行应用。 运行 `node app.js`，然后转至 `http://localhost:3000`。

使用个人 Microsoft 帐户或者工作或学校帐户登录，随后你会看到该用户的标识在 /account 列表中的显示方式。 Web 应用现在使用行业标准的协议进行保护，可使用个人和工作/学校帐户来验证用户。

[以 .zip 文件提供](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/complete.zip)完整示例（不包括配置值）以供参考。 或者可从 GitHub 克隆它：

```git clone --branch complete https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

现在，可以转到更高级的主题。 可能需要：

[使用 Azure AD 保护 Web API](active-directory-devquickstarts-webapi-nodejs.md)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]

