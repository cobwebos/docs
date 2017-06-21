---
title: "使用 Node.js 保护 Azure Active Directory v2.0 Web API | Microsoft Docs"
description: "了解如何构建一个可从个人 Microsoft 帐户及公司或学校帐户接受令牌的 Node.js Web API。"
services: active-directory
documentationcenter: nodejs
author: navyasric
manager: mbaldwin
editor: 
ms.assetid: 0b572fc1-2aaf-4cb6-82de-63010fb1941d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 05/13/2017
ms.author: nacanuma
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: de674af369080ad7eb608608685e293f2326c8e6
ms.openlocfilehash: d42b96b0fb02b6c2df364d5e19f3345991ee03b1
ms.contentlocale: zh-cn
ms.lasthandoff: 05/04/2017


---
# <a name="secure-a-web-api-by-using-nodejs"></a>使用 Node.js 保护 Web API
> [!NOTE]
> 并非所有 Azure Active Directory 方案和功能都可与 v2.0 终结点配合使用。 若要确定是应该使用 v2.0 终结点还是应该使用 v1.0 终结点，请阅读 [v2.0 限制](active-directory-v2-limitations.md)。
> 
> 

使用 Azure Active Directory (Azure AD) v2.0 终结点时，可以使用 [OAuth 2.0](active-directory-v2-protocols.md) 访问令牌来保护 Web API。 同时拥有个人 Microsoft 帐户以及工作或学校帐户的用户可以使用 OAuth 2.0 访问令牌安全访问你的 Web API。

*Passport* 是 Node.js 的身份验证中间件。 Passport 很灵活并且采用模块化结构，可以在不造成干扰的情况下放入任何基于 Express 的应用程序或 Resitify Web 应用程序。 在 Passport 中，有一套综合性策略支持使用用户名和密码、Facebook、Twitter 或其他选项进行身份验证。 我们针对 Azure AD 开发了一项策略。 本文介绍如何安装该模块及添加 Azure AD `passport-azure-ad` 插件。

## <a name="download"></a>下载
本教程的代码 [在 GitHub 上](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs)维护。 若要按照本教程操作，可以[下载 .zip 文件格式的应用框架](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs/archive/skeleton.zip)，或克隆该框架：

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs.git```

还可以在本教程结束时获得完整的应用程序。

## <a name="1-register-an-app"></a>1：注册应用
在 [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) 上创建新应用，或按照[以下详细步骤](active-directory-v2-app-registration.md)注册应用。 确保：

* 复制分配给应用的**应用程序 ID**。 在本教程中需要用到它。
* 为应用添加**移动**平台。
* 从门户复制**重定向 URI**。 必须使用默认的 URI 值 `urn:ietf:wg:oauth:2.0:oob`。

## <a name="2-install-nodejs"></a>2：安装 Node.js
若要使用本教程中的示例，必须[安装 Node.js](http://nodejs.org)。

## <a name="3-install-mongodb"></a>3：安装 MongoDB
若要成功使用本示例，必须[安装 MongoDB](http://www.mongodb.org)。 本示例使用 MongoDB 来使 REST API 持久保留在服务器实例之间。

> [!NOTE]
> 本文假设对 MongoDB 使用默认的安装与服务器终结点：mongodb://localhost。
> 
> 

## <a name="4-install-the-restify-modules-in-your-web-api"></a>4：在 Web API 中安装 Restify 模块
我们使用 Resitfy 来构建 REST API。 Restify 是派生自 Express 的精简弹性 Node.js 应用程序框架。 Restify 提供一套稳健功能用于在 Connect 的顶层构建 REST API。

### <a name="install-restify"></a>安装 Restify
1.  在命令行中，将目录切换到 **azuread**：

    `cd azuread`

    如果 **azuread** 目录不存在，请创建该目录。

    `mkdir azuread`

2.  安装 Restify：

    `npm install restify`

    此命令的输出应如下所示：

    ```
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
    └── bunyan@0.22.0(mv@0.0.5)
    ```

#### <a name="did-you-get-an-error"></a>遇到了错误吗？
在某些操作系统上使用 `npm` 命令时，可能会看到此消息：`Error: EPERM, chmod '/usr/local/bin/..'`。 发生此错误后，接着会要求以管理员身份尝试运行帐户。 如果发生此问题，请使用 `sudo` 命令，以更高特权级别运行 `npm`。

#### <a name="did-you-get-an-error-related-to-dtrace"></a>遇到了 DTrace 相关的错误吗？
安装 Restify 时，可能会看到以下消息：

```Shell
clang: error: no such file or directory: 'HD/azuread/node_modules/restify/node_modules/dtrace-provider/libusdt'
make: *** [Release/DTraceProviderBindings.node] Error 1
gyp ERR! build error
gyp ERR! stack Error: `make` failed with exit code: two
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

