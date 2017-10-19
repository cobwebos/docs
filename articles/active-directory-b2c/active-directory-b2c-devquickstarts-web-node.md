---
title: "将登录添加到 Azure B2C 的 Node.js Web 应用 | Microsoft Docs"
description: "如何生成一个使用 B2C 租户登录用户的 Node.js Web 应用。"
services: active-directory-b2c
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: db97f84a-1f24-447b-b6d2-0265c6896b27
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: hero-article
ms.date: 03/10/2017
ms.author: xerners
ms.openlocfilehash: c85b8f8434d1e837ac96ac63b9b37f990677ed6e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-b2c-add-sign-in-to-a-nodejs-web-app"></a>Azure AD B2C：将登录添加到 Node.js Web 应用

**Passport** 是 Node.js 的身份验证中间件。 Passport 极其灵活且采用模块化结构，可以在不造成干扰的情况下安装在任何基于 Express 的应用程序或 Resitify Web 应用程序中。 一套综合性策略支持使用用户名和密码、Facebook、Twitter 等进行身份验证。

我们针对 Azure Active Directory (Azure AD) 开发了一个策略。 需要安装此模块，然后添加 Azure AD `passport-azure-ad` 插件。

若要实现此目的，需要：

1. 使用 Azure AD 注册应用程序。
2. 将应用设置为使用 `passport-azure-ad` 插件。
3. 使用 Passport 向 Azure AD 发出登录和注销请求。
4. 输出用户数据。

本教程的代码 [保留在 GitHub 上](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS)。 若要遵循本文的步骤进行操作，可以[下载 .zip 文件格式的应用骨架](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip)， 也可以克隆骨干项目：

```git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS.git```

本教程末尾提供完成的应用程序。

## <a name="get-an-azure-ad-b2c-directory"></a>获取 Azure AD B2C 目录

只有在创建目录或租户之后，才可使用 Azure AD B2C。  目录是所有用户、应用、组等对象的容器。 如果没有容器，请先 [创建 B2C 目录](active-directory-b2c-get-started.md) ，然后继续执行本指南中的步骤。

## <a name="create-an-application"></a>创建应用程序

接下来，需要在 B2C 目录中创建应用。 此应用为 Azure AD 提供所需的 Azure AD 信息，使之能够与应用安全通信。 客户端应用与 Web API 构成一个逻辑应用，因此由单个**应用程序 ID** 表示。 若要创建应用，请遵循 [这些说明](active-directory-b2c-app-registration.md)。 请务必：

