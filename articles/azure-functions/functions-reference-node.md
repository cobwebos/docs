---
title: Azure Functions JavaScript 开发人员参考 | Microsoft 文档
description: 了解如何使用 JavaScript 开发函数。
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure Functions, Functions, 事件处理, webhook, 动态计算, 无服务体系结构
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.service: azure-functions
ms.devlang: nodejs
ms.topic: reference
ms.date: 03/04/2018
ms.author: glenga
ms.openlocfilehash: eb9387cec98621e27aff7dcb40b8897e326c6706
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2018
ms.locfileid: "49353486"
---
# <a name="azure-functions-javascript-developer-guide"></a>Azure Functions JavaScript 开发人员指南
本指南包含有关使用 JavaScript 编写 Azure Functions 的复杂性的信息。

JavaScript 函数是导出的 `function`，它将在触发时执行（[触发器在 function.json 中配置](functions-triggers-bindings.md)）。 每个函数都传递一个 `context` 对象，该对象用于接收和发送绑定数据、日志记录以及与运行时通信。

本文假定你已阅读 [Azure Functions 开发人员参考](functions-reference.md)。 此外，建议先按照“快速入门”下的教程来[创建第一个函数](functions-create-first-function-vs-code.md)。

## <a name="folder-structure"></a>文件夹结构

