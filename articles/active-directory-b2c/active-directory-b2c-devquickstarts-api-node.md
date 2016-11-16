---
title: "Azure AD B2C：使用 Node.js 保护 Web API | Microsoft Docs"
description: "如何构建可从 B2C 租户接受令牌的 Node.js Web API"
services: active-directory-b2c
documentationcenter: 
author: brandwe
manager: mbaldwin
editor: 
ms.assetid: fc2b9af8-fbda-44e0-962a-8b963449106a
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: hero-article
ms.date: 08/30/2016
ms.author: brandwe
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 833ba11df57e27cf1f5e4045d144550bb14ca1c2


---
# <a name="azure-ad-b2c-secure-a-web-api-by-using-nodejs"></a>Azure AD B2C：使用 Node.js 保护 Web API
<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

借助 Azure Active Directory (Azure AD) B2C，可以使用 OAuth 2.0 访问令牌来保护 Web API。 这些令牌允许使用 Azure AD B2C 的客户端应用对 API 进行身份验证。 本文说明如何创建一个“待办事项列表”API，使用户能够添加和列出任务。 Web API 使用 Azure AD B2C 进行保护，只允许经过身份验证的用户管理其待办事项列表。

> [!NOTE]
> 将使用 [iOS B2C 示例应用程序](active-directory-b2c-devquickstarts-ios.md)连接到这个编写的示例。 请先完成当前演练，然后遵循该示例。
> 
> 

**Passport** 是 Node.js 的身份验证中间件。 Passport 极其灵活且采用模块化结构，可以在不造成干扰的情况下安装在任何基于 Express 的应用程序或 Resitify Web 应用程序中。 一套综合性策略支持使用用户名和密码、Facebook、Twitter 等进行身份验证。 我们针对 Azure Active Directory (Azure AD) 开发了一个策略。 请安装此模块，然后添加 Azure AD `passport-azure-ad` 插件。

若要执行本示例，需要：

1. 将一个应用程序注册到 Azure AD。
2. 将应用程序设置为使用 Passport 的 `azure-ad-passport` 插件。
3. 配置一个客户端应用程序用于调用“待办事项列表”Web API。

## <a name="get-an-azure-ad-b2c-directory"></a>获取 Azure AD B2C 目录
只有在创建目录或租户之后，才可使用 Azure AD B2C。  目录是所有用户、应用、组等对象的容器。  如果没有容器，请先 [创建 B2C 目录](active-directory-b2c-get-started.md) ，然后再继续。

## <a name="create-an-application"></a>创建应用程序
接下来，需要在 B2C 目录中创建一个应用，以便为 Azure AD 提供一些必要信息，使它与应用安全通信。 在本例中，由于客户端应用与 Web API 构成一个逻辑应用，因此由单个 **应用程序 ID**表示。 若要创建应用，请遵循 [这些说明](active-directory-b2c-app-registration.md)。 请务必：

* 在应用程序中包含 **Web 应用/Web API**
* 在“回复 URL”中输入 `http://localhost/TodoListService`。 它是此代码示例的默认 URL。
* 为应用程序创建 **应用程序机密** 并复制下来。 稍后需要此数据。 请注意，此值在使用之前必须经过 [XML 转义](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) 。
* 复制分配给应用的 **应用程序 ID** 。 稍后需要此数据。

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>创建策略
在 Azure AD B2C 中，每个用户体验由 [策略](active-directory-b2c-reference-policies.md)定义。 此应用包含两种标识体验：注册和登录。 需要按 [策略参考文章](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)中所述，为每个类型创建一个策略。  创建三个策略时，请务必：

* 在注册策略中，选择“显示名称”和其他注册属性。
* 在每个策略中，选择“显示名称”和“对象 ID”应用程序声明。  也可以选择其他声明。
* 创建每个策略后，请复制策略的 **名称** 。 其前缀应为 `b2c_1_`。  稍后需要这些策略名称。

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

创建三个策略后，可以开始构建应用。

