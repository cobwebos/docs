---
title: Azure Functions 预热触发器
description: 了解如何在 Azure Functions 中使用预热触发器。
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: azure functions, functions, 事件处理, 预热, 冷启动, 高级, 动态计算, 无服务器体系结构
ms.service: azure-functions
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 11/08/2019
ms.author: cshoe
ms.openlocfilehash: f5523c513cc0bdd08c43bdbed5046bf662f1a3e5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88206580"
---
# <a name="azure-functions-warm-up-trigger"></a>Azure Functions 预热触发器

本文介绍了如何在 Azure Functions 中使用预热触发器。 只有在[高级计划](functions-premium-plan.md)中运行的函数应用支持预热触发器。 当添加实例来缩放正在运行的函数应用时，将调用预热触发器。 你可以在[预热过程](./functions-premium-plan.md#pre-warmed-instances)中使用预热触发器来预加载自定义依赖项，使你的函数能够准备就绪，以便立即开始处理请求。 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x-and-higher"></a>包 - Functions 2.x 及更高版本

需要 [Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet 包版本 **3.0.5 或更高版本**。 [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/) GitHub 存储库中提供了此包的源代码。 

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>触发器

使用预热触发器，你可以定义在将新实例添加到正在运行的应用时将在新实例上运行的函数。 你可以使用预热函数，在应用开始接收流量之前打开连接、加载依赖项或运行任何其他自定义逻辑。 

预热触发器用于创建将由应用中的其他函数使用的共享依赖项。 [请在此处查看共享依赖项的示例](./manage-connections.md#client-code-examples)。

请注意，只有在执行横向扩展操作期间才会调用预热触发器，在重启或其他非缩放启动期间不会调用。 你必须确保你的逻辑可以在不使用预热触发器的情况下加载所有必需的依赖项。 延迟加载是实现此目的的一个好模式。

## <a name="trigger---example"></a>触发器 - 示例

# <a name="c"></a>[C#](#tab/csharp)

下面的示例展示了一个 [C# 函数](functions-dotnet-class-library.md)，该函数将在向应用添加新实例时在每个新实例上运行。 不需要返回值属性。


* 函数必须命名为 ```warmup```（不区分大小写），并且每个应用只能有一个预热函数。
* 若要使用某个 .NET 类库函数作为预热函数，请确保引用 **3.0.5 或更高版本的 Microsoft.Azure.WebJobs.Extensions** 包。
    * ```<PackageReference Include="Microsoft.Azure.WebJobs.Extensions" Version="3.0.5" />```


占位符注释显示了在应用程序中声明和初始化共享依赖项的位置。 
[在此处详细了解共享依赖项](./manage-connections.md#client-code-examples)。

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
 
namespace WarmupSample
{

    //Declare shared dependencies here

    public static class Warmup
    {
        [FunctionName("Warmup")]
        public static void Run([WarmupTrigger()] WarmupContext context,
            ILogger log)
        {
            //Initialize shared dependencies here
            
            log.LogInformation("Function App instance is warm 🌞🌞🌞");
        }
    }
}
```
# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)


下面的示例展示了 function.json 文件中的一个预热触发器和一个 [C# 脚本函数](functions-reference-csharp.md)，该函数将在向应用添加新实例时在每个新实例上运行。

函数必须命名为 ```warmup```（不区分大小写），并且每个应用只能有一个预热函数。

function.json  文件如下所示：

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

[配置](#trigger---configuration)部分解释了这些属性。

下面是绑定到 `HttpRequest` 的 C# 脚本代码：

```cs
public static void Run(ILogger log)
{
    log.LogInformation("Function App instance is warm 🌞🌞🌞");  
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

下面的示例展示了 function.json 文件中的一个预热触发器和一个 [JavaScript 函数](functions-reference-node.md)，该函数将在向应用添加新实例时在每个新实例上运行。

函数必须命名为 ```warmup```（不区分大小写），并且每个应用只能有一个预热函数。

function.json  文件如下所示：

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

[配置](#trigger---configuration)部分解释了这些属性。

JavaScript 代码如下所示：

```javascript
module.exports = async function (context, warmupContext) {
    context.log('Function App instance is warm 🌞🌞🌞');
};
```

# <a name="python"></a>[Python](#tab/python)

下面的示例显示了文件 *function.js* 中的预热触发器，以及将在添加到应用时每个新实例上运行的 [Python 函数](functions-reference-python.md) 。

函数必须命名为 ```warmup```（不区分大小写），并且每个应用只能有一个预热函数。

function.json  文件如下所示：

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

[配置](#trigger---configuration)部分解释了这些属性。

下面是 Python 代码：

```python
import logging
import azure.functions as func


def main(warmupContext: func.Context) -> None:
    logging.info('Function App instance is warm 🌞🌞🌞')
```

# <a name="java"></a>[Java](#tab/java)

下面的示例展示了一个预热触发器，该触发器将在向应用添加新实例时运行。

函数必须命名为 `warmup`（不区分大小写），并且每个应用只能有一个预热函数。

```java
@FunctionName("Warmup")
public void run( ExecutionContext context) {
       context.getLogger().info("Function App instance is warm 🌞🌞🌞");
}
```

---

## <a name="trigger---attributes"></a>触发器 - 特性

在 [C# 类库](functions-dotnet-class-library.md)中，`WarmupTrigger` 属性可用于配置函数。

# <a name="c"></a>[C#](#tab/csharp)

此示例演示了如何使用 [warmup](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions/Extensions/Warmup/Trigger/WarmupTriggerAttribute.cs) 属性。

注意，函数必须命名为 ```Warmup```，并且每个应用只能有一个预热函数。

```csharp
 [FunctionName("Warmup")]
        public static void Run(
            [WarmupTrigger()] WarmupContext context, ILogger log)
        {
            ...
        }
```

有关完整示例，请参阅[触发器示例](#trigger---example)。

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

C# 脚本不支持特性。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支持特性。

# <a name="python"></a>[Python](#tab/python)

Python 不支持特性。

# <a name="java"></a>[Java](#tab/java)

Java 中不支持将预热触发器用作属性。

---

## <a name="trigger---configuration"></a>触发器 - 配置

下表解释了在 function.json 文件和 `WarmupTrigger` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
| **type** | 不适用| 必需 - 必须设置为 `warmupTrigger`。 |
| **direction** | 不适用| 必需 - 必须设置为 `in`。 |
| name | 不适用| 必需 - 函数代码中使用的变量名称。|

## <a name="trigger---usage"></a>触发器 - 用法

调用预热触发的函数时，不会向其提供附加信息。

## <a name="trigger---limits"></a>触发器 - 限制

* 预热触发器仅适用于按[高级计划](./functions-premium-plan.md)运行的应用。
* 只有在执行横向扩展操作期间才会调用预热触发器，在重启或其他非缩放启动期间不会调用。 你必须确保你的逻辑可以在不使用预热触发器的情况下加载所有必需的依赖项。 延迟加载是实现此目的的一个好模式。
* 如果实例已在运行，则无法调用预热触发器。
* 每个函数应用只能有一个预热触发器函数。

## <a name="next-steps"></a>后续步骤

[详细了解 Azure Functions 触发器和绑定](functions-triggers-bindings.md)
