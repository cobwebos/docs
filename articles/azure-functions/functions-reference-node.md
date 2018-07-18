---
title: Azure Functions JavaScript 开发人员参考 | Microsoft 文档
description: 了解如何使用 JavaScript 开发函数。
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: Azure Functions, Functions, 事件处理, webhook, 动态计算, 无服务体系结构
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.service: functions
ms.devlang: nodejs
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/04/2018
ms.author: tdykstra
ms.openlocfilehash: 78f29cd4a20861e40bb7f7f398979b8d93387a7b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
ms.locfileid: "33936620"
---
# <a name="azure-functions-javascript-developer-guide"></a>Azure Functions JavaScript 开发人员指南

Azure Functions 的 JavaScript 体验可以轻松导出一个函数，可以将该函数作为 `context` 对象进行传递，用以与运行时进行通信，以及用以通过绑定来接收和发送数据。

本文假定已阅读 [Azure Functions 开发人员参考](functions-reference.md)。

## <a name="exporting-a-function"></a>导出函数
所有 JavaScript 函数都必须通过 `module.exports` 导出单个 `function`，以便运行时能找到该函数并运行。 此函数必须始终包含 `context` 对象。

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // Additional inputs can be accessed by the arguments property
    if(arguments.length === 4) {
        context.log('This function has 4 inputs');
    }
};
// or you can include additional inputs in your arguments
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
};
```

`direction === "in"` 的绑定作为函数参数传递，这意味着可以使用 [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx) 动态处理新输入（例如，通过使用 `arguments.length` 循环访问所有输入）。 如果只有一个触发器并且没有其他输入，则此功能非常方便，因为可以在不引用 `context` 对象的情况下可预见地访问触发器数据。

参数总是以其在 function.json 中出现的顺序传递给函数，即使没有在 exports 语句中指定它们。 例如，如果具有 `function(context, a, b)` 并将其更改为 `function(context, a)`，仍然可以通过参考 `arguments[2]` 获取函数代码中的值 `b`。

所有绑定，无论方向如何，也在 `context` 对象上传递（请参阅下面的脚本）。 

## <a name="context-object"></a>上下文对象
运行时使用 `context` 对象将数据传入和传出函数，并能与其进行通信。

`context` 对象始终是函数的第一个参数，必须包含在函数中，因为它具有正确使用运行时所需的 `context.done` 和 `context.log` 等方法。 可以按个人喜好为对象命名（例如 `ctx` 或 `c`）。

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // function logic goes here :)
};
```

### <a name="contextbindings-property"></a>context.bindings 属性

```
context.bindings
```
返回一个包含所有输入和输出数据的已命名对象。 例如，*function.json* 中的以下绑定定义允许通过 `context.bindings.myInput` 对象访问队列的内容。 

```json
{
    "type":"queue",
    "direction":"in",
    "name":"myInput"
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

### <a name="contextdone-method"></a>context.done 方法
```
context.done([err],[propertyBag])
```

通知运行时代码已完成。 必须调用 `context.done`，否则，运行时永远不会知道函数已完成并且执行将超时。 

`context.done` 方法允许将用户定义的错误，以及一个将覆盖 `context.bindings` 对象上的属性的属性包传回运行时。

```javascript
// Even though we set myOutput to have:
//  -> text: hello world, number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method will overwrite the myOutput binding to be: 
//  -> text: hello there, world, noNumber: true
```

### <a name="contextlog-method"></a>context.log 方法  

```
context.log(message)
```
允许在默认跟踪级别向流式处理控制台日志进行写入。 `context.log` 中还提供了其他的日志记录方法，用以允许在其他跟踪级别向控制台日志进行写入。


| 方法                 | 说明                                |
| ---------------------- | ------------------------------------------ |
| **error(_message_)**   | 向错误级日志记录或更低级别进行写入。   |
| **warn(_message_)**    | 向警告级日志记录或更低级别进行写入。 |
| **info(_message_)**    | 向信息级日志记录或更低级别进行写入。    |
| **verbose(_message_)** | 向详细级日志记录进行写入。           |

以下示例在警告跟踪级别向控制台进行写入：

```javascript
context.log.warn("Something has happened."); 
```
可以在 host.json 文件中为日志记录设置跟踪级别阈值或者将其关闭。  有关如何向日志进行写入的详细信息，请参阅下一节。

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

`dataType` 的其他选项是 `stream` 和 `string`。

## <a name="writing-trace-output-to-the-console"></a>将跟踪输出写入到控制台 

在 Functions 中，可以使用 `context.log` 方法将跟踪输出写入到控制台。 目前，不能使用 `console.log` 写入到控制台。

调用 `context.log()` 时，消息会在默认跟踪级别（即_信息_跟踪级别）写入到控制台。 以下代码在信息跟踪级别向控制台进行写入：

```javascript
context.log({hello: 'world'});  
```

前面的代码等效于以下代码：

```javascript
context.log.info({hello: 'world'});  
```

以下代码在错误级别向控制台进行写入：

```javascript
context.log.error("An error has occurred.");  
```

因为_错误_是最高跟踪级别，所以，只要启用了日志记录，此跟踪会在所有跟踪级别写入到输出中。  


所有 `context.log` 方法都支持 Node.js [util.format 方法](https://nodejs.org/api/util.html#util_util_format_format)支持的同一参数格式。 请考虑以下代码，它使用默认跟踪级别向控制台进行写入：

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

`request` 对象具有以下属性：

| 属性      | 说明                                                    |
| ------------- | -------------------------------------------------------------- |
| _body_        | 一个包含请求正文的对象。               |
| _headers_     | 一个包含请求标头的对象。                   |
| _method_      | 请求的 HTTP 方法。                                |
| _originalUrl_ | 请求的 URL。                                        |
| _params_      | 一个包含请求的路由参数的对象。 |
| _query_       | 一个包含查询参数的对象。                  |
| _rawBody_     | 字符串形式的消息正文。                           |


### <a name="response-object"></a>响应对象

`response` 对象具有以下属性：

| 属性  | 说明                                               |
| --------- | --------------------------------------------------------- |
| _body_    | 一个包含响应正文的对象。         |
| _headers_ | 一个包含响应标头的对象。             |
| _isRaw_   | 指示是否为响应跳过格式设置。    |
| _status_  | 响应的 HTTP 状态代码。                     |

### <a name="accessing-the-request-and-response"></a>访问请求和响应 

使用 HTTP 触发器时，可采用三种方式来访问 HTTP 响应和请求对象：

+ 通过已命名的输入和输出绑定。 采用此方式时，HTTP 触发器和绑定的工作方式与其他绑定相同。 以下示例使用已命名的 `response` 绑定设置响应对象： 

    ```javascript
    context.bindings.response = { status: 201, body: "Insert succeeded." };
    ```

+ 通过 `context` 对象的 `req` 和 `res` 属性。 采用此方式时，可以使用传统模式通过上下文对象访问 HTTP 数据，而不必使用完整的 `context.bindings.name` 模式。 以下示例展示了如何访问 `context` 上的 `req` 和 `res` 对象：

    ```javascript
    // You can access your http request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your http response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ 通过调用 `context.done()`。 有一种特殊的 HTTP 绑定可返回传递到 `context.done()` 方法的响应。 以下 HTTP 输出绑定定义了一个 `$return` 输出参数：

    ```json
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
    ``` 
    此输出绑定要求在调用 `done()` 时提供响应，如下所示：

    ```javascript
     // Define a valid response object.
    res = { status: 201, body: "Insert succeeded." };
    context.done(null, res);   
    ```  