若要了解 Azure AD B2C 中策略的工作原理，请先阅读 [.NET Web 应用入门教程](active-directory-b2c-devquickstarts-web-dotnet.md)。

## <a name="download-the-code"></a>下载代码
本教程的代码 [保留在 GitHub 上](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS)。 若要根据说明构建示例，请 [下载 .zip 文件格式的骨干项目](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS/archive/skeleton.zip)。 也可以克隆骨干项目：

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS.git
```

完成的应用也[作为 .zip 文件提供](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS/archive/complete.zip)，或者放在同一存储库的 `complete` 分支中。

## <a name="download-nodejs-for-your-platform"></a>下载适用于平台的 Node.js
若要成功使用本示例，需要正确安装 Node.js。 

从 [nodejs.org](http://nodejs.org)安装 Node.js。

## <a name="install-mongodb-for-your-platform"></a>为平台安装 MongoDB
若要成功使用本示例，需要正确安装 MongoDB。 将使用 MongoDB 来使 REST API 持久保留在服务器实例之间。

从 [mongodb.org](http://www.mongodb.org)安装 MongoDB。

> [!NOTE]
> 本演练假设对 MongoDB 使用默认的安装和服务器终结点，在编写本文时，该终结点为 `mongodb://localhost`。
> 
> 

## <a name="install-the-restify-modules-in-your-web-api"></a>在 Web API 中安装 Restify 模块
将使用 Restify 构建 REST API。 Restify 是派生自 Express 的精简弹性 Node.js 应用程序框架。 它提供一套稳健功能用于在 Connect 的顶层构建 REST API。

### <a name="install-restify"></a>安装 Restify
在命令行中，将目录切换到 `azuread`。 如果 `azuread` 目录不存在，请创建该目录。

`cd azuread` 或 `mkdir azuread;`

输入以下命令：

`npm install restify`

此命令将安装 Restify。

#### <a name="did-you-get-an-error"></a>遇到了错误吗？
在某些操作系统上使用 `npm` 时，可能会收到 `Error: EPERM, chmod '/usr/local/bin/..'` 错误，并且系统要求以管理员身份运行帐户。 如果发生此问题，请使用 `sudo` 命令，以更高权限级别运行 `npm`。

#### <a name="did-you-get-a-dtrace-error"></a>是否遇到 DTrace 错误？
安装 Restify 时，可能会看到类似于下面的文本：

```Shell
clang: error: no such file or directory: 'HD/azuread/node_modules/restify/node_modules/dtrace-provider/libusdt'
make: *** [Release/DTraceProviderBindings.node] Error 1
gyp ERR! build error
gyp ERR! stack Error: `make` failed with exit code: 2
gyp ERR! stack     at ChildProcess.onExit (/usr/local/lib/node_modules/npm/node_modules/node-gyp/lib/build.js:267:23)
gyp ERR! stack     at ChildProcess.EventEmitter.emit (events.js:98:17)
gyp ERR! stack     at Process.ChildProcess._handle.onexit (child_process.js:789:12)
gyp ERR! System Darwin 13.1.0
gyp ERR! command "node" "/usr/local/lib/node_modules/npm/node_modules/node-gyp/bin/node-gyp.js" "rebuild"
gyp ERR! cwd /Volumes/Development HD/azuread/node_modules/restify/node_modules/dtrace-provider
gyp ERR! node -v v0.10.11
gyp ERR! node-gyp -v v0.10.0
gyp ERR! not ok
npm WARN optional dep failed, continuing dtrace-provider@0.2.8
```

Restify 提供强大的机制来使用 DTrace 跟踪 REST 调用。 但是，许多操作系统不提供 DTrace。 你可以安全地忽略这些错误。

