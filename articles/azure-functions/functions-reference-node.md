---
title: JavaScript developer reference for Azure Functions
description: 了解如何使用 JavaScript 开发函数。
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.topic: reference
ms.date: 02/24/2019
ms.openlocfilehash: b6b7db4c5f13a264b76dcab02dba51c464297307
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226710"
---
# <a name="azure-functions-javascript-developer-guide"></a>Azure Functions JavaScript 开发人员指南

本指南包含有关使用 JavaScript 编写 Azure Functions 的复杂性的信息。

JavaScript 函数是导出的 `function`，它将在触发时执行（[触发器在 function.json 中配置](functions-triggers-bindings.md)）。 The first argument passed to every function is a `context` object, which is used for receiving and sending binding data, logging, and communicating with the runtime.

本文假定你已阅读 [Azure Functions 开发人员参考](functions-reference.md)。 Complete the Functions quickstart to create your first function, using [Visual Studio Code](functions-create-first-function-vs-code.md) or [in the portal](functions-create-first-azure-function.md).

This article also supports [TypeScript app development](#typescript).

## <a name="folder-structure"></a>文件夹结构

JavaScript 项目所需的文件夹结构如下所示。 可更改此默认值。 有关详细信息，请参阅下面的 [scriptFile](#using-scriptfile) 部分。

```
FunctionsProject
 | - MyFirstFunction
 | | - index.js
 | | - function.json
 | - MySecondFunction
 | | - index.js
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.js
 | | - mySecondHelperFunction.js
 | - node_modules
 | - host.json
 | - package.json
 | - extensions.csproj
```

项目的根目录中有共享的 [host.json](functions-host-json.md) 文件，可用于配置函数应用。 每个函数都具有一个文件夹，其中包含其代码文件 (.js) 和绑定配置文件 (function.json)。 `function.json` 父目录的名称始终是函数的名称。

[2.x 版](functions-versions.md) Functions 运行时中所需的绑定扩展在 `extensions.csproj` 文件中定义，实际库文件位于 `bin` 文件夹中。 本地开发时，必须[注册绑定扩展](./functions-bindings-register.md#extension-bundles)。 在 Azure 门户中开发函数时，系统将为你完成此注册。

## <a name="exporting-a-function"></a>导出函数

必须通过 [`module.exports`](https://nodejs.org/api/modules.html#modules_module_exports)（或 [`exports`](https://nodejs.org/api/modules.html#modules_exports)）导出 JavaScript 函数。 导出的函数应是触发时执行的 JavaScript 函数。

默认情况下，Functions 运行时会在 `index.js` 中查找你的函数，其中，`index.js` 与其相应的 `function.json` 共享同一个父目录。 默认情况下，导出的函数应该是其文件中的唯一导出，或者名为 `run` 或 `index` 的导出。 若要配置文件位置和导出函数名称，请阅读下面的[配置函数的入口点](functions-reference-node.md#configure-function-entry-point)。

在执行时，将为导出的函数传递一些参数。 采用的第一个参数始终是 `context` 对象。 If your function is synchronous (doesn't return a Promise), you must pass the `context` object, as calling `context.done` is required for correct use.

```javascript
// You should include context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
```

### <a name="exporting-an-async-function"></a>导出异步函数
在 Functions 运行时版本 2.x 中使用 [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) 声明或普通 JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)，无需显式调用 [`context.done`](#contextdone-method) 回调即可通知函数已完成。 导出的异步函数/Promise 完成时，函数将完成。 对于面向版本 1.x 运行时的函数，在代码完成执行后，仍必须调用 [`context.done`](#contextdone-method)。

以下示例是一个简单的函数，用于记录其已被触发并立即完成执行。

```javascript
module.exports = async function (context) {
    context.log('JavaScript trigger function processed a request.');
};
```

导出异步函数时，还可配置输出绑定，以使用 `return` 值。 如果只有一个输出绑定，则建议使用此值。

若要使用 `return` 分配输出，请将 `name` 属性更改为 `function.json` 中的 `$return`。

```json
{
  "type": "http",
  "direction": "out",
  "name": "$return"
}
```

在这种情况下，函数应如以下示例所示：

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    // You can call and await an async method here
    return {
        body: "Hello, world!"
    };
}
```

## <a name="bindings"></a>绑定 
在 JavaScript 中，需在函数的 function.json 中配置和定义[绑定](functions-triggers-bindings.md)。 函数通过多种方式来与绑定交互。

### <a name="inputs"></a>输入
在 Azure Functions 中，输入分为两种类别：一种是触发器输入，另一种则是附加输入。 函数可通过三种方式读取触发器和其他输入绑定（`direction === "in"` 的绑定）：
 - **_[建议]_ 以传递给函数的参数的形式。** 它们以与 function.json 中定义的顺序相同的顺序传递给函数。 The `name` property defined in *function.json* does not need to match the name of your parameter, although it should.
 
   ```javascript
   module.exports = async function(context, myTrigger, myInput, myOtherInput) { ... };
   ```
   
 - **以 [`context.bindings`](#contextbindings-property) 对象的成员的形式。** 每个成员由 *function.json* 中定义的 `name` 属性命名。
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + context.bindings.myTrigger);
       context.log("This is myInput: " + context.bindings.myInput);
       context.log("This is myOtherInput: " + context.bindings.myOtherInput);
   };
   ```
   
 - **使用 JavaScript [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx) 对象以输入的形式。** 这实质上与作为参数传递输入相同，但可以动态处理输入。
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + arguments[1]);
       context.log("This is myInput: " + arguments[2]);
       context.log("This is myOtherInput: " + arguments[3]);
   };
   ```

### <a name="outputs"></a>Outputs
函数可通过多种方式写入输出（`direction === "out"` 的绑定）。 在所有情况下，*function.json* 中定义的绑定属性 `name` 对应于函数中所写入到的对象成员的名称。 

You can assign data to output bindings in one of the following ways (don't combine these methods):

- **_[有多个输出时建议使用]_ 返回对象。** If you are using an async/Promise returning function, you can return an object with assigned output data. 在以下示例中，*function.json* 中的输出绑定名为“httpResponse”和“queueOutput”。

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      return {
          httpResponse: {
              body: retMsg
          },
          queueOutput: retMsg
      };
  };
  ```

  如果使用同步函数，可以使用 [`context.done`](#contextdone-method) 返回此对象（请参阅示例）。
- **_[有单个输出时建议使用]_ 直接返回值，并使用 $return 绑定名称。** 这仅适用于异步函数/返回 Promise 的函数。 请参阅[导出异步函数](#exporting-an-async-function)中的示例。 
- **向 `context.bindings` 赋值** 可以直接向 context.bindings 赋值。

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      context.bindings.httpResponse = {
          body: retMsg
      };
      context.bindings.queueOutput = retMsg;
      return;
  };
  ```

### <a name="bindings-data-type"></a>绑定数据类型

若要定义输入绑定的数据类型，请使用绑定定义中的 `dataType` 属性。 例如，若要以二进制格式读取 HTTP 请求的内容，请使用类型 `binary`：

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

`dataType` 的选项包括：`binary`、`stream` 和 `string`。

## <a name="context-object"></a>上下文对象
运行时使用 `context` 对象将数据传入和传出函数，并能与其进行通信。 上下文对象可用于从绑定读取和设置数据、写入日志，以及当导出的函数是同步函数时使用 `context.done` 回调。

`context` 对象始终是传递给函数的第一个参数。 之所以需要包含此对象，是因为它包含 `context.done` 和 `context.log` 等重要方法。 可以按个人喜好为对象命名（例如 `ctx` 或 `c`）。

```javascript
// You must include a context, but other arguments are optional
module.exports = function(ctx) {
    // function logic goes here :)
    ctx.done();
};
```

### <a name="contextbindings-property"></a>context.bindings 属性

```js
context.bindings
```

Returns a named object that is used to read or assign binding data. Input and trigger binding data can be accessed by reading properties on `context.bindings`. Output binding data can be assigned by adding data to `context.bindings`

例如，function.json 中的以下绑定定义允许通过 `context.bindings.myInput` 访问队列的内容和使用 `context.bindings.myOutput` 将输出分配给队列。

```json
{
    "type":"queue",
    "direction":"in",
    "name":"myInput"
    ...
},
{
    "type":"queue",
    "direction":"out",
    "name":"myOutput"
    ...
}
```

```javascript
// myInput contains the input data, which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

可以选择使用 `context.done` 方法而不是 `context.binding` 对象来定义输出绑定数据（参阅下文）。

### <a name="contextbindingdata-property"></a>context.bindingData 属性

```js
context.bindingData
```

返回包含触发器元数据和函数调用数据（`invocationId`、`sys.methodName`、`sys.utcNow`、`sys.randGuid`）的命名对象。 有关触发器元数据的示例，请参阅此[事件中心示例](functions-bindings-event-hubs.md#trigger---javascript-example)。

### <a name="contextdone-method"></a>context.done 方法

```js
context.done([err],[propertyBag])
```

让运行时知道代码已完成。 如果函数使用 [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) 声明，则你不需要使用 `context.done()`。 `context.done` 回调是隐式调用的。 异步函数在 Node 8 或更高版本（需要 Functions 运行时版本 2.x）中可用。

如果函数不是异步函数，则必须调用 `context.done` 来告知运行时函数是完整的。 如果缺少它，则执行将会超时。

`context.done` 方法允许将用户定义的错误传递回运行时以及包含输出绑定数据的 JSON 对象。 传递给 `context.done` 的属性将覆盖 `context.bindings` 对象上设置的任何内容。

```javascript
// Even though we set myOutput to have:
//  -> text: 'hello world', number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method overwrites the myOutput binding to be: 
//  -> text: 'hello there, world', noNumber: true
```

### <a name="contextlog-method"></a>context.log 方法  

```js
context.log(message)
```

允许在默认跟踪级别向流式处理函数日志进行写入。 `context.log` 中还提供了其他的日志记录方法，用以允许在其他跟踪级别写入函数日志：


| 方法                 | 描述                                |
| ---------------------- | ------------------------------------------ |
| **error(_message_)**   | 向错误级日志记录或更低级别进行写入。   |
| **warn(_message_)**    | 向警告级日志记录或更低级别进行写入。 |
| **info(_message_)**    | 向信息级日志记录或更低级别进行写入。    |
| **verbose(_message_)** | 向详细级日志记录进行写入。           |

以下示例在警告跟踪级别写入日志：

```javascript
context.log.warn("Something has happened."); 
```

可以在 host.json 文件中[为日志记录配置跟踪级别阈值](#configure-the-trace-level-for-console-logging)。 有关写入日志的详细信息，请参阅下面的[写入跟踪输出](#writing-trace-output-to-the-console)。

若要了解有关查看和查询函数日志的详细信息，请阅读[监视 Azure Functions](functions-monitoring.md)。

## <a name="writing-trace-output-to-the-console"></a>将跟踪输出写入到控制台 

在 Functions 中，可以使用 `context.log` 方法将跟踪输出写入到控制台。 在 Functions v2.x 中，使用 `console.log` 的跟踪输出在函数应用级别捕获。 This means that outputs from `console.log` are not tied to a specific function invocation and aren't displayed in a specific function's logs. 但是，它们将传播到 Application Insights。 在 Functions v1.x 中，不能使用 `console.log` 向控制台进行写入。

调用 `context.log()` 时，消息会在默认跟踪级别（即_信息_跟踪级别）写入到控制台。 以下代码在信息跟踪级别向控制台进行写入：

```javascript
context.log({hello: 'world'});  
```

此代码等同于上面的代码：

```javascript
context.log.info({hello: 'world'});  
```

此代码在错误级别向控制台进行写入：

```javascript
context.log.error("An error has occurred.");  
```

因为_错误_是最高跟踪级别，所以，只要启用了日志记录，此跟踪会在所有跟踪级别写入到输出中。

所有 `context.log` 方法都支持 Node.js [util.format 方法](https://nodejs.org/api/util.html#util_util_format_format)支持的同一参数格式。 请考虑以下代码，它使用默认跟踪级别写入函数日志：

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

还可以采用以下格式编写同一代码：

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

### <a name="configure-the-trace-level-for-console-logging"></a>为控制台日志记录配置跟踪级别

Functions 1.x 允许定义向控制台进行写入时使用的阈值跟踪级别，这使得可以轻松控制从函数向控制台写入跟踪的方式。 若要针对写入到控制台的所有跟踪设置阈值，请在 host.json 文件中使用 `tracing.consoleLevel` 属性。 此设置应用于 Function App 中的所有函数。 以下示例设置跟踪阈值来启用详细日志记录：

```json
{
    "tracing": {
        "consoleLevel": "verbose"
    }
}  
```

**consoleLevel** 的值对应于 `context.log` 方法的名称。 要为控制台禁用所有跟踪日志记录，请将 **consoleLevel** 设置为 _off_。 有关详细信息，请参阅 [host.json 参考](functions-host-json-v1.md)。

## <a name="http-triggers-and-bindings"></a>HTTP 触发器和绑定

HTTP 和 webhook 触发器以及 HTTP 输出绑定使用请求和响应对象来表示 HTTP 消息。  

### <a name="request-object"></a>请求对象

`context.req`（请求）对象具有以下属性：

| properties      | 描述                                                    |
| ------------- | -------------------------------------------------------------- |
| _body_        | 一个包含请求正文的对象。               |
| _headers_     | 一个包含请求标头的对象。                   |
| _method_      | 请求的 HTTP 方法。                                |
| _originalUrl_ | 请求的 URL。                                        |
| _params_      | 一个包含请求的路由参数的对象。 |
| _query_       | 一个包含查询参数的对象。                  |
| _rawBody_     | 字符串形式的消息正文。                           |


### <a name="response-object"></a>响应对象

`context.res`（响应）对象具有以下属性：

| properties  | 描述                                               |
| --------- | --------------------------------------------------------- |
| _body_    | 一个包含响应正文的对象。         |
| _headers_ | 一个包含响应标头的对象。             |
| _isRaw_   | 指示是否为响应跳过格式设置。    |
| _status_  | 响应的 HTTP 状态代码。                     |

### <a name="accessing-the-request-and-response"></a>访问请求和响应 

使用 HTTP 触发器时，可采用多种方式来访问 HTTP 请求和响应对象：

+ **通过 `context` 对象的 `req` 和 `res` 属性。** 采用此方式时，可以使用传统模式通过上下文对象访问 HTTP 数据，而不必使用完整的 `context.bindings.name` 模式。 以下示例展示了如何访问 `context` 上的 `req` 和 `res` 对象：

    ```javascript
    // You can access your http request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your http response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ **通过已命名的输入和输出绑定。** 采用此方式时，HTTP 触发器和绑定的工作方式与其他绑定相同。 以下示例使用已命名的 `response` 绑定设置响应对象： 

    ```json
    {
        "type": "http",
        "direction": "out",
        "name": "response"
    }
    ```
    ```javascript
    context.bindings.response = { status: 201, body: "Insert succeeded." };
    ```
+ **_[仅响应]_ 通过调用 `context.res.send(body?: any)`。** 创建 HTTP 响应时使用输入 `body` 作为响应正文。 `context.done()` 是隐式调用的。

+ **_[仅响应]_ 通过调用 `context.done()`。** A special type of HTTP binding returns the response that is passed to the `context.done()` method. 以下 HTTP 输出绑定定义了一个 `$return` 输出参数：

    ```json
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
    ``` 
    ```javascript
     // Define a valid response object.
    res = { status: 201, body: "Insert succeeded." };
    context.done(null, res);   
    ```  

## <a name="node-version"></a>Node 版本

下表显示了 Functions 运行时的每个主要版本使用的 Node.js 版本：

| Functions 版本 | Node.js 版本 | 
|---|---|
| 1.x | 6.11.2（运行时锁定） |
| 2.x  | _Active LTS_ and _Maintenance LTS_ Node.js versions (~10 recommended). Target the version in Azure by setting the WEBSITE_NODE_DEFAULT_VERSION [app setting](functions-how-to-use-azure-function-app-settings.md#settings) to `~10`.|

可以通过查看上述应用设置或打印任何函数的 `process.version` 来查看运行时正在使用的当前版本。

## <a name="dependency-management"></a>依赖项管理
若要在 JavaScript 代码中使用社区库（如下面的示例所示），需要确保在 Azure 中的 Function App 上安装所有依赖项。

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

> [!NOTE]
> 应当在 Function App 的根目录下定义一个 `package.json` 文件。 定义该文件将允许应用中的所有函数共享所缓存的相同包，从而获得最佳性能。 如果发生版本冲突，可以通过在具体函数的文件夹中添加一个 `package.json` 文件来解决冲突。  

部署过程中，从源控件中部署 Function App 时，存储库中存在的任何 `package.json` 文件都将在其文件夹中触发 `npm install`。 但在通过门户或 CLI 部署时，必须手动安装包。

可通过两种方法在 Function App 上安装包： 

### <a name="deploying-with-dependencies"></a>使用依赖项部署
1. 通过运行 `npm install` 在本地安装所有必需的包。

2. 部署代码，并确保部署中包含 `node_modules` 文件夹。 


### <a name="using-kudu"></a>使用 Kudu
1. 转到  `https://<function_app_name>.scm.azurewebsites.net` 。

2. 单击“调试控制台”，选择“CMD”。 > 

3. 转到 `D:\home\site\wwwroot`，然后将 package.json 文件拖到页面上半部分中的 **wwwroot** 文件夹上。  
    还可采用其他方式将文件上传到 Function App。 有关详细信息，请参阅[如何更新 Function App 文件](functions-reference.md#fileupdate)。 

4. 上传 package.json 文件后，在 Kudu 远程执行控制台中运行  命令。  
    此操作将下载 package.json 文件中指定的包并重新启动 Function App。

## <a name="environment-variables"></a>环境变量

在 Functions 中，服务连接字符串等[应用设置](functions-app-settings.md)在执行过程中将公开为环境变量。 You can access these settings using `process.env`, as shown here in the second and third calls to `context.log()` where we log the `AzureWebJobsStorage` and `WEBSITE_SITE_NAME` environment variables:

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.log('Node.js timer trigger function ran!', timeStamp);
    context.log("AzureWebJobsStorage: " + process.env["AzureWebJobsStorage"]);
    context.log("WEBSITE_SITE_NAME: " + process.env["WEBSITE_SITE_NAME"]);
};
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

在本地运行时，可从 [local.settings.json](functions-run-local.md#local-settings-file) 项目文件读取应用设置。

## <a name="configure-function-entry-point"></a>配置函数入口点

`function.json` 属性 `scriptFile` 和 `entryPoint` 可用于配置导出函数的位置和名称。 转译 JavaScript 时，这些属性可能非常重要。

### <a name="using-scriptfile"></a>使用 `scriptFile`

默认情况下通过 `index.js`（与其对应的 `function.json` 共享相同父目录的文件）执行 JavaScript 函数。

`scriptFile` 可用于获取以下示例所示的文件夹结构：

```
FunctionApp
 | - host.json
 | - myNodeFunction
 | | - function.json
 | - lib
 | | - sayHello.js
 | - node_modules
 | | - ... packages ...
 | - package.json
```

`myNodeFunction` 的 `function.json` 应包含 `scriptFile` 属性，该属性指向包含要运行的导出函数的文件。

```json
{
  "scriptFile": "../lib/sayHello.js",
  "bindings": [
    ...
  ]
}
```

### <a name="using-entrypoint"></a>使用 `entryPoint`

在 `scriptFile`（或 `index.js`）中，必须使用 `module.exports` 导出函数才能使其被找到和运行。 默认情况下，触发时执行的函数是该文件的唯一导出（导出名为 `run` 或 `index`）。

可以使用 `function.json` 中的 `entryPoint` 配置此项设置，如以下示例所示：

```json
{
  "entryPoint": "logFoo",
  "bindings": [
    ...
  ]
}
```

Functions v2.x 支持用户函数中的 `this` 参数，其中的函数代码可能如以下示例所示：

```javascript
class MyObj {
    constructor() {
        this.foo = 1;
    };

    logFoo(context) { 
        context.log("Foo is " + this.foo); 
        context.done(); 
    }
}

const myObj = new MyObj();
module.exports = myObj;
```

In this example, it is important to note that although an object is being exported, there are no guarantees for preserving state between executions.

## <a name="local-debugging"></a>Local Debugging

When started with the `--inspect` parameter, a Node.js process listens for a debugging client on the specified port. In Azure Functions 2.x, you can specify arguments to pass into the Node.js process that runs your code by adding the environment variable or App Setting `languageWorkers:node:arguments = <args>`. 

To debug locally, add `"languageWorkers:node:arguments": "--inspect=5858"` under `Values` in your [local.settings.json](https://docs.microsoft.com/azure/azure-functions/functions-run-local#local-settings-file) file and attach a debugger to port 5858.

When debugging using VS Code, the `--inspect` parameter is automatically added using the `port` value in the project's launch.json file.

In version 1.x, setting `languageWorkers:node:arguments` will not work. The debug port can be selected with the [`--nodeDebugPort`](https://docs.microsoft.com/azure/azure-functions/functions-run-local#start) parameter on Azure Functions Core Tools.

## <a name="typescript"></a>TypeScript

When you target version 2.x of the Functions runtime, both [Azure Functions for Visual Studio Code](functions-create-first-function-vs-code.md) and the [Azure Functions Core Tools](functions-run-local.md) let you create function apps using a template that support TypeScript function app projects. The template generates `package.json` and `tsconfig.json` project files that make it easier to transpile, run, and publish JavaScript functions from TypeScript code with these tools.

A generated `.funcignore` file is used to indicate which files are excluded when a project is published to Azure.  

TypeScript files (.ts) are transpiled into JavaScript files (.js) in the `dist` output directory. TypeScript templates use the [`scriptFile` parameter](#using-scriptfile) in `function.json` to indicate the location of the corresponding .js file in the `dist` folder. The output location is set by the template by using `outDir` parameter in the `tsconfig.json` file. If you change this setting or the name of the folder, the runtime is not able to find the code to run.

> [!NOTE]
> Experimental support for TypeScript exists version 1.x of the Functions runtime. The experimental version transpiles TypeScript files into JavaScript files when the function is invoked. In version 2.x, this experimental support has been superseded by the tool-driven method that does transpilation before the host is initialized and during the deployment process.

The way that you locally develop and deploy from a TypeScript project depends on your development tool.

### <a name="visual-studio-code"></a>Visual Studio Code

The [Azure Functions for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) extension lets you develop your functions using TypeScript. The Core Tools is a requirement of the Azure Functions extension.

To create a TypeScript function app in Visual Studio Code, choose `TypeScript` as your language when you create a function app.

When you press **F5** to run the app locally, transpilation is done before the host (func.exe) is initialized. 

When you deploy your function app to Azure using the **Deploy to function app...** button, the Azure Functions extension first generates a production-ready build of JavaScript files from the TypeScript source files.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

There are several ways in which a TypeScript project differs from a JavaScript project when using the Core Tools.

#### <a name="create-project"></a>创建项目

To create a TypeScript function app project using Core Tools, you must specify the TypeScript language option when you create your function app. You can do this in one of the following ways:

- Run the `func init` command, select `node` as your language stack, and then select `typescript`.

- 运行 `func init --worker-runtime typescript` 命令。

#### <a name="run-local"></a>Run local

To run your function app code locally using Core Tools, use the following commands instead of `func host start`: 

```command
npm install
npm start
```

The `npm start` command is equivalent to the following commands:

- `npm run build`
- `func extensions install`
- `tsc`
- `func start`

#### <a name="publish-to-azure"></a>发布到 Azure

Before you use the [`func azure functionapp publish`] command to deploy to Azure, you create a production-ready build of JavaScript files from the TypeScript source files. 

The following commands prepare and publish your TypeScript project using Core Tools: 

```command
npm run build:production 
func azure functionapp publish <APP_NAME>
```

In this command, replace `<APP_NAME>` with the name of your function app.

## <a name="considerations-for-javascript-functions"></a>JavaScript 函数的注意事项

使用 JavaScript 函数时，请注意以下各节中的注意事项。

### <a name="choose-single-vcpu-app-service-plans"></a>选择单 vCPU 应用服务计划

创建使用应用服务计划的函数应用时，建议选择单 vCPU 计划，而不是选择具有多个 vCPU 的计划。 目前，Functions 在单 vCPU VM 上运行 JavaScript 函数更为高效；使用更大的 VM 不会产生预期的性能提高。 When necessary, you can manually scale out by adding more single-vCPU VM instances, or you can enable autoscale. 有关详细信息，请参阅[手动或自动缩放实例计数](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service%2ftoc.json)。

### <a name="cold-start"></a>冷启动

对于无服务器托管模型中开发 Azure Functions，冷启动已成为现实。 “冷启动”是指在函数应用处于非活动状态一段时间后进行第一次启动时，将需要较长时间才能启动。 具体而言，对于具有较大依赖项树的 JavaScript 函数，冷启动可能不足以解决问题。 为了加快冷启动过程，请尽量[以包文件的形式运行函数](run-functions-from-deployment-package.md)。 许多部署方法默认使用包模型中的运行，但如果遇到大规模的冷启动而不是以这种方式运行，则此项更改可以提供明显的改善。

### <a name="connection-limits"></a>Connection Limits

When you use a service-specific client in an Azure Functions application, don't create a new client with every function invocation. Instead, create a single, static client in the global scope. For more information, see [managing connections in Azure Functions](manage-connections.md).

### <a name="use-async-and-await"></a>Use `async` and `await`

When writing Azure Functions in JavaScript, you should write code using the `async` and `await` keywords. Writing code using `async` and `await` instead of callbacks or `.then` and `.catch` with Promises helps avoid two common problems:
 - Throwing uncaught exceptions that [crash the Node.js process](https://nodejs.org/api/process.html#process_warning_using_uncaughtexception_correctly), potentially affecting the execution of other functions.
 - Unexpected behavior, such as missing logs from context.log, caused by asynchronous calls that are not properly awaited.

In the example below, the asynchronous method `fs.readFile` is invoked with an error-first callback function as its second parameter. This code causes both of the issues mentioned above. An exception that is not explicitly caught in the correct scope crashed the entire process (issue #1). Calling `context.done()` outside of the scope of the callback function means that the function invocation may end before the file is read (issue #2). In this example, calling `context.done()` too early results in missing log entries starting with `Data from file:`.

```javascript
// NOT RECOMMENDED PATTERN
const fs = require('fs');

module.exports = function (context) {
    fs.readFile('./hello.txt', (err, data) => {
        if (err) {
            context.log.error('ERROR', err);
            // BUG #1: This will result in an uncaught exception that crashes the entire process
            throw err;
        }
        context.log(`Data from file: ${data}`);
        // context.done() should be called here
    });
    // BUG #2: Data is not guaranteed to be read before the Azure Function's invocation ends
    context.done();
}
```

Using the `async` and `await` keywords helps avoid both of these errors. You should use the Node.js utility function [`util.promisify`](https://nodejs.org/api/util.html#util_util_promisify_original) to turn error-first callback-style functions into awaitable functions.

In the example below, any unhandled exceptions thrown during the function execution only fail the individual invocation that raised an exception. The `await` keyword means that steps following `readFileAsync` only execute after `readFile` is complete. With `async` and `await`, you also don't need to call the `context.done()` callback.

```javascript
// Recommended pattern
const fs = require('fs');
const util = require('util');
const readFileAsync = util.promisify(fs.readFile);

module.exports = async function (context) {
    let data;
    try {
        data = await readFileAsync('./hello.txt');
    } catch (err) {
        context.log.error('ERROR', err);
        // This rethrown exception will be handled by the Functions Runtime and will only fail the individual invocation
        throw err;
    }
    context.log(`Data from file: ${data}`);
}
```

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下资源：

+ [Azure Functions 最佳实践](functions-best-practices.md)
+ [Azure Functions 开发人员参考](functions-reference.md)
+ [Azure Functions 触发器和绑定](functions-triggers-bindings.md)

[`func azure functionapp publish`]: functions-run-local.md#project-file-deployment