Restify 提供强大的机制来使用 DTrace 跟踪 REST 调用。 但是，许多操作系统不提供 DTrace。 可以放心地忽略此错误消息。


## <a name="5-install-passportjs-in-your-web-api"></a>5：将在 Passport.js 安装到 Web API
1.  在命令行提示符下，将目录切换到 **azuread**。

2.  安装 Passport.js：

    `npm install passport`

    此命令的输出应如下所示：

    ```
     passport@0.1.17 node_modules\passport
    ├── pause@0.0.1
    └── pkginfo@0.2.3
    ```

## <a name="6-add-passport-azure-ad-to-your-web-api"></a>6：将 passport-azure-ad 添加到 Web API
接下来，使用 passport-azuread 添加 OAuth 策略。 `passport-azuread` 是一套将 Azure AD 连接到 Passport 的策略。 在此 REST API 示例中，我们将针对持有者令牌使用此策略。

> [!NOTE]
> 尽管 OAuth 2.0 提供了可以颁发任何已知令牌类型的框架，但我们通常使用其中的某些令牌类型。 我们往往使用持有者令牌保护终结点。 持有者令牌是 OAuth 2.0 中最广泛颁发的令牌类型。 许多 OAuth 2.0 实现假设持有者令牌是唯一颁发的令牌类型。
> 
> 

1.  在命令行中，将目录切换到 **azuread**。

    `cd azuread`

2.  安装 Passport.js `passport-azure-ad` 模块：

    `npm install passport-azure-ad`

    此命令的输出应如下所示：

    ```
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
    ```

## <a name="7-add-mongodb-modules-to-your-web-api"></a>7：将 MongoDB 模块添加到 Web API
本示例使用 MongoDB 作为数据存储。 

1.  安装 Mongoose，这是广泛用于管理模型和架构的插件： 

    `npm install mongoose`

2.  安装 MongoDB 的数据库驱动程序（也称为 MongoDB）：

    `npm install mongodb`

## <a name="8-install-additional-modules"></a>8：安装其他模块
安装剩余的所需模块。

1.  在命令行中，将目录切换到 **azuread**：

    `cd azuread`

2.  输入以下命令。 这些命令在 node_modules 目录中安装以下模块：

    *   `npm install crypto`
    *   `npm install assert-plus`
    *   `npm install posix-getopt`
    *   `npm install util`
    *   `npm install path`
    *   `npm install connect`
    *   `npm install xml-crypto`
    *   `npm install xml2js`
    *   `npm install xmldom`
    *   `npm install async`
    *   `npm install request`
    *   `npm install underscore`
    *   `npm install grunt-contrib-jshint@0.1.1`
    *   `npm install grunt-contrib-nodeunit@0.1.2`
    *   `npm install grunt-contrib-watch@0.2.0`
    *   `npm install grunt@0.4.1`
    *   `npm install xtend@2.0.3`
    *   `npm install bunyan`
    *   `npm update`

