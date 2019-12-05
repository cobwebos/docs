---
title: 如何在 Azure Functions 中禁用函数
description: 了解如何在 Azure Functions 1.x 和 2.x 中禁用与启用函数。
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: 7968580fcaa40575571a41f067fa74fbdc0a3a34
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233043"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>如何在 Azure Functions 中禁用函数

本文介绍如何在 Azure Functions 中禁用函数。 禁用某个函数意味着运行时将忽略针对该函数定义的自动触发器。 执行该操作的方式取决于运行时版本和编程语言：

* Functions 2.x：
  * 适用于所有语言的单一方法
  * 适用于 C# 类库的可选方式
* Functions 1.x：
  * 脚本语言
  * C# 类库

## <a name="functions-2x---all-languages"></a>Functions 2.x - 所有语言

在 Functions 2.x 中，可以使用格式为 `AzureWebJobs.<FUNCTION_NAME>.Disabled` 的应用设置禁用函数。 可以通过多种方式创建和修改此应用程序设置，包括使用 [Azure CLI](/cli/azure/) 以及从 **Azure 门户**中函数的“管理”[](https://portal.azure.com)选项卡。 

### <a name="azure-cli"></a>Azure CLI

在 Azure CLI 中，使用 [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) 命令创建和修改应用设置。 以下命令通过创建名为 `QueueTrigger` 的应用设置并将其设置为 `AzureWebJobs.QueueTrigger.Disabled` 来禁用名为 `true` 的函数。 

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

若要重新启用此函数，请使用值 `false` 重新运行相同的命令。

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

### <a name="portal"></a>门户

您还可以在函数的 "**管理**" 选项卡上使用**函数状态**开关。此开关的工作方式是创建和删除 `AzureWebJobs.<FUNCTION_NAME>.Disabled` 应用设置。

![函数状态开关](media/disable-function/function-state-switch.png)

## <a name="functions-2x---c-class-libraries"></a>Functions 2.x - C# 类库

在 Functions 2.x 类库中，我们建议使用适用于所有语言的方法。 但如果需要，可以[像在 Functions 1.x 中一样使用 Disable 属性](#functions-1x---c-class-libraries)。

## <a name="functions-1x---scripting-languages"></a>Functions 1.x - 脚本语言

对于 C# 脚本和 JavaScript 等脚本语言，可使用 `disabled`function.json*文件的* 属性来告知运行时不要触发某个函数。 此属性可设置为 `true` 或应用设置的名称：

```json
{
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ],
    "disabled": true
}
```
或 

```json
    "bindings": [
        ...
    ],
    "disabled": "IS_DISABLED"
```

在第二个示例中，当存在名为 IS_DISABLED 的应用设置且其值设置为 `true` 或 1 时，将禁用相应的函数。

可以在 Azure 门户中编辑文件，或在函数的 "**管理**" 选项卡上使用 "**函数状态**" 开关。门户切换通过更改*函数 json*文件来工作。

![函数状态开关](media/disable-function/function-state-switch.png)

## <a name="functions-1x---c-class-libraries"></a>Functions 1.x - C# 类库

在 Functions 1.x 类库中，可以使用 `Disable` 属性来防止触发某个函数。 可以使用不带构造函数参数的属性，如以下示例中所示：

```csharp
public static class QueueFunctions
{
    [Disable]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

不带构造函数参数的属性要求重新编译并重新部署项目，以更改函数的禁用状态。 该属性的更灵活用法是包含一个引用布尔应用设置的构造函数参数，如以下示例中所示：

```csharp
public static class QueueFunctions
{
    [Disable("MY_TIMER_DISABLED")]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

使用此方法可以通过更改应用设置来启用和禁用函数，而无需重新编译或重新部署。 更改应用设置会导致函数应用重启，因此，可立即识别到禁用状态的更改。

> [!IMPORTANT]
> 只能使用 `Disabled` 属性来禁用类库函数。 为类库函数生成的 *function.json* 文件不可直接编辑。 如果编辑该文件，对 `disabled` 属性所做的任何更改都不起作用。
>
> “管理”选项卡上的“函数状态”开关也是如此，因为它的工作方式就是更改 **function.json** 文件。
>
> 另请注意，门户可能指示函数已禁用，但实际上并未禁用。

## <a name="next-steps"></a>后续步骤

本文介绍了如何禁用自动触发器。 有关触发器的详细信息，请参阅[触发器和绑定](functions-triggers-bindings.md)。