命令输出应类似于以下文本：

    restify@2.6.1 node_modules/restify
    ├── assert-plus@0.1.4
    ├── once@1.3.0
    ├── deep-equal@0.0.0
    ├── escape-regexp-component@1.0.2
    ├── qs@0.6.5
    ├── tunnel-agent@0.3.0
    ├── keep-alive-agent@0.0.1
    ├── lru-cache@2.3.1
    ├── node-uuid@1.4.0
    ├── negotiator@0.3.0
    ├── mime@1.2.11
    ├── semver@2.2.1
    ├── spdy@1.14.12
    ├── backoff@2.3.0
    ├── formidable@1.0.14
    ├── verror@1.3.6 (extsprintf@1.0.2)
    ├── csv@0.3.6
    ├── http-signature@0.10.0 (assert-plus@0.1.2, asn1@0.1.11, ctype@0.5.2)
    └── bunyan@0.22.0 (mv@0.0.5)

## <a name="install-passport-in-your-web-api"></a>在 Web API 中安装 Passport
在命令行中，将目录切换到 `azuread`（如果不在此目录中）。

使用以下命令安装 Passport：

`npm install passport`

命令输出应类似于以下文本：

    passport@0.1.17 node_modules\passport
    ├── pause@0.0.1
    └── pkginfo@0.2.3

## <a name="add-passportazuread-to-your-web-api"></a>将 passport-azuread 添加到 Web API
接下来，使用 `passport-azuread`添加 OAuth 策略，这是一套将 Azure AD 连接到 Passport 的策略。 在 Rest API 示例中，将为持有者令牌使用此策略。

> [!NOTE]
> 尽管 OAuth2 提供了可以颁发任何已知令牌类型的框架，但只有一部分令牌类型已得到广泛使用。 用于保护终结点的令牌是持有者令牌。 这些类型的令牌是 OAuth2 中最广泛颁发的令牌。 许多实现假设持有者令牌是唯一颁发的令牌类型。
> 
> 

在命令行中，将目录切换到 `azuread`（如果不在此目录中）。

使用以下命令安装 Passport `passport-azure-ad` 模块：

`npm install passport-azure-ad`

命令输出应类似于以下文本：

``
passport-azure-ad@1.0.0 node_modules/passport-azure-ad
├── xtend@4.0.0
├── xmldom@0.1.19
├── passport-http-bearer@1.0.1 (passport-strategy@1.0.0)
├── underscore@1.8.3
├── async@1.3.0
├── jsonwebtoken@5.0.2
├── xml-crypto@0.5.27 (xpath.js@1.0.6)
├── ursa@0.8.5 (bindings@1.2.1, nan@1.8.4)
├── jws@3.0.0 (jwa@1.0.1, base64url@1.0.4)
├── request@2.58.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, tunnel-agent@0.4.1, oauth-sign@0.8.0, isstream@0.1.2, extend@2.0.1, json-stringify-safe@5.0.1, node-uuid@1.4.3, qs@3.1.0, combined-stream@1.0.5, mime-types@2.0.14, form-data@1.0.0-rc1, http-signature@0.11.0, bl@0.9.4, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
└── xml2js@0.4.9 (sax@0.6.1, xmlbuilder@2.6.4)
``

## <a name="add-mongodb-modules-to-your-web-api"></a>将 MongoDB 模块添加到 Web API
本示例使用 MongoDB 作为数据存储。 为此请安装 Mongoose，这是广泛用于管理模型和架构的插件。

* `npm install mongoose`

## <a name="install-additional-modules"></a>安装其他模块
接下来，安装剩余的所需模块。

在命令行中，将目录切换到 `azuread`（如果不在此目录中）：

`cd azuread`

在 `node_modules` 目录中安装模块：

* `npm install assert-plus`
* `npm install ejs`
* `npm install ejs-locals`
* `npm install express`
* `npm install bunyan`

## <a name="create-a-serverjs-file-with-your-dependencies"></a>创建包含依赖项的 server.js 文件
`server.js` 文件提供 Web API 服务器的大多数功能。 

在命令行中，将目录切换到 `azuread`（如果不在此目录中）：

`cd azuread`

在编辑器中创建 `server.js` 文件。 添加以下信息：

```Javascript
'use strict';
/**
* Module dependencies.
*/
var fs = require('fs');
var path = require('path');
var util = require('util');
var assert = require('assert-plus');
var mongoose = require('mongoose/');
var bunyan = require('bunyan');
var restify = require('restify');
var config = require('./config');
var passport = require('passport');
var OIDCBearerStrategy = require('passport-azure-ad').BearerStrategy;
```