## <a name="9-create-a-serverjs-file-for-your-dependencies"></a>9：创建依赖项的 Server.js 文件
Server.js 文件保存 Web API 服务器的大多数功能。 大部分代码将添加到此文件中。 用于生产目的，可以将功能重构为较小的文件，例如单独的路由和控制器。 本文为此目的使用 Server.js。

1.  在命令行中，将目录切换到 **azuread**：

    `cd azuread`

2.  使用所选的编辑器创建 Server.js 文件。 在文件中添加以下信息：

    ```Javascript
    'use strict';
    /**
    * Module dependencies.
    */
    var util = require('util');
    var assert = require('assert-plus');
    var mongoose = require('mongoose/');
    var bunyan = require('bunyan');
    var restify = require('restify');
    var config = require('./config');
    var passport = require('passport');
    var OIDCBearerStrategy = require('passport-azure-ad').OIDCStrategy;
    ```

3.  保存文件。 稍后将要使用此文件。

## <a name="10-create-a-config-file-to-store-your-azure-ad-settings"></a>10：创建一个配置文件用于存储 Azure AD 设置
此代码文件将配置参数从 Azure AD 门户传递到 Passport.js。 在本文开头部分向门户添加 Web API 时，已创建了这些配置值。 在你复制代码后，我们将解释要输入其中的哪些参数值。

1.  在命令行中，将目录切换到 **azuread**：

    `cd azuread`

2.  在编辑器中创建 Config.js 文件。 添加以下信息：

    ```Javascript
    // Don't commit this file to your public repos. This config is for first-run.
    exports.creds = {
    mongoose_auth_local: 'mongodb://localhost/tasklist', // Your Mongo auth URI goes here.
    issuer: 'https://sts.windows.net/**<your application id>**/',
    audience: '<your redirect URI>',
    identityMetadata: 'https://login.microsoftonline.com/common/.well-known/openid-configuration' // For Microsoft, you should never need to change this.
    };

    ```



### <a name="required-values"></a>必需的值

*   **IdentityMetadata**：`passport-azure-ad` 将在此处查找适用于标识提供者 (IDP) 的配置数据，以及用于验证 JSON Web 令牌 (JWT) 的密钥。 如果使用 Azure AD，可能不需要更改此值。

*   audience：来自门户的重定向 URI。

> [!NOTE]
> 频繁滚动更新密钥。 请确保始终从“openid_keys”URL 提取密钥，并且应用程序能够访问 Internet。
> 
> 

## <a name="11-add-the-configuration-to-your-serverjs-file"></a>11：将配置添加到 Server.js 文件
应用程序需要从刚刚创建的配置文件读取值。 在应用程序中添加 .config 文件作为所需的资源。 将全局变量设置为 Config.js 中的值。

1.  在命令行提示符下，将目录切换到 **azuread**：

    `cd azuread`

2.  在编辑器中打开 Server.js。 添加以下信息：

    ```Javascript
    var config = require('./config');
    ```

3.  将一个新节添加到 Server.js：

    ```Javascript
    // Pass these options in to the ODICBearerStrategy.
    var options = {
    // The URL of the metadata document for your app. Put the keys for token validation from the URL found in the jwks_uri tag in the metadata.
    identityMetadata: config.creds.identityMetadata,
    issuer: config.creds.issuer,
    audience: config.creds.audience
    };
    // Array to hold signed-in users and the current signed-in user (owner).
    var users = [];
    var owner = null;
    // Your logger
    var log = bunyan.createLogger({
    name: 'Microsoft Azure Active Directory Sample'
    });
    ```

## <a name="12-add-the-mongodb-model-and-schema-information-by-using-mongoose"></a>12：使用 Mongoose 添加 MongoDB 模型和架构信息
接下来，在 REST API 服务中连接这三个文件。

本文使用 MongoDB 来存储任务。 *步骤 4* 将介绍此操作。

