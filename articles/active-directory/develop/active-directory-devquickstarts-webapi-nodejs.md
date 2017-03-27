---
title: "Azure AD Node.js 入门 | Microsoft 文档"
description: "如何生成一个与 Azure AD 集成、可用于身份验证的 Node.js REST Web API。"
services: active-directory
documentationcenter: nodejs
author: xerners
manager: mbaldwin
editor: 
ms.assetid: 7654ab4c-4489-4ea5-aba9-d7cdc256e42a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/07/2017
ms.author: brandwe
translationtype: Human Translation
ms.sourcegitcommit: 682f6d0919df20cd3643abf04e1d5192be116399
ms.openlocfilehash: f20c2c2c37692d439ca8e023cf9d5a48cd42c973
ms.lasthandoff: 02/24/2017


---
# <a name="get-started-with-web-apis-for-nodejs"></a>用于 Node.js 的 Web API 入门
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

*Passport* 是 Node.js 的身份验证中间件。 Passport 很灵活并且采用模块化结构，可以在不造成干扰的情况下放入任何基于 Express 的应用程序或 Resitify Web 应用程序。 一套综合性策略支持使用用户名和密码、Facebook、Twitter 等进行身份验证。 我们针对 Microsoft Azure Active Directory (Azure AD) 开发了一个策略。 我们将安装此模块，然后添加 Microsoft Azure Active Directory `passport-azure-ad` 插件。

若要实现此目的，需要：

1. 将一个应用程序注册到 Azure AD。
2. 将应用设置为使用 Passport 的 `passport-azure-ad` 插件。
3. 配置一个客户端应用程序用于调用“待办事项列表”Web API。