保存文件。 稍后将返回处理此文件。

## <a name="create-a-configjs-file-to-store-your-azure-ad-settings"></a>创建 config.js 文件用于存储 Azure AD 设置
此代码文件将配置参数从 Azure AD 门户传递到 `Passport.js` 文件。 在本演练的第一部分中向门户添加 Web API 时，已经创建了这些配置值。 我们将解释在复制代码后，要输入其中的哪些参数值。

在命令行中，将目录切换到 `azuread`（如果不在此目录中）：

`cd azuread`

在编辑器中创建 `config.js` 文件。 添加以下信息：

```Javascript
// Don't commit this file to your public repos. This config is for first-run
exports.creds = {
clientID: <your client ID for this Web API you created in the portal>
mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
audience: '<your audience URI>', // the Client ID of the application that is calling your API, usually a web API or native client
identityMetadata: 'https://login.microsoftonline.com/<tenant name>/.well-known/openid-configuration', // Make sure you add the B2C tenant name in the <tenant name> area
tenantName:'<tenant name>', 
policyName:'b2c_1_<sign in policy name>' // This is the policy you'll want to validate against in B2C. Usually this is your Sign-in policy (as users sign in to this API)
passReqToCallback: false // This is a node.js construct that lets you pass the req all the way back to any upstream caller. We turn this off as there is no upstream caller.
};

```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

### <a name="required-values"></a>必需的值
`clientID`：Web API 应用程序的客户端 ID。

`IdentityMetadata`：`passport-azure-ad` 在此处查找标识提供者的配置数据。 它还会查找密钥以验证 JSON Web 令牌。 

`audience`：门户中的统一资源标识符 (URI)，用于标识调用应用程序。 

`tenantName`：租户名称（例如 **contoso.onmicrosoft.com**）。

`policyName`：用于验证传入服务器的令牌的策略。 此策略应与针对客户端应用程序登录所用的策略相同。

> [!NOTE]
> 在 B2C 预览版中，请将相同的策略用于客户端与服务器设置。 如果已完成演练并创建这些策略，则不需要再做一次。 因为已完成此演练，不需要在站点上为客户端演练创建新策略。
> 
> 

## <a name="add-configuration-to-your-serverjs-file"></a>将配置添加到 server.js 文件
若要从创建的 `config.js` 文件中读取值，请在应用程序中添加 `.config` 文件作为必需资源，然后将全局变量设置为 `config.js` 文档中的值。

在命令行中，将目录切换到 `azuread`（如果不在此目录中）：

`cd azuread`

在编辑器中打开 `server.js` 文件。 添加以下信息：

```Javascript
var config = require('./config');
```
将包含以下代码的新节添加到 `server.js` ：

```Javascript
// We pass these options in to the ODICBearerStrategy.

var options = {
    // The URL of the metadata document for your app. We put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
    identityMetadata: config.creds.identityMetadata,
    clientID: config.creds.clientID,
    tenantName: config.creds.tenantName,
    policyName: config.creds.policyName,
    validateIssuer: config.creds.validateIssuer,
    audience: config.creds.audience,
    passReqToCallback: config.creds.passReqToCallback

};
```

接下来，为用户添加一些从调用应用程序接收的占位符。

```Javascript
// array to hold logged in users and the current logged in user (owner)
var users = [];
var owner = null;
```

继续创建记录器。

```Javascript
// Our logger
var log = bunyan.createLogger({
    name: 'Microsoft Azure Active Directory Sample'
});
```

## <a name="add-the-mongodb-model-and-schema-information-by-using-mongoose"></a>使用 Mongoose 添加 MongoDB 模型和架构信息
提前准备有助于在 REST API 服务中组合这三个文件。

在本演练中，请如前所述使用 MongoDB 来存储任务。

在 `config.js` 文件中，将数据库命名为 **tasklist**。 该名称也是在 `mongoose_auth_local` 连接 URL 末尾处输入的内容。 不需要在 MongoDB 中事先创建此数据库。 首次运行服务器应用程序时，将自动创建该数据库。