在步骤 11 创建的 Config.js 文件中，数据库名为 *tasklist*。 该名称也是在 mongoose_auth_local 连接 URL 末尾处输入的内容。 不需要在 MongoDB 中事先创建此数据库。 首次运行服务器应用程序时，将会创建该数据库（假设该数据库尚不存在）。

已告知服务器要使用哪个 MongoDB 数据库。 接下来，需要编写一些额外的代码，为服务器的任务创建模型和架构。

### <a name="the-model"></a>模型
架构模型非常简单。 可以根据需要扩展它。 

架构模型具有以下值：

*   **NAME**。 分配给任务的人员。 这是一个**字符串**值。
*   **TASK**。 任务的名称。 这是一个**字符串**值。
*   **DATE**。 任务截止日期。 这是一个**日期时间**值。
*   **COMPLETED**。 任务是否已完成。 这是一个**布尔**值。

### <a name="create-the-schema-in-the-code"></a>在代码中创建架构
1.  在命令行中，将目录切换到 **azuread**：

    `cd azuread`

2.  在编辑器中打开 Server.js。 在配置项下面添加以下信息：

    ```Javascript
    // MongoDB setup.
    // Set up some configuration.
    var serverPort = process.env.PORT || 8080;
    var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
    // Connect to MongoDB.
    global.db = mongoose.connect(serverURI);
    var Schema = mongoose.Schema;
    log.info('MongoDB Schema loaded');
    ```

此代码连接到 MongoDB 服务器。 它还返回架构对象。

#### <a name="using-the-schema-create-your-model-in-the-code"></a>使用该架构在代码中创建模型
在上述代码的下面添加以下代码：

```Javascript
// Create a basic schema to store your tasks and users.
var TaskSchema = new Schema({
owner: String,
task: String,
completed: Boolean,
date: Date
});
// Use the schema to register a model.
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```

从该代码中可以看到，首先会创建架构。 接下来，创建模型对象。 定义**路由**时，将使用模型对象存储整个代码中的数据。

## <a name="13-add-your-routes-for-your-task-rest-api-server"></a>13：为任务 REST API 服务器添加路由
现已创建可以使用的数据库模型，接下来请添加用于 REST API 服务器的路由。

### <a name="about-routes-in-restify"></a>关于 Restify 中的路由
Restify 中路由的工作原理与使用 Express 堆栈时的工作原理完全相同。 可以使用客户端应用程序应该调用的 URI 定义路由。 通常，需要在单独的文件中定义路由。 本教程将在 Server.js 中放置路由。 对于生产用途，我们建议在各自的文件中构建路由。

Restify 路由的典型模式是：

```Javascript
function createObject(req, res, next) {
// Do work on object.
_object.name = req.params.object; // Passed value is in req.params under object.
///...
return next(); // Keep the server going.
}
....
server.post('/service/:add/:object', createObject); // calls createObject on routes that match this.
```


这是最基本级别的模式。 Resitfy（以及 Express）提供更深层的功能，例如，定义应用程序类型，以及跨不同的终结点提供复杂路由。

#### <a name="add-default-routes-to-your-server"></a>将默认路由添加到服务器
添加基本 CRUD 路由：**create**、**retrieve**、**update** 和 **delete**。

1.  在命令行中，将目录切换到 **azuread**：

    `cd azuread`