JavaScript 项目所需的文件夹结构如下所示。 请注意，可以更改此默认值：有关更多详细信息，请参阅下面的 [scriptFile](functions-reference-node.md#using-scriptfile) 部分。

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
 | - bin
```

项目的根目录中有共享的 [host.json](functions-host-json.md) 文件，可用于配置函数应用。 每个函数都具有一个文件夹，其中包含其代码文件 (.js) 和绑定配置文件 (function.json)。

[2.x 版](functions-versions.md) Functions 运行时中所需的绑定扩展在 `extensions.csproj` 文件中定义，实际库文件位于 `bin` 文件夹中。 本地开发时，必须[注册绑定扩展](functions-triggers-bindings.md#local-development-azure-functions-core-tools)。 在 Azure 门户中开发函数时，系统将为你完成此注册。

## <a name="exporting-a-function"></a>导出函数

必须通过 [`module.exports`](https://nodejs.org/api/modules.html#modules_module_exports)（或 [`exports`](https://nodejs.org/api/modules.html#modules_exports)）导出 JavaScript 函数。 默认情况下，导出的函数应该是其文件中的唯一导出（导出名为 `run` 或 `index`）。 函数的默认位置是 `index.js`，其中 `index.js` 与相应的 `function.json` 共享相同的父目录。 请注意，`function.json` 父目录的名称始终是函数的名称。 

若要配置文件位置和导出函数名称，请阅读下面的[配置函数的入口点](functions-reference-node.md#configure-function-entry-point)。

导出的函数入口点必须始终将 `context` 对象作为第一参数。

```javascript
// You must include a context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
```
```javascript
// You can also use 'arguments' to dynamically handle inputs
module.exports = async function(context) {
    context.log('Number of inputs: ' + arguments.length);
    // Iterates through trigger and input binding data
    for (i = 1; i < arguments.length; i++){
        context.log(arguments[i]);
    }
};
```

触发器和输入绑定（`direction === "in"` 的绑定）可以作为参数传递给函数。 它们以与 function.json 中定义的顺序相同的顺序传递给函数。 也可以使用 JavaScript [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx) 对象动态处理输入。 例如，如果具有 `function(context, a, b)` 并将其更改为 `function(context, a)`，仍然可以通过参考 `arguments[2]` 获取函数代码中的值 `b`。

所有绑定，无论方向如何，也都使用 `context.bindings` 属性在 `context` 对象上传递。

### <a name="exporting-an-async-function"></a>导出异步函数
使用 JavaScript [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) 声明或普通 JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)（Functions v1.x 不支持）时，无需显式调用 [`context.done`](#contextdone-method) 回叫即可通知函数已完成。 导出的异步函数/Promise 完成时，函数将完成。

例如，下面是一个简单函数，用于记录其已被触发并立即完成执行。
``` javascript
module.exports = async function (context) {
    context.log('JavaScript trigger function processed a request.');
};
```

导出异步函数时，还可配置输出绑定，以使用 `return` 值。 这是一种使用 [`context.bindings`](#contextbindings-property) 属性分配输出的备选方法。

若要使用 `return` 分配输出，请将 `name` 属性更改为 `function.json` 中的 `$return`。
```json
{
  "type": "http",
  "direction": "out",
  "name": "$return"
}
```
JavaScript 函数代码可能如下所示：
```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    // You can call and await an async method here
    return {
        body: "Hello, world!"
    };
}
```

## <a name="context-object"></a>上下文对象
运行时使用 `context` 对象将数据传入和传出函数，并能与其进行通信。

`context` 对象始终是函数的第一个参数，必须包含在函数中，因为它具有正确使用运行时所需的 `context.done` 和 `context.log` 等方法。 可以按个人喜好为对象命名（例如 `ctx` 或 `c`）。

```javascript
// You must include a context, but other arguments are optional
module.exports = function(ctx) {
    // function logic goes here :)
    ctx.done();
};
```

### <a name="contextbindings-property"></a>context.bindings 属性

```
context.bindings
```
返回一个包含所有输入和输出数据的已命名对象。 例如，function.json 中的以下绑定定义允许通过 `context.bindings.myInput` 访问队列的内容和使用 `context.bindings.myOutput` 将输出分配给队列。

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

请注意，可以选择使用 `context.done` 方法而不是 `context.binding` 对象来定义输出绑定数据（参见下方）。

### <a name="contextbindingdata-property"></a>context.bindingData 属性

```
context.bindingData
```
返回包含触发器元数据和函数调用数据（`invocationId`、`sys.methodName`、`sys.utcNow`、`sys.randGuid`）的命名对象。 有关触发器元数据的示例，请参阅此[事件中心示例](functions-bindings-event-hubs.md#trigger---javascript-example)。

### <a name="contextdone-method"></a>context.done 方法
```
context.done([err],[propertyBag])
```

通知运行时代码已完成。 如果函数使用了 JavaScript [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) 声明（使用 Functions 2.x 版本中的 Node 8+ 时可用），则不需要使用 `context.done()`。 `context.done` 回调是隐式调用的。

如果函数不是异步函数，则必须调用 `context.done` 来告知运行时函数是完整的。 如果缺少它，则执行将超时。

`context.done` 方法允许将用户定义的错误传递回运行时以及包含输出绑定数据的 JSON 对象。 传递给 `context.done` 的属性将覆盖 `context.bindings` 对象上设置的任何内容。

```javascript
// Even though we set myOutput to have:
//  -> text: 'hello world', number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method will overwrite the myOutput binding to be: 
//  -> text: 'hello there, world', noNumber: true
```

### <a name="contextlog-method"></a>context.log 方法  

```
context.log(message)
```
允许在默认跟踪级别向流式处理函数日志进行写入。 `context.log` 中还提供了其他的日志记录方法，用以允许在其他跟踪级别写入函数日志：


| 方法                 | Description                                |
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

## <a name="binding-data-type"></a>绑定数据类型

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

## <a name="writing-trace-output-to-the-console"></a>将跟踪输出写入到控制台 

在 Functions 中，可以使用 `context.log` 方法将跟踪输出写入到控制台。 在 Functions v2.x 中，通过 `console.log` 的跟踪输出在 Function App 级别捕获。 这意味着来自 `console.log` 的输出不受限于特定的函数调用，因此不会显示在特定函数的日志中。 但是，它们将传播到 Application Insights。 在 Functions v1.x 中，不能使用 `console.log` 向控制台进行写入。 

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

Functions 允许定义向控制台进行写入时使用的阈值跟踪级别，这使得可以轻松控制从函数向控制台写入跟踪的方式。 若要针对写入到控制台的所有跟踪设置阈值，请在 host.json 文件中使用 `tracing.consoleLevel` 属性。 此设置应用于 Function App 中的所有函数。 以下示例设置跟踪阈值来启用详细日志记录：

```json
{ 
    "tracing": {      
        "consoleLevel": "verbose"     
    }
}  
```

**consoleLevel** 的值对应于 `context.log` 方法的名称。 要为控制台禁用所有跟踪日志记录，请将 **consoleLevel** 设置为 _off_。 有关详细信息，请参阅 [host.json 参考](functions-host-json.md)。

## <a name="http-triggers-and-bindings"></a>HTTP 触发器和绑定

HTTP 和 webhook 触发器以及 HTTP 输出绑定使用请求和响应对象来表示 HTTP 消息。  

### <a name="request-object"></a>请求对象

`context.req`（请求）对象具有以下属性：

| 属性      | Description                                                    |
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

| 属性  | Description                                               |
| --------- | --------------------------------------------------------- |
| _body_    | 一个包含响应正文的对象。         |
| _headers_ | 一个包含响应标头的对象。             |
| _isRaw_   | 指示是否为响应跳过格式设置。    |
| _status_  | 响应的 HTTP 状态代码。                     |

### <a name="accessing-the-request-and-response"></a>访问请求和响应 

使用 HTTP 触发器时，可采用多种方式来访问 HTTP 请求和响应对象：

+ 通过 `context` 对象的 `req` 和 `res` 属性。 采用此方式时，可以使用传统模式通过上下文对象访问 HTTP 数据，而不必使用完整的 `context.bindings.name` 模式。 以下示例展示了如何访问 `context` 上的 `req` 和 `res` 对象：

    ```javascript
    // You can access your http request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your http response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ 通过已命名的输入和输出绑定。 采用此方式时，HTTP 触发器和绑定的工作方式与其他绑定相同。 以下示例使用已命名的 `response` 绑定设置响应对象： 

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
+ [仅响应] 通过调用 `context.res.send(body?: any)`。 创建 HTTP 响应时使用输入 `body` 作为响应正文。 `context.done()` 是隐式调用的。