在告诉服务器要使用哪个 MongoDB 数据库之后，需要编写一些额外的代码，为服务器任务创建模型和架构。

### <a name="expand-the-model"></a>扩展模型
此架构模型非常简单。 可以根据需要来扩展它。

`owner`：分配给任务的人员。 此对象是 **字符串**。  

`Text`：任务本身。 此对象是 **字符串**。

`date`：任务截止日期。 此对象是 **日期时间**。

`completed`：如果任务已完成。 此对象是 **布尔值**。

### <a name="create-the-schema-in-the-code"></a>在代码中创建架构
在命令行中，将目录切换到 `azuread`（如果不在此目录中）：

`cd azuread`

在编辑器中打开 `server.js` 文件。 在配置项下面添加以下信息：

```Javascript
// MongoDB setup
// Setup some configuration
var serverPort = process.env.PORT || 3000; // Note we are hosting our API on port 3000
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;

// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');

// Here we create a schema to store our tasks and users. Pretty simple schema for now.
var TaskSchema = new Schema({
    owner: String,
    Text: String,
    completed: Boolean,
    date: Date
});

// Use the schema to register a model
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```
首先创建架构，然后创建模型对象，在定义 **路由**时用于存储整个代码中的数据。

## <a name="add-routes-for-your-rest-api-task-server"></a>添加 REST API 任务服务器的路由
现已创建可以使用的数据库模型，接下来请添加用于 REST API 服务器的路由。

### <a name="about-routes-in-restify"></a>关于 Restify 中的路由
Restify 中路由的工作原理与使用 Express 堆栈时的工作原理相同。 可以使用客户端应用程序应该调用的 URI 定义路由。 

Restify 路由的典型模式是：

```Javascript
function createObject(req, res, next) {
// do work on Object
_object.name = req.params.object; // passed value is in req.params under object
///...
return next(); // keep the server going
}
....
server.post('/service/:add/:object', createObject); // calls createObject on routes that match this.
```

Resitfy 和 Express 提供更深层的功能，例如，定义应用程序类型，以及跨不同的终结点执行复杂路由。 在本教程中，我们将保持使用简单路由。

#### <a name="add-default-routes-to-your-server"></a>将默认路由添加到服务器
现在，请为 REST API 添加 **create** 和 **list** 的基本 CRUD 路由。 在示例的 `complete` 分支中可以找到其他路由。

在命令行中，将目录切换到 `azuread`（如果不在此目录中）：

`cd azuread`

在编辑器中打开 `server.js` 文件。 在前面输入的数据库项下面添加以下信息：

```Javascript
/**
 *
 * APIs for our REST Task server
 */

// Create a task

function createTask(req, res, next) {

    // Resitify currently has a bug which doesn't allow you to set default headers
    // This headers comply with CORS and allow us to mongodbServer our response to any origin

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    // Create a new task model, fill it up and save it to Mongodb
    var _task = new Task();

    if (!req.params.Text) {
        req.log.warn({
            params: req.params
        }, 'createTodo: missing task');
        next(new MissingTaskError());
        return;
    }

    _task.owner = owner;
    _task.Text = req.params.Text;
    _task.date = new Date();

    _task.save(function(err) {
        if (err) {
            req.log.warn(err, 'createTask: unable to save');
            next(err);
        } else {
            res.send(201, _task);

        }
    });

    return next();

}
```

```Javascript
/// Simple returns the list of TODOs that were loaded.

function listTasks(req, res, next) {
    // Resitify currently has a bug which doesn't allow you to set default headers
    // This headers comply with CORS and allow us to mongodbServer our response to any origin

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    log.info("listTasks was called for: ", owner);

    Task.find({
        owner: owner
    }).limit(20).sort('date').exec(function(err, data) {

        if (err)
            return next(err);

        if (data.length > 0) {
            log.info(data);
        }

        if (!data.length) {
            log.warn(err, "There is no tasks in the database. Add one!");
        }

        if (!owner) {
            log.warn(err, "You did not pass an owner when listing tasks.");
        } else {

            res.json(data);

        }
    });

    return next();
}
```


