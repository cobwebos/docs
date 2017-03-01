---
title: "Azure Functions JavaScript 开发人员参考 | Microsoft 文档"
description: "了解如何使用 JavaScript 开发 Azure Functions。"
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "Azure Functions, Functions, 事件处理, webhook, 动态计算, 无服务体系结构"
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.service: functions
ms.devlang: nodejs
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/13/2016
ms.author: chrande, glenga
translationtype: Human Translation
ms.sourcegitcommit: e660f3313ba6805356eef30b3a17ac609fc9043e
ms.openlocfilehash: 880fdbd6fc51f16add2f2497402d8b01047d0847


---
# <a name="azure-functions-javascript-developer-guide"></a>Azure Functions JavaScript 开发人员指南
> [!div class="op_single_selector"]
> * [C# 脚本](functions-reference-csharp.md)
> * [F# 脚本](functions-reference-fsharp.md)
> * [JavaScript](functions-reference-node.md)
> 
> 

Azure Functions 的 JavaScript 体验可以轻松地导出一个传递 `context` 对象的函数，该对象可用于与运行时通信，以及通过绑定接收和发送数据。

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

`direction === "in"` 的绑定作为函数参数传递，这意味着可以使用 [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx) 动态处理新输入（例如，通过使用 `arguments.length` 循环访问所有输入）。 如果只有一个没有其他输入的触发器，则此功能非常方便，因为可以在不引用 `context` 对象的情况下可预见的访问触发器数据。

参数总是以其在 function.json 中出现的顺序传递给函数，即使没有在 exports 语句中指定它们。 例如，如果具有 `function(context, a, b)` 并将其更改为 `function(context, a)`，仍然可以通过参考 `arguments[3]` 获取函数代码中的值 `b`。

所有绑定，无论方向如何，也在 `context` 对象上传递（见下文）。 

## <a name="context-object"></a>上下文对象
运行时使用 `context` 对象将数据传入和传出函数，并能与其进行通信。

上下文对象始终是函数的第一参数，应始终包含在函数中，因为它具有正确使用运行时所需的 `context.done` 和 `context.log` 等方法。 可以按喜好为对象命名（如 `ctx` 或 `c`）。

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // function logic goes here :)
};
```

## <a name="contextbindings"></a>context.bindings
`context.bindings` 对象收集所有输入和输出数据。 数据通过绑定的 `name` 属性添加到 `context.bindings` 对象。 例如，在 function.json 中给定以下绑定定义，可以通过 `context.bindings.myInput` 访问队列的内容。 

```json
{
    "type":"queue",
    "direction":"in",
    "name":"myInput"
    ...
}
```

```javascript
// myInput contains the input data which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

## `context.done([err],[propertyBag])`
`context.done` 函数告诉运行时已完成运行。 完成此函数后进行调用非常重要；如果没有调用，运行时仍然不知道函数已运行完成。 

`context.done` 函数允许将用户定义的错误，以及一个将覆盖 `context.bindings` 对象上的属性的属性包传回运行时。

```javascript
// Even though we set myOutput to have:
//  -> text: hello world, number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method will overwrite the myOutput binding to be: 
//  -> text: hello there, world, noNumber: true
```

## <a name="contextlogmessage"></a>context.log(message)
`context.log` 方法允许输出为了记录而相关在一起的日志语句。 如果使用 `console.log`，消息将只显示进程级记录，这不太有用。

```javascript
/* You can use context.log to log output specific to this 
function. You can access your bindings via context.bindings */
context.log({hello: 'world'}); // logs: { 'hello': 'world' } 
```

`context.log` 方法支持的参数格式与 Node [ util.format 方法](https://nodejs.org/api/util.html#util_util_format_format)支持的参数格式相同。 因此，例如，代码如下：

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

可以编写如下代码：

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

## <a name="http-triggers-contextreq-and-contextres"></a>HTTP触发器：context.req 和 context.res
在 HTTP 触发器的情况下，因为对于HTTP 请求和响应对象使用 `req` 和 `res` 是一种常见的模式，所以决定使其能在上下文对象上轻松进行访问，而不是强制使用完整的 `context.bindings.name` 模式。

```javascript
// You can access your http request off of the context ...
if(context.req.body.emoji === ':pizza:') context.log('Yay!');
// and also set your http response
context.res = { status: 202, body: 'You successfully ordered more coffee!' };   
```

## <a name="node-version--package-management"></a>节点版本和包管理
当前节点版本被锁定为 `6.5.0`。 我们正在调查添加对更多版本的支持，并使其可配置。

可以通过将 package.json 文件上传到 Function App 文件系统中的函数文件夹中，使函数中包含包。 有关文件上传说明，请参阅 [Azure Functions 开发人员参考主题](functions-reference.md#fileupdate)中的**如何更新 Function App 文件**部分。 

还可以在 Function App 的 SCM (Kudu) 命令行界面中使用 `npm install`：

1. 导航到：`https://<function_app_name>.scm.azurewebsites.net`。
2. 单击“调试控制台”，选择“CMD”。
3. 导航到 `D:\home\site\wwwroot\<function_name>`。
4. 运行 `npm install`。

安装了所需的软件包后，便可以通过常规方式（即通过 `require('packagename')`）将它们导入到函数中

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

## <a name="environment-variables"></a>环境变量
若要获取环境变量或应用设置值，请使用 `process.env`，如以下代码示例所示：

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

## <a name="typescriptcoffeescript-support"></a>TypeScript/CoffeeScript support
但是，还没有通过运行时自动编译 TypeScript/CoffeeScript 的任何直接支持，因此所有这些都需要在运行时外，即在部署时，进行处理。 

## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅以下资源：

* [Azure Functions 最佳实践](functions-best-practices.md)
* [Azure Functions 开发人员参考](functions-reference.md)
* [Azure Functions C# 开发人员参考](functions-reference-csharp.md)
* [Azure Functions F# 开发人员参考](functions-reference-fsharp.md)
* [Azure Functions 触发器和绑定](functions-triggers-bindings.md)




<!--HONumber=Feb17_HO1-->