2.  在编辑器中打开 Server.js。 在前面输入的数据库项下面添加以下信息：

    ```Javascript
    /**
    *
    * APIs for your REST task server
    */
    // Create a task.
    function createTask(req, res, next) {
    // Resitify currently has a bug that doesn't allow you to set default headers.
    // These headers comply with CORS, and allow you to use MongoDB Server as your response to any origin.
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");
    // Create a new task model, fill it, and save it to MongoDB.
    var _task = new Task();
    if (!req.params.task) {
    req.log.warn({
    params: p
    }, 'createTodo: missing task');
    next(new MissingTaskError());
    return;
    }
    _task.owner = owner;
    _task.task = req.params.task;
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
    // Delete a task by name.
    function removeTask(req, res, next) {
    Task.remove({
    task: req.params.task,
    owner: owner
    }, function(err) {
    if (err) {
    req.log.warn(err,
    'removeTask: unable to delete %s',
    req.params.task);
    next(err);
    } else {
    log.info('Deleted task:', req.params.task);
    res.send(204);
    next();
    }
    });
    }
    // Delete all tasks.
    function removeAll(req, res, next) {
    Task.remove();
    res.send(204);
    return next();
    }
    // Get a specific task based on name.
    function getTask(req, res, next) {
    log.info('getTask was called for: ', owner);
    Task.find({
    owner: owner
    }, function(err, data) {
    if (err) {
    req.log.warn(err, 'get: unable to read %s', owner);
    next(err);
    return;
    }
    res.json(data);
    });
    return next();
    }
    /// Returns the list of TODOs that were loaded.
    function listTasks(req, res, next) {
    // Resitify currently has a bug that doesn't allow you to set default headers.
    // These headers comply with CORS, and allow us to use MongoDB Server as our response to any origin.
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
    log.warn(err, "There are no tasks in the database. Add one!");
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

### <a name="add-error-handling-for-the-routes"></a>为路由添加错误处理
添加一些错误处理，以便将遇到的问题传达给客户端。

添加以下代码（前面已编写这些代码）：

```Javascript
///--- Errors for communicating something more information back to the client.
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


## <a name="14-create-your-server"></a>14：创建服务器
最后一个步骤是添加服务器实例。 服务器实例用于管理调用。

Restify（和 Express）提供可对 REST API 服务器使用的深度自定义功能。 本教程使用最基本的设置。

```Javascript
/**
* Your server
*/
var server = restify.createServer({
name: "Microsoft Azure Active Directory TODO Server",
version: "2.0.1"
});
// Ensure that you don't drop data on uploads.
server.pre(restify.pre.pause());
// Clean up imprecise paths like //todo//////1//.
server.pre(restify.pre.sanitizePath());
// Handle annoying user agents (curl).
server.pre(restify.pre.userAgentConnection());
// Set a per-request Bunyan logger (with requestid filled in).
server.use(restify.requestLogger());
// Allow 5 requests/second by IP address, and burst to 10.
server.use(restify.throttle({
burst: 10,
rate: 5,
ip: true,
}));
// Use common commands, such as:
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
mapParams: true
}));
```
## <a name="15-add-the-routes-without-authentication-for-now"></a>15：添加路由（目前不包括身份验证）
```Javascript
/// Use CRUD to add the real handlers.
/**
/*
/* Each of these handlers is protected by your Open ID Connect Bearer strategy. Invoke 'oidc-bearer'
/* in the pasport.authenticate() method. Because REST is stateless, set 'session: false'. You 
/* don't need to maintain session state. You can experiment with removing API protection.
/* To do this, remove the passport.authenticate() method:
/*
/* server.get('/tasks', listTasks);
/*
**/
server.get('/tasks', listTasks);
server.get('/tasks', listTasks);
server.get('/tasks/:owner', getTask);
server.head('/tasks/:owner', getTask);
server.post('/tasks/:owner/:task', createTask);
server.post('/tasks', createTask);
server.del('/tasks/:owner/:task', removeTask);
server.del('/tasks/:owner', removeTask);
server.del('/tasks', removeTask);
server.del('/tasks', removeAll, function respond(req, res, next) {
res.send(204);
next();
});
// Register a default '/' handler
server.get('/', function root(req, res, next) {
var routes = [
'GET /',
'POST /tasks/:owner/:task',
'POST /tasks (for JSON body)',
'GET /tasks',
'PUT /tasks/:owner',
'GET /tasks/:owner',
'DELETE /tasks/:owner/:task'
];
res.send(200, routes);
next();
});
server.listen(serverPort, function() {
var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
consoleMessage += '\n %s server is listening at %s';
consoleMessage += '\n Open your browser to %s/tasks\n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';
});
```
## <a name="16-run-the-server"></a>16：运行服务器
添加身份验证之前最好是先测试服务器。

