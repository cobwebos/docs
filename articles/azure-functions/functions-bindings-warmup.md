---
title: Azure 函数预热触发器
description: 了解如何在 Azure 函数中使用预热触发器。
documentationcenter: na
author: alexkarcher-msft
manager: gwallace
keywords: azure 函数、功能、事件处理、预热、冷启动、高级、动态计算、无服务器体系结构
ms.service: azure-functions
ms.topic: reference
ms.date: 11/08/2019
ms.author: alkarche
ms.openlocfilehash: c3ed780bc50b690b2f5c3285024695ec6426b9b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77167314"
---
# <a name="azure-functions-warm-up-trigger"></a>Azure 函数预热触发器

本文介绍如何使用 Azure 函数中的预热触发器。 预热触发器仅支持在[高级计划中](functions-premium-plan.md)运行的函数应用。 当添加实例以缩放正在运行的函数应用时，将调用预热触发器。 您可以使用预热触发器在[预热过程](./functions-premium-plan.md#pre-warmed-instances)期间预加载自定义依赖项，以便函数可以立即开始处理请求。 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x-and-higher"></a>包 - Functions 2.x 及更高版本

[需要 Microsoft.Azure.WebJobs.扩展](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions)NuGet 包，版本**3.0.5 或更高**版本。 [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/) GitHub 存储库中提供了此包的源代码。 

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>触发器

预热触发器允许您定义一个函数，该函数将在新实例添加到正在运行的应用时运行。 在应用开始接收流量之前，可以使用预热函数打开连接、加载依赖项或运行任何其他自定义逻辑。 

预热触发器旨在创建共享依赖项，这些依赖项将由应用中的其他函数使用。 [在此处查看共享依赖项的示例](./manage-connections.md#client-code-examples)。

请注意，预热触发器仅在横向扩展操作期间调用，而不是在重新启动或其他非规模启动期间调用。 您必须确保逻辑可以加载所有必要的依赖项，而无需使用预热触发器。 延迟加载是实现这一点的良好模式。

## <a name="trigger---example"></a>触发器 - 示例

# <a name="c"></a>[C#](#tab/csharp)

下面的示例显示了一个[C# 函数](functions-dotnet-class-library.md)，该函数在添加到应用时将在每个新实例上运行。 不需要返回值属性。


* 您的函数必须命名```warmup```（不区分大小写），并且每个应用可能只有一个预热功能。
* 要将预热用作 .NET 类库函数，请确保对**Microsoft.Azure.WebJobs.扩展>= 3.0.5**
    * ```<PackageReference Include="Microsoft.Azure.WebJobs.Extensions" Version="3.0.5" />```


占位符注释显示应用程序中声明和初始化共享依赖项的位置。 
[在此处了解有关共享依赖项的更多。](./manage-connections.md#client-code-examples)

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


下面的示例显示*函数.json*文件中的预热触发器和一个[C# 脚本函数](functions-reference-csharp.md)，该函数在添加到应用时将在每个新实例上运行。

您的函数必须命名```warmup```（不区分大小写），并且每个应用可能只有一个预热功能。

下面是*函数.json*文件：

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

下面的示例显示*函数.json*文件中的预热触发器和[JavaScript 函数](functions-reference-node.md)，该函数在添加到应用时将在每个新实例上运行。

您的函数必须命名```warmup```（不区分大小写），并且每个应用可能只有一个预热功能。

下面是*函数.json*文件：

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
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

下面的示例显示*函数.json*文件中的预热触发器和[Python 函数](functions-reference-python.md)，该函数在添加到应用时将在每个新实例上运行。

您的函数必须命名```warmup```（不区分大小写），并且每个应用可能只有一个预热功能。

下面是*函数.json*文件：

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

下面的示例显示一个预热触发器，该触发器在将每个新实例添加到应用时运行。

您的函数必须命名`warmup`（不区分大小写），并且每个应用可能只有一个预热功能。

```java
@FunctionName("Warmup")
public void run( ExecutionContext context) {
       context.getLogger().info("Function App instance is warm 🌞🌞🌞");
}
```

---

## <a name="trigger---attributes"></a>触发器 - 特性

在[C# 类库中](functions-dotnet-class-library.md)，`WarmupTrigger`属性可用于配置函数。

# <a name="c"></a>[C#](#tab/csharp)

此示例演示如何使用[预热](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions/Extensions/Warmup/Trigger/WarmupTriggerAttribute.cs)属性。

请注意，必须调用```Warmup```函数，并且每个应用只能有一个预热函数。

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

JavaScript 不支持特性。

# <a name="python"></a>[Python](#tab/python)

Python 不支持属性。

# <a name="java"></a>[Java](#tab/java)

Java 中不支持作为属性的预热触发器。

---

## <a name="trigger---configuration"></a>触发器 - 配置

下表介绍了您在*函数.json*文件和`WarmupTrigger`属性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |描述|
|---------|---------|----------------------|
| **type** | 不适用| 必需 - 必须设置为 `warmupTrigger`。 |
| direction**** | 不适用| 必需 - 必须设置为 `in`。 |
| **name** | 不适用| 必需 - 函数代码中使用的变量名称。|

## <a name="trigger---usage"></a>触发器 - 用法

调用预热触发函数时，不会向该函数提供其他信息。

## <a name="trigger---limits"></a>触发器 - 限制

* 预热触发器仅适用于在[高级计划](./functions-premium-plan.md)上运行的应用。
* 预热触发器仅在扩展操作期间调用，而不是在重新启动或其他非规模启动期间调用。 您必须确保逻辑可以加载所有必要的依赖项，而无需使用预热触发器。 延迟加载是实现这一点的良好模式。
* 实例已运行后，无法调用预热触发器。
* 每个函数应用只能有一个预热触发器函数。

## <a name="next-steps"></a>后续步骤

[详细了解 Azure Functions 触发器和绑定](functions-triggers-bindings.md)