#### <a name="add-error-handling-for-the-routes"></a>为路由添加错误处理
添加一些错误处理，以便将遇到的任何问题以客户端可理解的方式返回给客户端。

添加以下代码：

```Javascript
///--- Errors for communicating something interesting back to the client
function MissingTaskError() {
restify.RestError.call(this, {
statusCode: 409,
restCode: 'MissingTask',
message: '"task" is a required parameter',
constructorOpt: MissingTaskError
});
this.name = 'MissingTaskError';
}
util.inherits(MissingTaskError, restify.RestError);
function TaskExistsError(owner) {
assert.string(owner, 'owner');
restify.RestError.call(this, {
statusCode: 409,
restCode: 'TaskExists',
message: owner + ' already exists',
constructorOpt: TaskExistsError
});
this.name = 'TaskExistsError';
}
util.inherits(TaskExistsError, restify.RestError);
function TaskNotFoundError(owner) {
assert.string(owner, 'owner');
restify.RestError.call(this, {
statusCode: 404,
restCode: 'TaskNotFound',
message: owner + ' was not found',
constructorOpt: TaskNotFoundError
});
this.name = 'TaskNotFoundError';
}
util.inherits(TaskNotFoundError, restify.RestError);
```


## <a name="create-your-server"></a>创建服务器
现已定义数据库并准备好路由。 最后一件事是添加服务器实例用于管理调用。

Restify 和 Express 为 REST API 服务器提供深度自定义功能，但此处使用最基本的设置。 

```Javascript

**
 * Our Server
 */


var server = restify.createServer({
    name: "Microsoft Azure Active Directroy TODO Server",
    version: "2.0.1"
});

// Ensure we don't drop data on uploads
server.pre(restify.pre.pause());

// Clean up sloppy paths like //todo//////1//
server.pre(restify.pre.sanitizePath());

// Handles annoying user agents (curl)
server.pre(restify.pre.userAgentConnection());

// Set a per request bunyan logger (with requestid filled in)
server.use(restify.requestLogger());

// Allow 5 requests/second by IP, and burst to 10
server.use(restify.throttle({
    burst: 10,
    rate: 5,
    ip: true,
}));

// Use the common stuff you probably want
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
    mapParams: true
})); // Allows for JSON mapping to REST
server.use(restify.authorizationParser()); // Looks for authorization headers

// Let's start using Passport.js

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support


```
## <a name="add-the-routes-to-the-server-without-authentication"></a>将路由添加到服务器（不包括身份验证）
```Javascript
server.get('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), listTasks);
server.get('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), listTasks);
server.get('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), getTask);
server.head('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), getTask);
server.post('/api/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
    session: false
}), createTask);
server.post('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), createTask);
server.del('/api/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), removeAll, function respond(req, res, next) {
    res.send(204);
    next();
});


// Register a default '/' handler

server.get('/', function root(req, res, next) {
    var routes = [
        'GET     /',
        'POST    /api/tasks/:owner/:task',
        'POST    /api/tasks (for JSON body)',
        'GET     /api/tasks',
        'PUT     /api/tasks/:owner',
        'GET     /api/tasks/:owner',
        'DELETE  /api/tasks/:owner/:task'
    ];
    res.send(200, routes);
    next();
});
```

```Javascript

server.listen(serverPort, function() {

    var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
    consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
    consoleMessage += '\n %s server is listening at %s';
    consoleMessage += '\n Open your browser to %s/api/tasks\n';
    consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
    consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
    consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';

    //log.info(consoleMessage, server.name, server.url, server.url, server.url);

});

``` 

## <a name="add-authentication-to-your-rest-api-server"></a>将身份验证添加到 REST API 服务器
设置好正常运行的 REST API 服务器后，可以让它对 Azure AD 发挥作用。

在命令行中，将目录切换到 `azuread`（如果不在此目录中）：