测试服务器的最简单方法是在命令提示符下使用 curl。 为此，需要使用一个用于分析 JSON 输出的简单实用工具。 

1.  安装要在以下示例中使用的 JSON 工具：

    `$npm install -g jsontool`

    这将全局安装 JSON 工具。

2.  确保 MongoDB 实例正在运行：

    `$sudo mongod`

3.  将目录切换到 **azuread**，然后运行 curl：

    `$ cd azuread`
    `$ node server.js`

    `$ curl -isS http://127.0.0.1:8080 | json`

    ```Shell
    HTTP/1.1 2.0OK
    Connection: close
    Content-Type: application/json
    Content-Length: 171
    Date: Tue, 14 Jul 2015 05:43:38 GMT
    [
    "GET /",
    "POST /tasks/:owner/:task",
    "POST /tasks (for JSON body)",
    "GET /tasks",
    "PUT /tasks/:owner",
    "GET /tasks/:owner",
    "DELETE /tasks/:owner/:task"
    ]
    ```

4.  添加任务：

    `$ curl -isS -X POST http://127.0.0.1:8888/tasks/brandon/Hello`

    响应应为：

    ```Shell
    HTTP/1.1 201 Created
    Connection: close
    Access-Control-Allow-Origin: *
    Access-Control-Allow-Headers: X-Requested-With
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 5
    Date: Tue, 04 Feb 2014 01:02:26 GMT
    Hello
    ```