## <a name="node-version-and-package-management"></a>节点版本和包管理

下表显示了 Functions 运行时的每个主要版本使用的 Node.js 版本：

| Functions 版本 | Node.js 版本 | 
|---|---|
| 1.x | 6.11.2（运行时锁定） |
| 2.x  |>=8.4.0，建议使用当前的 LTS 8.9.4。 使用 WEBSITE_NODE_DEFAULT_VERSION [应用设置](functions-how-to-use-azure-function-app-settings.md#settings)来设置版本。|

可以通过打印任何函数的 `process.version` 来查看运行时正在使用的当前版本。

可以通过以下步骤在 Function App 中包括包： 

1. 转到 `https://<function_app_name>.scm.azurewebsites.net`。

2. 单击“调试控制台”，选择“CMD”。 > 

3. 转到 `D:\home\site\wwwroot`，然后将 package.json 文件拖到页面上半部分中的 **wwwroot** 文件夹上。  
    还可采用其他方式将文件上传到 Function App。 有关详细信息，请参阅[如何更新 Function App 文件](functions-reference.md#fileupdate)。 

4. 上传 package.json 文件后，在 **Kudu 远程执行控制台**中运行 `npm install` 命令。  
    此操作将下载 package.json 文件中指定的包并重新启动 Function App。

安装了所需的包后，可以通过调用 `require('packagename')` 将它们导入到函数中，如以下示例中所示：

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

应当在  Function App 的根目录下定义一个 `package.json` 文件。 定义该文件将允许应用中的所有函数共享所缓存的相同包，从而获得最佳性能。 如果发生版本冲突，可以通过在具体函数的文件夹中添加一个 `package.json` 文件来解决冲突。  

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
## <a name="considerations-for-javascript-functions"></a>JavaScript 函数的注意事项

使用 JavaScript 函数时，请注意以下两节中的注意事项。

### <a name="choose-single-vcpu-app-service-plans"></a>选择单 vCPU 应用服务计划

创建使用应用服务计划的函数应用时，建议选择单 vCPU 计划，而不是选择具有多个 vCPU 的计划。 目前，Functions 在单 vCPU VM 上运行 JavaScript 函数更为高效；使用更大的 VM 不会产生预期的性能提高。 需要时，可以通过添加更多单 vCPU VM 实例来手动扩大，也可以启用自动缩放。 有关详细信息，请参阅[手动或自动缩放实例计数](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service-web%2ftoc.json)。    

### <a name="typescript-and-coffeescript-support"></a>TypeScript 和 CoffeeScript 支持
因为目前还不能直接支持通过运行时自动编译 TypeScript 或 CoffeeScript，因此需要在部署时在运行时外部处理此类支持。 

## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅以下资源：

* [Azure Functions 最佳实践](functions-best-practices.md)
* [Azure Functions 开发人员参考](functions-reference.md)
* [Azure Functions 触发器和绑定](functions-triggers-bindings.md)