+ [仅响应] 通过调用 `context.done()`。 有一种特殊的 HTTP 绑定可返回传递到 `context.done()` 方法的响应。 以下 HTTP 输出绑定定义了一个 `$return` 输出参数：

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
| 2.x  | 活动 LTS 和当前的 Node.js 版本（推荐 8.11.1 和 10.6.0）。 使用 WEBSITE_NODE_DEFAULT_VERSION [应用设置](functions-how-to-use-azure-function-app-settings.md#settings)来设置版本。|

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

4. 上传 package.json 文件后，在 **Kudu 远程执行控制台**中运行 `npm install` 命令。  
    此操作将下载 package.json 文件中指定的包并重新启动 Function App。

## <a name="environment-variables"></a>环境变量
若要获取环境变量或应用设置值，请使用 `process.env`，如此处的 `GetEnvironmentVariable` 函数中所示：

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.log('Node.js timer trigger function ran!', timeStamp);   
    context.log(GetEnvironmentVariable("AzureWebJobsStorage"));
    context.log(GetEnvironmentVariable("WEBSITE_SITE_NAME"));

    context.done();
};

function GetEnvironmentVariable(name)
{
    return name + ": " + process.env[name];
}
```

## <a name="configure-function-entry-point"></a>配置函数入口点

`function.json` 属性 `scriptFile` 和 `entryPoint` 可用于配置导出函数的位置和名称。 如果 JavaScript 经过转换，这些可能很重要。

### <a name="using-scriptfile"></a>使用 `scriptFile`

默认情况下通过 `index.js`（与其对应的 `function.json` 共享相同父目录的文件）执行 JavaScript 函数。

`scriptFile` 可用于获取如下所示的文件夹结构：
```
FunctionApp
 | - host.json
 | - myNodeFunction
 | | - function.json
 | - lib
 | | - nodeFunction.js
 | - node_modules
 | | - ... packages ...
 | - package.json
```

`myNodeFunction` 的 `function.json` 应包含 `scriptFile` 属性，该属性指向包含要运行的导出函数的文件。
```json
{
  "scriptFile": "../lib/nodeFunction.js",
  "bindings": [
    ...
  ]
}
```

### <a name="using-entrypoint"></a>使用 `entryPoint`

在 `scriptFile`（或 `index.js`）中，必须使用 `module.exports` 导出函数才能使其被找到和运行。 默认情况下，触发时执行的函数是该文件的唯一导出（导出名为 `run` 或 `index`）。

这可以使用 `function.json` 中的 `entryPoint` 进行此配置：
```json
{
  "entryPoint": "logFoo",
  "bindings": [
    ...
  ]
}
```

Functions v2.x 支持用户函数中的 `this` 参数，其中的函数代码可能如下所示：
```javascript
class MyObj {
    constructor() {
        this.foo = 1;
    };
    
    function logFoo(context) { 
        context.log("Foo is " + this.foo); 
        context.done(); 
    }
}

const myObj = new MyObj();
module.exports = myObj;
```

请在此示例中务必注意，尽管正在导出对象，但无法保证可保留两次执行之间的状态。

## <a name="considerations-for-javascript-functions"></a>JavaScript 函数的注意事项

使用 JavaScript 函数时，请注意以下各节中的注意事项。

### <a name="choose-single-vcpu-app-service-plans"></a>选择单 vCPU 应用服务计划

创建使用应用服务计划的函数应用时，建议选择单 vCPU 计划，而不是选择具有多个 vCPU 的计划。 目前，Functions 在单 vCPU VM 上运行 JavaScript 函数更为高效；使用更大的 VM 不会产生预期的性能提高。 需要时，可以通过添加更多单 vCPU VM 实例来手动扩大，也可以启用自动缩放。 有关详细信息，请参阅[手动或自动缩放实例计数](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service-web%2ftoc.json)。    

### <a name="typescript-and-coffeescript-support"></a>TypeScript 和 CoffeeScript 支持
因为目前还不能直接支持通过运行时自动编译 TypeScript 或 CoffeeScript，因此需要在部署时在运行时外部处理此类支持。 

### <a name="cold-start"></a>冷启动
对于无服务器托管模型中开发 Azure Functions，冷启动已成为现实。 “冷启动”是指在 Function App 处于非活动状态一段时间后进行第一次启动时，将需要较长时间才能启动。 具有较大依赖项树的 JavaScript 函数尤其如此，这可能导致严重的速度缓慢情况。 若要加快此过程，（如果可能）请[将函数作为包文件运行](run-functions-from-deployment-package.md)。 默认情况下，许多部署方法都选择使用此模型，但如果遇到大型冷启动并且未通过包文件运行，此方法可能可以大幅度提速。

## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅以下资源：

* [Azure Functions 最佳实践](functions-best-practices.md)
* [Azure Functions 开发人员参考](functions-reference.md)
* [Azure Functions 触发器和绑定](functions-triggers-bindings.md)