本教程的代码 [在 GitHub 上](https://github.com/Azure-Samples/active-directory-node-webapi)维护。

> [!NOTE]
> 本文不会介绍如何使用 Azure AD B2C 来实施登录、注册或配置文件管理， 而是着重介绍如何在用户已通过身份验证后调用 Web API。  我们建议先从[如何与 Azure Active Directory 集成文档](active-directory-how-to-integrate.md)入手，了解 Azure Active Directory 的基础知识。
>
>

我们已在 GitHub 中的 MIT 许可证下发布了此运行示例的所有源代码，你可以任意克隆（甚至分发）这些代码，并提供反馈和发出请求。

## <a name="about-nodejs-modules"></a>关于 Node.js 模块
本演练将使用 Node.js 模块。 模块是可加载的 JavaScript 包，可为你的应用程序提供特定功能。 我们通常使用 Node.js NPM 命令行工具在 NPM 安装目录中安装模块。 但有些模块（如 HTTP 模块）是作为核心 Node.js 包的一部分提供的。

安装的模块保存在 Node.js 安装目录的根目录下的 **node_modules** 目录中。 **node_modules** 目录中的每个模块都保留自身的 **node_modules** 目录，其中包含它依赖的所有模块。 另外，每个必需的模块都有一个 **node_modules** 目录 这种递归的目录结构表示了依赖关系链。

这种依赖关系链结构会导致应用程序占用空间变大。 但是，它也可以保证满足所有依赖项的要求，并且在开发环境中使用的模块版本也能在生产环境中使用。 这使得生产应用程序的行为更有预测性，并防止出现影响用户的版本控制问题。

## <a name="step-1-register-an-azure-ad-tenant"></a>步骤 1：注册 Azure AD 租户
若要使用本示例，需要一个 Azure Active Directory 租户。 如果不确定什么是租户或者如何获取租户，请参阅[如何获取 Azure AD 租户](active-directory-howto-tenant.md)。

## <a name="step-2-create-an-application"></a>步骤 2：创建应用程序
接下来，需要在目录中创建一个应用，以便为 Azure AD 提供必要的信息，使它能够与应用安全通信。  在本例中，由于客户端应用与 Web API 构成一个逻辑应用，因此由单个**应用程序 ID** 表示。  若要创建应用，请遵循 [这些说明](active-directory-how-applications-are-added.md)。 若要生成业务线应用，[这些附加说明可能很有用](../active-directory-applications-guiding-developers-for-lob-applications.md)。

创建应用程序：

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 在顶部菜单中选择你的帐户。 然后，在“目录”列表下选择要注册应用程序的 Active Directory 租户。

3. 在左侧的菜单中选择“更多服务”，然后选择“Azure Active Directory”。

4. 选择“应用注册”，然后选择“添加”。

5. 根据提示创建一个 **Web 应用程序和/或 WebAPI**。

      * 应用程序的**名称**向最终用户描述该应用程序。

      * “登录 URL”是应用的基本 URL。  示例代码的默认 URL 为 `https://localhost:8080`。

      * “应用程序 ID URI”是应用程序的唯一标识符。 约定是使用 `https://<tenant-domain>/<app-name>`，例如：`https://contoso.onmicrosoft.com/my-first-aad-app`。

6. 注册后，Azure AD 会为应用分配唯一的应用程序 ID。 在后面的部分中将会用到此值，因此，请从应用程序页复制此值。

7. 通过“设置”页为应用程序创建**密钥**，然后将该密钥复制到某个位置， 稍后需要用到它。

## <a name="step-3-download-nodejs-for-your-platform"></a>步骤 3：下载平台的 Node.js
若要成功使用本示例，你必须正确安装 Node.js。

从 [http://nodejs.org](http://nodejs.org) 安装 Node.js。

## <a name="step-4-install-mongodb-on-your-platform"></a>步骤 4：在平台上安装 MongoDB
若要成功使用本示例，你必须正确安装 MongoDB。 使用 MongoDB 将 REST API 持久保留在服务器实例之间。

从 [http://mongodb.org](http://www.mongodb.org) 安装 MongoDB。

> [!NOTE]
> 本演练假设要为 MongoDB 使用默认的安装与服务器终结点，在编写本文时，该终结点为：mongodb://localhost。
>
>

## <a name="step-5-install-the-restify-modules-in-your-web-api"></a>步骤 5：在 Web API 中安装 Restify 模块
我们将使用 Resitfy 来生成 REST API。 Restify 是派生自 Express 的精简弹性 Node.js 应用程序框架。 它提供一套稳健功能用于在 Connect 的顶层构建 REST API。

### <a name="install-restify"></a>安装 Restify
1. 在命令行中，将目录切换到 **azuread** 目录。 如果 **azuread** 目录不存在，请创建该目录。

        `cd azuread - or- mkdir azuread; cd azuread`

2. 输入以下命令：

    `npm install restify`

    此命令将安装 Restify。

#### <a name="did-you-get-an-error"></a>遇到了错误吗？
在某些操作系统上使用 NPM 时，可能会出现“错误: EPERM，chmod '/usr/local/bin/..'” 并建议尝试以管理员身份运行帐户。 如果发生这种情况，请使用 sudo 命令以更高的特权级别运行 NPM。

#### <a name="did-you-get-an-error-regarding-dtrace"></a>遇到了有关 DTRACE 的错误吗？
安装 Restify 时，可能会出现类似于下面的错误：

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
Restify 提供强大的机制来使用 DTrace 跟踪 REST 调用。 但是，许多操作系统中没有 DTrace。 你可以安全地忽略这些错误。

此命令的输出应如下所示：

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


## <a name="step-6-install-passportjs-in-your-web-api"></a>步骤 6：将在 Passport.js 安装到 Web API
[Passport](http://passportjs.org/) 是 Node.js 的身份验证中间件。 Passport 很灵活并且采用模块化结构，可以在不造成干扰的情况下放入任何基于 Express 的应用程序或 Resitify Web 应用程序。 一套综合性策略支持使用用户名和密码、Facebook、Twitter 等进行身份验证。

我们针对 Azure Active directory 开发了一个策略。 我们将安装此模块，然后添加 Azure Active Directory 策略插件。

1. 在命令行中，将目录切换到 **azuread** 目录。

2. 若要安装 passport.js，请输入以下命令：

    `npm install passport`

    该命令的输出应如下所示：

``
        passport@0.1.17 node_modules\passport
        ├── pause@0.0.1
        └── pkginfo@0.2.3
``

## <a name="step-7-add-passport-azure-ad-to-your-web-api"></a>步骤 7：将 Passport-Azure-AD 添加到 Web API
接下来，使用 `passport-azure-ad` 添加 OAuth 策略，这是一套将 Azure Active Directory 连接到 Passport 的策略。 在此 REST API 示例中，我们将针对持有者令牌使用此策略。

> [!NOTE]
> 尽管 OAuth2 提供了可以颁发任何已知令牌类型的框架，但我们通常只使用其中的某些令牌类型。 持有者令牌是最常用于保护终结点的令牌。 这些令牌是 OAuth2 中最广泛颁发的令牌类型。 许多实现假设持有者令牌是唯一颁发的令牌类型。
>
>

在命令行中，将目录切换到 **azuread** 目录。

键入以下命令安装 Passport.js `passport-azure-ad module`：

`npm install passport-azure-ad`

该命令的输出应如下所示：


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



## <a name="step-8-add-mongodb-modules-to-your-web-api"></a>步骤 8：将 MongoDB 模块添加到 Web API
我们使用 MongoDB 作为数据存储。 为此，需要安装一个广泛使用的、名为 Mongoose 的插件来管理模型和架构。 另外，还需要安装 MongoDB 的数据库驱动程序（也称为 MongoDB）。

 `npm install mongoose`

## <a name="step-9-install-additional-modules"></a>步骤 9：安装其他模块
接下来，安装剩余的所需模块。

1. 在命令行中，将目录切换到 **azuread** 文件夹（如果尚未进入）。

    `cd azuread`

2. 输入以下命令，在 **node_modules** 目录中安装以下模块：

    * `npm install assert-plus`
    * `npm install bunyan`
    * `npm update`

## <a name="step-10-create-a-serverjs-with-your-dependencies"></a>步骤 10：创建包含依赖项的 server.js
server.js 文件提供 Web API 服务器的大多数功能。 我们要将大部分代码添加到此文件中。 对于生产目的，我们建议将功能重构为较小的文件，例如单独的路由和控制器。 本演示为此功能使用 server.js。

1. 在命令行中，将目录切换到 **azuread** 文件夹（如果尚未进入）。

    `cd azuread`

2. 在偏好的编辑器中创建 `server.js` 文件，然后添加以下信息：

    ```Javascript
        'use strict';

        /**
         * Module dependencies.
         */

        var fs = require('fs');
        var path = require('path');
        var util = require('util');
        var assert = require('assert-plus');
        var bunyan = require('bunyan');
        var getopt = require('posix-getopt');
        var mongoose = require('mongoose/');
        var restify = require('restify');
        var passport = require('passport');
      var BearerStrategy = require('passport-azure-ad').BearerStrategy;
    ```

3. 保存文件。 稍后我们将要使用该文件。

## <a name="step-11-create-a-config-file-to-store-your-azure-ad-settings"></a>步骤 11：创建一个配置文件用于存储 Azure AD 设置
此代码文件会将配置参数从 Azure Active Directory 门户传递到 Passport.js。 在本演练的第一部分中向门户添加 Web API 时，已经创建了这些配置值。 我们将解释在复制代码后，要输入其中的哪些参数值。

1. 在命令行中，将目录切换到 **azuread** 文件夹（如果尚未进入）。

    `cd azuread`

2. 在偏好的编辑器中创建 `config.js` 文件，然后添加以下信息：

    ```Javascript
         exports.creds = {
             mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
             clientID: 'your client ID',
             audience: 'your application URL',
            // you cannot have users from multiple tenants sign in to your server unless you use the common endpoint
          // example: https://login.microsoftonline.com/common/.well-known/openid-configuration
             identityMetadata: 'https://login.microsoftonline.com/<your tenant id>/.well-known/openid-configuration',
             validateIssuer: true, // if you have validation on, you cannot have users from multiple tenants sign in to your server
             passReqToCallback: false,
             loggingLevel: 'info' // valid are 'info', 'warn', 'error'. Error always goes to stderr in Unix.

         };
    ```
3. 保存文件。

## <a name="step-12-add-configuration-values-to-your-serverjs-file"></a>步骤 12：将配置值添加到 server.js 文件
我们需要在整个应用程序中从创建的 .config 文件读取这些值。 为此，请在应用程序中添加 .config 文件作为所需的资源。 然后将全局变量设置为与 config.js 文档中的变量匹配。

1. 在命令行中，将目录切换到 **azuread** 文件夹（如果尚未进入）。

    `cd azuread`

2. 在偏好的编辑器中打开 `server.js` 文件，然后添加以下信息：

    ```Javascript
    var config = require('./config');
    ```
3. 然后，在 `server.js` 中替换包含以下代码的新节：

    ```Javascript
    var options = {
        // The URL of the metadata document for your app. We will put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
        identityMetadata: config.creds.identityMetadata,
        clientID: config.creds.clientID,
        validateIssuer: config.creds.validateIssuer,
        audience: config.creds.audience,
        passReqToCallback: config.creds.passReqToCallback,
        loggingLevel: config.creds.loggingLevel

    };

    // Array to hold logged in users and the current logged in user (owner).
    var users = [];
    var owner = null;

    // Our logger.
    var log = bunyan.createLogger({
        name: 'Azure Active Directory Bearer Sample',
             streams: [
            {
                stream: process.stderr,
                level: "error",
                name: "error"
            },
            {
                stream: process.stdout,
                level: "warn",
                name: "console"
            }, ]
    });

      // If the logging level is specified, switch to it.
      if (config.creds.loggingLevel) { log.levels("console", config.creds.loggingLevel); }

    // MongoDB setup.
    // Set up some configuration.
    var serverPort = process.env.PORT || 8080;
    var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
    ```

4. 保存文件。

## <a name="step-13-add-the-mongodb-model-and-schema-information-by-using-mongoose"></a>步骤 13：使用 Mongoose 添加 MongoDB 模型和架构信息
现在，我们已将这三个文件合并在一个 REST API 服务中，接下来让我们的准备工作发挥作用。

本演练使用 MongoDB 来存储步骤 4 中所述的任务。

我们在步骤 11 中创建了 `config.js` 文件，并将数据库命名为 `tasklist`，因为这是在 **mogoose_auth_local** 连接 URL 的末尾放置的内容。 不需要在 MongoDB 中事先创建此数据库。 首次运行服务器应用程序时，MongoDB 将会自动创建此数据库（假设该数据库尚不存在）。

现在，我们已告诉服务器要使用哪个 MongoDB 数据库，接下来需要编写一些附加的代码，为服务器任务创建模型和架构。

### <a name="discussion-of-the-model"></a>模型介绍
架构模型非常简单。 可以根据需要来扩展它。

NAME：分配到任务的人员的姓名。 一个**字符串**。

TASK：任务本身。 一个**字符串**。

DATE：任务截止日期。 一个**日期时间**。

COMPLETED：任务是否已完成。 一个**布尔值**。

### <a name="creating-the-schema-in-the-code"></a>在代码中创建架构
1. 在命令行中，将目录切换到 **azuread** 文件夹（如果尚未进入）。

    `cd azuread`

2. 在偏好的编辑器中打开 `server.js` 文件，然后在配置条目下面添加以下信息：

    ```Javascript
    // Connect to MongoDB.
    global.db = mongoose.connect(serverURI);
    var Schema = mongoose.Schema;
    log.info('MongoDB Schema loaded');

    // Here we create a schema to store our tasks and users. It's a fairly simple schema for now.
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
从该代码中可以看到，首先会创建架构。 然后创建在定义**路由**时，用于存储整个代码中的数据的模型对象

## <a name="step-14-add-our-routes-for-our-task-rest-api-server"></a>步骤 14：为任务 REST API 服务器添加路由
现已创建一个可用的数据库模型，接下来添加用于 REST API 服务器的路由。

### <a name="about-routes-in-restify"></a>关于 Restify 中的路由
Restify 中路由的工作原理与 Express 堆栈中的路由相同。 可以使用客户端应用程序应该调用的 URI 定义路由。 通常，需要在单独的文件中定义路由。 本演练将在 server.js 文件中定义路由。 对于生产用途，建议在各自的文件中定义路由。

Restify 路由的典型模式如下：

```Javascript
function createObject(req, res, next) {

// Do work on object.

 _object.name = req.params.object; // passed value is in req.params under object

 ///...

return next(); // Keep the server going.
}

....

server.post('/service/:add/:object', createObject); // Calls createObject on routes that match this.

```


这是最基本级别的模式。 Resitfy（以及 Express）提供更深层的功能，例如，定义应用程序类型，以及跨不同的终结点提供复杂路由。 本演练将使这些路由保持精简。

### <a name="add-default-routes-to-our-server"></a>将默认路由添加到服务器
现在，添加 Create、Retrieve、Update 和 Delete 的基本 CRUD 路由。

1. 在命令行中，将目录切换到 **azuread** 文件夹（如果尚未进入）：

    `cd azuread`

2. 在偏好的编辑器中打开 `server.js` 文件，然后在前面创建的数据库条目下面添加以下信息：

```Javascript

/**
 *
 * APIs for our REST Task server.
 */

// Create a task.

function createTask(req, res, next) {

    // Restify currently has a bug which doesn't allow you to set default headers.
    // These headers comply with CORS and allow us to mongodbServer our response to any origin.

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    // Create a new task model, fill it, and save it to Mongodb.
    var _task = new Task();

    if (!req.params.task) {
        req.log.warn('createTodo: missing task');
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

/// Simple returns the list of TODOs that were loaded.

function listTasks(req, res, next) {
    // Restify currently has a bug which doesn't allow you to set default headers.
    // These headers comply with CORS and allow us to mongodbServer our response to any origin.

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    log.info("listTasks was called for: ", owner);

    Task.find({
        owner: owner
    }).limit(20).sort('date').exec(function(err, data) {

        if (err) {
            return next(err);
        }

        if (data.length > 0) {
            log.info(data);
        }

        if (!data.length) {
            log.warn(err, "There is no tasks in the database. Did you initialize the database as stated in the README?");
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

### <a name="add-error-handling-in-our-apis"></a>在 API 中添加错误处理
```

///--- Errors for communicating something interesting back to the client.

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


## <a name="step-15-create-your-server"></a>步骤 15：创建服务器
我们已定义了数据库，并且准备好了路由。 最后一件事就是添加用于管理调用的服务器实例。

在 Restify（以及 Express）中，可以针对 REST API 服务器执行大量的自定义，但同样，本演练将使用最基本的设置。

```Javascript
/**
 * Our server.
 */


var server = restify.createServer({
    name: "Azure Active Directroy TODO Server",
    version: "2.0.1"
});

// Ensure we don't drop data on uploads.
server.pre(restify.pre.pause());

// Clean up sloppy paths like //todo//////1//.
server.pre(restify.pre.sanitizePath());

// Handle annoying user agents (curl).
server.pre(restify.pre.userAgentConnection());

// Set a per request bunyan logger (with requestid filled in).
server.use(restify.requestLogger());

// Allow five requests per second by IP, and burst to 10.
server.use(restify.throttle({
    burst: 10,
    rate: 5,
    ip: true,
}));

// Use the common stuff you probably want.
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
    mapParams: true
})); // Allow for JSON mapping to REST.
```

## <a name="step-16-add-the-routes-to-the-server-without-authentication-for-now"></a>步骤 16：将路由添加到服务器（目前不包括身份验证）
```Javascript
/// Now the real handlers. Here we just CRUD.
/**
/*
/* Each of these handlers is protected by our OIDCBearerStrategy by invoking 'oidc-bearer'.
/* In the pasport.authenticate() method. We set 'session: false' because REST is stateless and
/* we don't need to maintain session state. You can experiment with removing API protection
/* by removing the passport.authenticate() method as follows:
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
// Register a default '/' handler.
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

## <a name="step-17-run-the-server-before-adding-oauth-support"></a>步骤 17：运行服务器（在添加 OAuth 支持之前）
添加身份验证之前，请先测试服务器。

测试服务器的最简单方法是在命令行中使用 curl。 在这样做之前，我们需要一个用于分析 JSON 输出的实用工具。

1. 安装以下 JSON 工具（后面的所有示例将使用此工具）：

    `$npm install -g jsontool`

    这将全局安装 JSON 工具。 现已安装了工具，接下来让我们试运行服务器：

2. 首先，确保 mongoDB 实例正在运行：

    `$sudo mongod`

3. 然后，切换到目录并开始运行：

    `$ cd azuread`
    `$ node server.js`

    `$ curl -isS http://127.0.0.1:8080 | json`

    ```Shell
    HTTP/1.1 200 OK
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

4. 然后，我们按如下所示添加一个任务：

    `$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

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
    我们可以按如下所示列出 Brandon 的任务：

        `$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

如果一切正常，便可以将 OAuth 添加到 REST API 服务器。

你已有一台装有 MongoDB 的 REST API 服务器！

## <a name="step-18-add-authentication-to-our-rest-api-server"></a>步骤 18：将身份验证添加到 REST API 服务器
既然我们运行了 REST API，接下来让它在 Azure AD 中发挥作用。

在命令行中，将目录切换到 **azuread** 文件夹（如果尚未进入）。

`cd azuread`

### <a name="use-the-oidcbearerstrategy-that-is-included-with-passport-azure-ad"></a>使用 passport-azure-ad 随附的 OIDCBearerStrategy
到目前为止，我们已构建一个典型的 REST TODO 服务器，其中不包含任何授权种类。 这是我们将其结合在一起的起点。

1. 首先，需指出要使用 Passport。 在其他服务器配置之后紧接着执行此操作：

    ```Javascript
            // Let's start using Passport.js.

            server.use(passport.initialize()); // Starts passport.
            server.use(passport.session()); // Provides session support.
    ```
    > [!TIP]
    > 编写 API 时，我们建议始终将数据链接到用户无法证明其在令牌中是唯一的项目。 当此服务器存储 TODO 项目时，会根据我们放在“所有者”字段的令牌（通过 token.oid 调用）中的用户对象 ID 来存储。 这可以确保只有该用户能够访问其自己的 TODO（待办事项）。 “owner”的 API 中不公开任何信息，因此，外部用户可以请求其他人的 TODO，即使他们已经过身份验证。                    

2. 接下来，使用 `passport-azure-ad` 随附的持有者策略。 先看看下面的代码，稍后我们将予以解释。 将此代码放在上面粘贴的内容后面：

```Javascript
    /**
    /*
    /* Calling the OIDCBearerStrategy and managing users.
    /*
    /* Passport pattern provides the need to manage users and info tokens
    /* with a FindorCreate() method that must be provided by the implementor.
    /* Here we just auto-register any user and implement a FindById().
    /* You'll want to do something smarter.
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


    var bearerStrategy = new BearerStrategy(options,
        function(token, done) {
            log.info('verifying the user');
            log.info(token, 'was the token retreived');
            findById(token.sub, function(err, user) {
                if (err) {
                    return done(err);
                }
                if (!user) {
                    // "Auto-registration"
                    log.info('User was added automatically as they were new. Their sub is: ', token.sub);
                    users.push(token);
                    owner = token.sub;
                    return done(null, token);
                }
                owner = token.sub;
                return done(null, user, token);
            });
        }
    );

    passport.use(bearerStrategy);
```

Passport 使用适用于它的所有策略（Twitter、Facebook 等），所有策略写入器都依循类似的模式。 查看该策略可以发现，我们已将它作为函数来传递，其中包含一个令牌和一个用作参数的 done。 策略完成其工作后将返回。 完成后，需要存储用户并隐藏令牌，以免再次请求它。

> [!IMPORTANT]
> 上述代码使用了正好地服务器上进行身份验证的任何用户。 这就是所谓的自动注册。 在生产服务器中，建议要求所有人都必须先经历你所确定的注册过程，然后才能进入服务器。 这通常是在使用者应用中看到的模式，可让向 Facebook 注册，但接着请求填写其他信息。 如果这不是命令行程序，我们就只能从返回的令牌对象中提取电子邮件，然后请求用户填写其他信息。 由于这是测试服务器，因此，我们直接将它们加入到内存中的数据库。
>
>

### <a name="protect-some-endpoints"></a>保护某些终结点
通过结合要使用的协议指定 `passport.authenticate()` 调用来保护终结点。

为了让服务器代码产生一些有趣的结果，我们先编辑路由。

```Javascript
server.get('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), listTasks);
server.get('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), listTasks);
server.get('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), getTask);
server.head('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), getTask);
server.post('/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
session: false
}), createTask);
server.post('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), createTask);
server.del('/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), removeAll, function respond(req, res, next) {
res.send(204);
next();
});
```

## <a name="step-19-run-your-server-application-again-and-ensure-it-rejects-you"></a>步骤 19：再次运行服务器应用程序并确保它拒绝你
再次使用 `curl` 来查看是否针对终结点提供了 OAuth2 保护。 应该在针对此终结点运行任何客户端 SDK 之前执行此操作。 返回的标头足以说明一切正常进行。

1. 首先，确保 mongoDB 实例正在运行：

    `$sudo mongod`

2. 然后，切换到目录并开始运行。

      `$ cd azuread`
      `$ node server.js`

3. 试用基本 POST。

    `$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

    ```Shell
    HTTP/1.1 401 Unauthorized
    Connection: close
    WWW-Authenticate: Bearer realm="Users"
    Date: Tue, 14 Jul 2015 05:45:03 GMT
    Transfer-Encoding: chunked
    ```

401 在这里是正常的响应。 此响应表明 Passport 层正在尝试重定向到授权终结点，这正是我们所希望的。

## <a name="next-steps"></a>后续步骤
这就是在不使用 OAuth2 兼容客户端的情况下，此服务器能够带来的最大优势。 接下来请学习其他演练。

我们已了解如何使用 Restify 和 OAuth2 实现 REST API。 本示例中的代码足以帮助你学习开发和生成服务。

如果对 ADAL 学习历程中的后续步骤感兴趣，我们建议了解下面这些支持的 ADAL 客户端。

请将这些代码克隆到开发人员计算机，然后根据演练中所述进行配置。

[iOS 版 ADAL](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios)

[Android 版 ADAL](https://github.com/MSOpenTech/azure-activedirectory-library-for-android)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]

