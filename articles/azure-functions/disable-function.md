---
title: 如何在 Azure Functions 中禁用函数
description: 了解如何在 Azure Functions 中禁用与启用函数。
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 8922edb7aaa41bcf50dcce5257b6600f1bde224a
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83115553"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>如何在 Azure Functions 中禁用函数

本文介绍如何在 Azure Functions 中禁用函数。 禁用某个函数意味着运行时将忽略针对该函数定义的自动触发器。 这使你可以在不停止整个函数应用的情况下阻止特定函数运行。

若要禁用函数，建议使用 `AzureWebJobs.<FUNCTION_NAME>.Disabled` 格式的应用设置。 可以通过多种方式创建和修改此应用程序设置，包括使用 [Azure CLI](/cli/azure/)，以及使用 [Azure 门户](https://portal.azure.com)中函数的“管理”选项卡。 

> [!NOTE]  
> 如果使用本文所述的方法禁用 HTTP 触发的函数，则在本地计算机上运行时，仍然可以访问该终结点。  

## <a name="use-the-azure-cli"></a>使用 Azure CLI

在 Azure CLI 中，可以使用 [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) 命令来创建和修改应用设置。 以下命令通过创建名为“`AzureWebJobs.QueueTrigger.Disabled`”的应用设置并将其设置为 `true` 来禁用名为“`QueueTrigger`”的函数。 

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

若要重新启用此函数，请使用值 `false` 重新运行同一命令。

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

## <a name="use-the-portal"></a>使用门户

还可以使用函数的“概览”页上的“启用”和“禁用”按钮。 这两个按钮通过创建和删除 `AzureWebJobs.<FUNCTION_NAME>.Disabled` 应用设置来工作。

![函数状态开关](media/disable-function/function-state-switch.png)

> [!NOTE]  
> 门户集成的测试功能会忽略 `Disabled` 设置。 这意味着，从门户的“测试”窗口启动时，禁用的函数仍会运行。 

## <a name="other-methods"></a>其他方法

虽然我们建议你将应用程序设置方法用于所有语言和所有运行时版本，但是你也可以使用其他几种方法来禁用函数。 这些方法因语言和运行时版本而异，为了后向兼容而保留。 

### <a name="c-class-libraries"></a>C# 类库

在类库函数中，还可以使用 `Disable` 属性来防止函数被触发。 可以使用不带构造函数参数的属性，如以下示例中所示：

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
> “管理”选项卡上的“函数状态”开关也是如此，因为它的工作方式就是更改 *function.json* 文件。 
>
> 另请注意，门户可能指示函数已禁用，但实际上并未禁用。

### <a name="functions-1x---scripting-languages"></a>Functions 1.x - 脚本语言

在版本 1.x 中，还可以使用 function.json 文件的 `disabled` 属性来指示运行时不要触发函数。 此方法仅适用于脚本语言（例如，C# 脚本和 JavaScript）。 `disabled` 属性可设置为 `true` 或某个应用设置的名称：

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

可以在 Azure 门户中编辑该文件，或者使用函数的“概览”页上的“启用”和“禁用”按钮。 门户开关的工作方式是更改 *function.json* 文件。


## <a name="next-steps"></a>后续步骤

本文介绍了如何禁用自动触发器。 有关触发器的详细信息，请参阅[触发器和绑定](functions-triggers-bindings.md)。