- 在应用程序中包含一个 **Web 应用**/**Web API**
- 在“回复 URL”中输入 `http://localhost:3000/auth/openid/return`。 它是此代码示例的默认 URL。
- 为应用程序创建 **应用程序机密** 并复制下来。 稍后需要用到此值。 请注意，此值在使用之前必须经过 [XML 转义](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) 。
- 复制分配给应用的 **应用程序 ID** 。 稍后也需要用到此信息。

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>创建策略

在 Azure AD B2C 中，每个用户体验由 [策略](active-directory-b2c-reference-policies.md)定义。 此应用包含三个标识体验：注册、登录和使用 Facebook 登录。 需要按照 [策略参考文章](active-directory-b2c-reference-policies.md#create-a-sign-up-policy)中所述，为每个类型创建此策略。 创建三个策略时，请务必：

- 在注册策略中，选择“显示名称”和其他注册属性。
- 在每个策略中，选择“显示名称”和“对象 ID”应用程序声明。 也可以选择其他声明。
- 创建每个策略后，请复制策略的 **名称** 。 其前缀应为 `b2c_1_`。  稍后需要用到这些策略名称。

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

创建三个策略后，可以开始构建应用。

请注意，本文不介绍如何使用刚刚创建的策略。 若要了解 Azure AD B2C 中策略的工作原理，请先阅读 [.NET Web 应用入门教程](active-directory-b2c-devquickstarts-web-dotnet.md)。

## <a name="add-prerequisites-to-your-directory"></a>向目录添加先决条件

在命令行中，将目录切换到根文件夹（如果尚未进入）。 运行以下命令：

- `npm install express`
- `npm install ejs`
- `npm install ejs-locals`
- `npm install restify`
- `npm install mongoose`
- `npm install bunyan`
- `npm install assert-plus`
- `npm install passport`
- `npm install webfinger`
- `npm install body-parser`
- `npm install express-session`
- `npm install cookie-parser`

此外，在快速入门的骨架项目内，我们对预览版使用了 `passport-azure-ad`。

- `npm install passport-azure-ad`

这会安装 `passport-azure-ad` 所依赖的库。

## <a name="set-up-your-app-to-use-the-passport-nodejs-strategy"></a>将应用设置为使用 Passport-Node.js 策略
将 Express 中间件配置为使用 OpenID Connect 身份验证协议。 Passport 用于发出登录和注销请求、管理用户会话、获取有关用户的信息，等等。

打开位于项目根目录中的 `config.js` 文件，并在 `exports.creds` 节中输入应用的配置值。
- `clientID`：在注册门户中为应用分配的**应用程序 ID**。
- `returnURL`：在门户中输入的**重定向 URI**。
- `tenantName`：应用的租户名称，例如 **contoso.onmicrosoft.com**。

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

打开项目根目录中的 `app.js` 文件。 添加以下调用，调用随 `passport-azure-ad` 附带的 `OIDCStrategy` 策略。


```JavaScript
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

// Add some logging
var log = bunyan.createLogger({
    name: 'Microsoft OIDC Example Web Application'
});
```

使用刚刚引用的策略来处理登录请求。

```JavaScript
// Use the OIDCStrategy in Passport (Section 2).
//
//   Strategies in Passport require a "validate" function that accepts
//   credentials (in this case, an OpenID identifier), and invokes a callback
//   by using a user object.
passport.use(new OIDCStrategy({
    callbackURL: config.creds.returnURL,
    realm: config.creds.realm,
    clientID: config.creds.clientID,
    oidcIssuer: config.creds.issuer,
    identityMetadata: config.creds.identityMetadata,
    skipUserProfile: config.creds.skipUserProfile,
    responseType: config.creds.responseType,
    responseMode: config.creds.responseMode,
    tenantName: config.creds.tenantName
  },
  function(iss, sub, profile, accessToken, refreshToken, done) {
    log.info('Example: Email address we received was: ', profile.email);
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
Passport 对所有策略都使用类似的模式（Twitter 和 Facebook 也不例外）。 所有策略编写器都遵循该模式。 查看该策略即可发现，为其传递了一个包含令牌的 `function()`，以及一个用作参数的 `done`。 策略在完成所有工作之后会返回。 存储用户和令牌，这样就不需要再次请求。

> [!IMPORTANT]
上面的代码会接受经过服务器身份验证的所有用户。 这是自动注册。 使用生产服务器时，如果用户未完成你所设置的注册过程，不会允许系统接受该用户。 通常可以在消费类应用中看到这种模式。 这样便可以使用 Facebook 来注册，但此后你必须按照要求填写其他信息。 如果应用程序不是示例应用程序，我们就可以从返回的令牌对象提取电子邮件地址，并要求用户填写其他信息。 由于这是测试服务器，因此，我们直接将用户添加到内存中的数据库。

根据 Passport 的要求，添加用于跟踪已登录用户的方法。 其中包括将用户信息序列化和反序列化：

```JavaScript

// Passport session setup. (Section 2)

//   To support persistent sign-in sessions, Passport needs to be able to
//   serialize users into and deserialize users out of sessions. Typically,
//   this is as simple as storing the user ID when Passport serializes a user
//   and finding the user by ID when Passport deserializes that user.
passport.serializeUser(function(user, done) {
  done(null, user.email);
});

passport.deserializeUser(function(id, done) {
  findByEmail(id, function (err, user) {
    done(err, user);
  });
});

// Array to hold users who have signed in
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

添加可加载 Express 引擎的代码。 在下面的示例中，可以看到我们使用 Express 所提供的默认的 `/views` 和 `/routes` 模式。

```JavaScript

// configure Express (Section 2)

var app = express();


app.configure(function() {
  app.set('views', __dirname + '/views');
  app.set('view engine', 'ejs');
  app.use(express.logger());
  app.use(express.methodOverride());
  app.use(cookieParser());
  app.use(expressSession({ secret: 'keyboard cat', resave: true, saveUninitialized: false }));
  app.use(bodyParser.urlencoded({ extended : true }));
  // Initialize Passport!  Also use passport.session() middleware to support
  // persistent sign-in sessions (recommended).
  app.use(passport.initialize());
  app.use(passport.session());
  app.use(app.router);
  app.use(express.static(__dirname + '/../../public'));
});

```

添加 `POST` 路由，以便将实际的登录请求递交到 `passport-azure-ad` 引擎：

```JavaScript

// Our Auth routes (Section 3)

// GET /auth/openid
//   Use passport.authenticate() as route middleware to authenticate the
//   request. The first step in OpenID authentication involves redirecting
//   the user to an OpenID provider. After the user is authenticated,
//   the OpenID provider redirects the user back to this application at
//   /auth/openid/return

app.get('/auth/openid',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Authentication was called in the Sample');
    res.redirect('/');
  });

// GET /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request. If authentication fails, the user will be redirected back to the
//   sign-in page. Otherwise, the primary route function will be called.
//   In this example, it redirects the user to the home page.
app.get('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {

    res.redirect('/');
  });

// POST /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request. If authentication fails, the user will be redirected back to the
//   sign-in page. Otherwise, the primary route function will be called.
//   In this example, it will redirect the user to the home page.

app.post('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {

    res.redirect('/');
  });
```

## <a name="use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>使用 Passport 向 Azure AD 发出登录和注销请求

现在，应用已正确配置为使用 OpenID Connect 身份验证协议与 v2.0 终结点通信。 `passport-azure-ad` 已处理有关创建身份验证消息、验证 Azure AD 提供的令牌以及保留用户会话的细节。 要做的就是为用户提供登录和注销方式，以及收集有关已登录用户的其他信息。

首先，向 `app.js` 文件添加默认方法、登录方法、帐户方法和注销方法：

```JavaScript

//Routes (Section 4)

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

这些方法详述如下：
- `/` 路由将重定向到 `index.ejs` 视图，并在请求中传递用户（如果存在）。
- `/account` 路由首先验证你是否经过身份验证（具体实施见下）， 然后在请求中传递用户，以便你获取该用户的其他信息。
- `/login` 路由从 `passport-azure-ad` 调用 `azuread-openidconnect` 验证器。 如果不成功，该路由会将用户重定向回 `/login`。
- `/logout` 直接调用 `logout.ejs`（及其路由）。 此操作会清除 Cookie，然后将用户返回到 `index.ejs`。


对于 `app.js` 的最后一部分，请添加在 `/account` 路由中使用的 `EnsureAuthenticated` 方法。

```JavaScript

// Simple route middleware to ensure that the user is authenticated. (Section 4)

//   Use this route middleware on any resource that needs to be protected. If
//   the request is authenticated (typically via a persistent sign-in session),
//   then the request will proceed. Otherwise, the user will be redirected to the
//   sign-in page.
function ensureAuthenticated(req, res, next) {
  if (req.isAuthenticated()) { return next(); }
  res.redirect('/login')
}

```

最后，在 `app.js` 中创建服务器本身。

```JavaScript

app.listen(3000);

```


## <a name="create-the-views-and-routes-in-express-to-call-your-policies"></a>在 Express 中创建调用策略所需的视图和路由

`app.js` 现已完成。 只需添加路由和视图，以便调用登录和注册策略。 这些策略也会处理你所创建的 `/logout` 和 `/login` 路由。

在根目录下创建 `/routes/index.js` 路由。

```JavaScript

/*
 * GET home page.
 */

