---
title: Azure Functions 预热触发器
description: 了解如何在 Azure Functions 中使用预热触发器。
documentationcenter: na
author: alexkarcher-msft
manager: gwallace
keywords: azure 函数，函数，事件处理，预热，冷启动，高级，动态计算，无服务器体系结构
ms.service: azure-functions
ms.topic: reference
ms.date: 11/08/2019
ms.author: alkarche
ms.openlocfilehash: 013001eebeec232cc60e31f1a850aeab4fd6c905
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982235"
---
# <a name="azure-functions-warm-up-trigger"></a>Azure Functions 预热触发器

本文介绍如何在 Azure Functions 中使用预热触发器。 预热触发器仅支持[高级计划](functions-premium-plan.md)中运行的函数应用。 添加实例以缩放正在运行的函数应用时，将调用预热触发器。 您可以使用预热触发器在[预处理过程](./functions-premium-plan.md#pre-warmed-instances)中预先加载自定义依赖项，以便您的函数可以立即开始处理请求。 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x-and-higher"></a>包 - Functions 2.x 及更高版本

需要**3.0.5 或更高**版本的[Microsoft Azure web 作业](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions)包。 [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/) GitHub 存储库中提供了此包的源代码。 

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>触发器

使用预热触发器可以定义一个函数，该函数将在添加到正在运行的应用程序的新实例上运行。 你可以使用预热函数在应用开始接收流量之前打开连接、加载依赖项或运行任何其他自定义逻辑。 

预热触发器用于创建将由应用中的其他函数使用的共享依赖项。 [请参阅此处的共享依赖项的示例](./manage-connections.md#client-code-examples)。

请注意，预热触发器仅在向外扩展操作期间调用，而不是在重新启动或其他非缩放启动过程中调用。 您必须确保您的逻辑可以加载所有必要的依赖项，而无需使用预热触发器。 延迟加载是实现此目的的一个好模式。

## <a name="trigger---example"></a>触发器 - 示例

# <a name="c"></a>[C#](#tab/csharp)

下面的示例演示了一个[c # 函数](functions-dotnet-class-library.md)，该函数将在添加到应用中的每个新实例上运行。 不需要返回值特性。


* 函数必须命名为```warmup``` （不区分大小写），并且每个应用程序只能有一个预热函数。
* 若要使用预热作为 .NET 类库功能，请确保具有对3.0.5 的包引用 **>=**
    * ```<PackageReference Include="Microsoft.Azure.WebJobs.Extensions" Version="3.0.5" />```


占位符注释显示了应用程序中声明和初始化共享依赖项的位置。 
[在此处了解有关共享依赖项的详细信息](./manage-connections.md#client-code-examples)。

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
# <a name="c-script"></a>[C # 脚本](#tab/csharp-script)


下面的示例演示*函数 json*文件中的预热触发器，以及将在添加到应用时每个新实例上运行的[c # 脚本函数](functions-reference-csharp.md)。

您的函数必须命名```warmup```为（不区分大小写），并且每个应用程序只能有一个预热函数。

下面是*函数 json*文件：

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

下面的示例演示*函数 json*文件中的预热触发器，以及将在添加到应用时每个新实例上运行的[JavaScript 函数](functions-reference-node.md)。

函数必须命名为```warmup``` （不区分大小写），并且每个应用程序只能有一个预热函数。

下面是*函数 json*文件：

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

下面的示例演示*函数 json*文件中的预热触发器，以及将在添加到应用时每个新实例上运行的[Python 函数](functions-reference-python.md)。

函数必须命名为```warmup``` （不区分大小写），并且每个应用程序只能有一个预热函数。

下面是*函数 json*文件：

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

下面的示例演示了在将每个新实例添加到应用程序时运行的预热触发器。

函数必须命名为`warmup` （不区分大小写），并且每个应用程序只能有一个预热函数。

```java
@FunctionName("Warmup")
public void run( ExecutionContext context) {
       context.getLogger().info("Function App instance is warm 🌞🌞🌞");
}
```

---

## <a name="trigger---attributes"></a>触发器 - 特性

在[c # 类库](functions-dotnet-class-library.md)中， `WarmupTrigger`特性可用于配置函数。

# <a name="c"></a>[C#](#tab/csharp)

此示例演示如何使用[预热](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions/Extensions/Warmup/Trigger/WarmupTriggerAttribute.cs)特性。

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

# <a name="c-script"></a>[C # 脚本](#tab/csharp-script)

C# 脚本不支持特性。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支持特性。

# <a name="python"></a>[Python](#tab/python)

Python 不支持特性。

# <a name="java"></a>[Java](#tab/java)

不支持在 Java 中将预热触发器用作特性。

---

## <a name="trigger---configuration"></a>触发器 - 配置

下表说明了在*函数 json*文件和`WarmupTrigger`属性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
| type  | 不适用| 必需 - 必须设置为 `warmupTrigger`。 |
| **方向键** | 不适用| 必需 - 必须设置为 `in`。 |
| **name** | 不适用| 必需-在函数代码中使用的变量名称。|

## <a name="trigger---usage"></a>触发器 - 用法

调用预热触发函数时，不会向其提供附加信息。

## <a name="trigger---limits"></a>触发器 - 限制

* 预热触发器仅适用于[高级计划](./functions-premium-plan.md)中运行的应用程序。
* 预热触发器仅在向外扩展操作期间调用，而不是在重新启动或其他非缩放启动过程中调用。 您必须确保您的逻辑可以加载所有必要的依赖项，而无需使用预热触发器。 延迟加载是实现此目的的一个好模式。
* 如果实例已在运行，则无法调用预热触发器。
* 每个 function 应用只能有一个预热触发器函数。

## <a name="next-steps"></a>后续步骤

[详细了解 Azure Functions 触发器和绑定](functions-triggers-bindings.md)