5.  列出 Brandon 的任务：

    `$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

如果运行了所有这些命令且未出错，则可以将 OAuth 添加到 REST API 服务器。

*现在，已有一台装有 MongoDB 的 REST API 服务器！*

## <a name="17-add-authentication-to-your-rest-api-server"></a>17：将身份验证添加到 REST API 服务器
运行 REST API 后，请将它设置为与 Azure AD 配合使用。

在命令行中，将目录切换到 **azuread**：

`cd azuread`

### <a name="use-the-oidcbearerstrategy-thats-included-with-passport-azure-ad"></a>使用 passport-azure-ad 包含的 oidcbearerstrategy
到目前为止，我们已构建一个典型的 REST TODO 服务器，其中不包含任何授权种类。 现在，请添加身份验证。

首先，指出你要使用 Passport。 在前面的服务器配置后面紧接着插入以下代码：

```Javascript
// Start using Passport.js.

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support
```

> [!TIP]
> 编写 API 时，最好是始终将数据链接到用户无法证明其在令牌中是唯一的项目。 此服务器存储 TODO 项目时，会根据令牌（通过 token.sub 调用）中的用户订阅 ID 来存储它们。 token.sub 放在“owner”字段中。 这可以确保只有该用户能够访问其自己的 TODO（待办事项）。 任何人都无法访问输入的 TODO。 “owner”的 API 中不公开任何信息。 外部用户可以请求其他用户的 TODO，即使他们已经过身份验证。
> 
> 

接下来，使用 `passport-azure-ad` 中随附的 Open ID Connect Bearer 策略。 将此代码放在上面粘贴的内容后面：

```Javascript
/**
/*
/* Calling the OIDCBearerStrategy and managing users.
/*
/* Because of the Passport pattern, you need to manage users and info tokens
/* with a FindorCreate() method. The method must be provided by the implementor.
/* In the following code, you autoregister any user and implement a FindById().
/* It's a good idea to do something more advanced.
**/
var findById = function(id, fn) {
for (var i = 0, len = users.length; i < len; i++) {
var user = users[i];
if (user.sub === id) {
log.info('Found user: ', user);
return fn(null, user);
}
}
return fn(null, null);
};
var oidcStrategy = new OIDCBearerStrategy(options,
function(token, done) {
log.info('verifying the user');
log.info(token, 'was the token retrieved');
findById(token.sub, function(err, user) {
if (err) {
return done(err);
}
if (!user) {
// "Auto-registration"
log.info('User was added automatically, because they were new. Their sub is: ', token.sub);
users.push(token);
owner = token.sub;
return done(null, token);
}
owner = token.sub;
return done(null, user, token);
});
}
);
passport.use(oidcStrategy);
```

Passport 对其所有策略（Twitter、Facebook 等）都使用类似的模式。 所有策略编写器都遵循该模式。 向策略传递使用令牌和 `done` 作为参数的 `function()`。 策略会在完成所有任务后返回。 存储用户和令牌，这样一来，就不需要再次请求。

> [!IMPORTANT]
> 前面的代码会将所有可以进行身份验证的用户转到服务器。 这就是所谓的自动注册。 在生产服务器上，你会希望所有人都先经历指定的注册过程，然后才能进入服务器。 通常会在消费类应用中看到这种模式。 应用可能会允许使用 Facebook 注册，但随后会要求输入其他信息。 如果在本教程中没有使用命令行程序，可从返回的令牌对象中提取电子邮件。 然后，可以要求用户输入其他信息。 由于这是测试服务器，可以直接将用户添加到内存中数据库。
> 
> 

### <a name="protect-endpoints"></a>保护终结点
通过结合想要使用的协议指定 **passport.authenticate()** 调用来保护终结点。

可以在服务器代码中针对更高级的选项编辑路由：

```Javascript
server.get('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), listTasks);
server.get('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), listTasks);
server.get('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), getTask);
server.head('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), getTask);
server.post('/tasks/:owner/:task', passport.authenticate('oidc-bearer', {
session: false
}), createTask);
server.post('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), createTask);
server.del('/tasks/:owner/:task', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), removeAll, function respond(req, res, next) {
res.send(204);
next();
});
```

## <a name="18-run-your-server-application-again"></a>18：再次运行服务器应用程序
再次使用 curl 来检查是否针对终结点提供 OAuth 2.0 保护。 请在针对此终结点运行任何客户端 SDK 之前执行此操作。 返回的标头应告知身份验证是否正常工作。

1.  确保 MongoDB 实例正在运行：

    `$sudo mongod`

2.  切换到 **azuread** 目录，然后使用 curl：

    `$ cd azuread`

    `$ node server.js`

3.  试用基本 POST：

    `$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

    ```Shell
    HTTP/1.1 401 Unauthorized
    Connection: close
    WWW-Authenticate: Bearer realm="Users"
    Date: Tue, 14 Jul 2015 05:45:03 GMT
    Transfer-Encoding: chunked
    ```

401 响应表明 Passport 层正在尝试重定向到授权终结点，这正是我们所希望的。

*现已创建一个使用 OAuth 2.0 的 REST API 服务！*

这就是在不使用 OAuth 2.0 兼容客户端的情况下，此服务器能够带来的最大优势。 相关信息请参阅其他教程。

## <a name="next-steps"></a>后续步骤
某个 [.zip 文件](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs/archive/complete.zip)提供了完整的示例（不包括配置值）供参考。 也可以从 GitHub 中克隆该示例：

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs.git```

现在，可以继续学习更高级的主题。 请尝试学习[使用 v2.0 终结点保护 Node.js Web 应用](active-directory-v2-devquickstarts-node-web.md)

下面是其他一些资源：

* [Azure AD v2.0 开发人员指南](active-directory-appmodel-v2-overview.md)
* [Stack Overflow“azure-active-directory”标记](http://stackoverflow.com/questions/tagged/azure-active-directory)

### <a name="get-security-updates-for-our-products"></a>获取关于我们产品的安全更新
我们建议注册，以便在发生安全事件时获得通知。 在 [Microsoft 技术安全通知](https://technet.microsoft.com/security/dd252948)页上订阅安全顾问警报。