exports.index = function(req, res){
  res.render('index', { title: 'Express' });
};
```

在根目录下创建 `/routes/user.js` 路由。

```JavaScript

/*
 * GET users listing.
 */

exports.list = function(req, res){
  res.send("respond with a resource");
};
```

这些简单的路由会将请求一路传递到视图。 请求中包括用户（如果存在）。

在根目录下创建 `/views/index.ejs` 视图。 这是一个简单的页面，用于调用登录和注销策略。也可将其用于获取帐户信息。 请注意，在请求中传递用户时，可以使用条件性的 `if (!user)`，提供用户已登录的证据。

```JavaScript
<% if (!user) { %>
    <h2>Welcome! Please sign in.</h2>
    <a href="/login/?p=your facebook policy">Sign in with Facebook</a>
    <a href="/login/?p=your email sign-in policy">Sign in with email</a>
    <a href="/login/?p=your email sign-up policy">Sign up with email</a>
<% } else { %>
    <h2>Hello, <%= user.displayName %>.</h2>
    <a href="/account">Account info</a></br>
    <a href="/logout">Log out</a>
<% } %>
```

在根目录下创建 `/views/account.ejs` 视图，以便查看 `passport-azure-ad` 放置在用户请求中的其他信息。

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
<p>Full Claims</p>
<%- JSON.stringify(user) %>
<p></p>
<a href="/logout">Log Out</a>
<% } %>
```

现在可以生成并运行应用。

运行 `node app.js` 并导航到 `http://localhost:3000`


使用电子邮件或 Facebook 注册或登录到应用。 注销后以其他用户身份重新登录。

##<a name="next-steps"></a>后续步骤

[以 .zip 文件提供](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/complete.zip)完整示例（不包括配置值）以供参考。 也可以从 GitHub 克隆它：

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-nodejs.git```

现在，可以转到更高级的主题。 可以尝试：

[在 Node.js 中使用 B2C 模型确保 Web API 的安全](active-directory-b2c-devquickstarts-api-node.md)

<!--

For additional resources, check out:
You can now move on to more advanced B2C topics. You might try:

[Call a Node.js web API from a Node.js web app]()

[Customizing the your B2C App's UX >>]()

-->