`cd azuread`

### <a name="use-the-oidcbearerstrategy-that-is-included-with-passportazuread"></a>使用 passport-azure-ad 随附的 OIDCBearerStrategy
> [!TIP]
> 编写 API 时，应始终将数据链接到用户无法证明其在令牌中是唯一的项目。 当服务器存储待办事项时，会根据我们放在“owner”字段的令牌（通过 token.oid 调用）中的用户对象 **oid** 来存储。 此值可确保只有该用户可以访问自己的待办事项。 “owner”API 中不公开任何信息，因此，外部用户可以请求其他人的待办事项，即使他们已经过身份验证。
> 
> 

接下来，使用 `passport-azure-ad`随附的持有者策略。

```Javascript
var findById = function(id, fn) {
    for (var i = 0, len = users.length; i < len; i++) {
        var user = users[i];
        if (user.oid === id) {
            log.info('Found user: ', user);
            return fn(null, user);
        }
    }
    return fn(null, null);
};


var oidcStrategy = new OIDCBearerStrategy(options,
    function(token, done) {
        log.info('verifying the user');
        log.info(token, 'was the token retreived');
        findById(token.sub, function(err, user) {
            if (err) {
                return done(err);
            }
            if (!user) {
                // "Auto-registration"
                log.info('User was added automatically as they were new. Their sub is: ', token.oid);
                users.push(token);
                owner = token.oid;
                return done(null, token);
            }
            owner = token.sub;
            return done(null, user, token);
        });
    }
);

passport.use(oidcStrategy);
```

Passport 对其所有策略使用相同的模式。 需要向它传递 `function()`，其中包含 `token` 和 `done` 作为参数。 策略在完成所有工作之后将会返回。 然后，应该存储用户并保存令牌，以便不需要再次请求。

> [!IMPORTANT]
> 上述代码接受正好向服务器进行身份验证的任何用户。 此过程称为自动注册。 在生产服务器中，除非用户先完成注册过程，否则不允许他们访问 API。 此过程是使用者应用中常见的模式，允许使用 Facebook 来注册，但然后要求填写其他信息。 如果此程序不是命令行程序，就只能从返回的令牌对象中提取电子邮件，然后要求用户填写其他信息。 由于这是一个示例，我们要将其添加到内存中的数据库。
> 
> 

## <a name="run-your-server-application-to-verify-that-it-rejects-you"></a>运行服务器应用程序以验证它是否拒绝请求
可以使用 `curl` 来检查现在是否为终结点提供 OAuth2 保护。 返回的标头应该足以表明执行的操作正确无误。

确保 MongoDB 实例正在运行：

    $sudo mongodb

切换到相应的目录并运行服务器：

    $ cd azuread
    $ node server.js

在新终端窗口中运行 `curl`

试用基本 POST：

`$ curl -isS -X POST http://127.0.0.1:3000/api/tasks/brandon/Hello`

```Shell
HTTP/1.1 401 Unauthorized
Connection: close
WWW-Authenticate: Bearer realm="Users"
Date: Tue, 14 Jul 2015 05:45:03 GMT
Transfer-Encoding: chunked
```

401 错误是所需的响应。 它表示 Passport 层正在尝试重定向到授权终结点。

## <a name="you-now-have-a-rest-api-service-that-uses-oauth2"></a>现已创建了一个使用 OAuth2 的 REST API 服务
已使用 Restify 和 OAuth 实现 REST API！ 现在已经有足够的代码可以继续开发服务，并在本示例的基础上构建应用。 这就是在不使用 OAuth2 兼容客户端的情况下，此服务器能够带来的最大优势。 接下来，我们将学习其他演练，例如 [Connect to a web API by using iOS with B2C](active-directory-b2c-devquickstarts-ios.md) （使用 iOS 和 B2C 连接到 Web API）演练。

## <a name="next-steps"></a>后续步骤
现在，可以转到更高级主题，例如：

[Connect to a web API by using iOS with B2C](active-directory-b2c-devquickstarts-ios.md)




<!--HONumber=Nov16_HO2-->


