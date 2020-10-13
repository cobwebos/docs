---
title: Azure Functions 作为 Azure 事件网格事件的事件处理程序
description: 介绍如何将 Azure Functions 用作事件网格事件的事件处理程序。
ms.topic: conceptual
ms.date: 09/18/2020
ms.openlocfilehash: cd500eed180096388eede96f768f08b896ca6456
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91873721"
---
# <a name="azure-function-as-an-event-handler-for-event-grid-events"></a>Azure Functions 作为事件网格事件的事件处理程序

事件处理程序是发送事件的位置。 处理程序将通过一个操作来处理事件。 几个 Azure 服务已自动配置为处理事件，Azure Functions 就是其中之一。 


若要使用 Azure 函数作为事件的处理程序，请遵循以下方法之一： 

-   使用 [事件网格触发器](../azure-functions/functions-bindings-event-grid-trigger.md)。  指定 **Azure 函数** 作为 **终结点类型**。 然后，指定 Azure function app 和将处理事件的函数。 
-   使用 [HTTP 触发器](../azure-functions/functions-bindings-http-webhook.md)。  指定 **Web 挂钩** 作为 **终结点类型**。 然后，指定将处理事件的 Azure 函数的 URL。 

建议使用第一种方法 (事件网格触发器) ，因为它与第二种方法相比具有以下优势：
-   事件网格会自动验证事件网格触发器。 使用通用 HTTP 触发器时，必须自行实现[验证响应](webhook-event-delivery.md)。
-   事件网格根据函数可处理事件的已知速率来自动调整事件传递到事件网格事件触发的函数的速率。 这一速率匹配功能设置传递错误，这些错误不能处理事件，因为函数的事件处理速率可能会随时间而改变。 若要以高吞吐量提升效率，请对事件订阅启用批处理。 有关详细信息，请参阅 [启用批处理](#enable-batching)。

    > [!NOTE]
    > 目前，在 **CloudEvents** 架构中传递事件时，不能对 Azure Functions 应用使用事件网格触发器。 应转而使用 HTTP 触发器。

## <a name="tutorials"></a>教程

|标题  |说明  |
|---------|---------|
| [快速入门：使用函数处理事件](custom-event-to-function.md) | 将自定义事件发送到函数进行处理。 |
| [教程：使用事件网格自动调整上传图像的大小](resize-images-on-storage-blob-upload-event.md) | 用户通过 Web 应用将映像上传到存储帐户。 创建存储 Blob 后，事件网格会向用于重设已上传映像的大小的函数应用发送一个事件。 |
| [教程：将大数据流式传输到数据仓库](event-grid-event-hubs-integration.md) | 当事件中心创建捕获文件时，事件网格会将一个事件发送到函数应用。 应用会检索捕获文件并将数据迁移到数据仓库。 |
| [教程：Azure 服务总线到 Azure 事件网格集成示例](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 事件网格会将消息从服务总线主题发送到函数应用和逻辑应用。 |

## <a name="rest-example-for-put"></a>REST 示例（对于 PUT）

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "AzureFunction",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Web/sites/<FUNCTION APP NAME>/functions/<FUNCTION NAME>",
                "maxEventsPerBatch": 10,
                "preferredBatchSizeInKilobytes": 6400
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="enable-batching"></a>启用批处理
为了获得更高的吞吐量，请在订阅上启用批处理。 如果使用的是 Azure 门户，则可以设置每个批处理的最大事件数，以及在创建订阅时或创建后的首选批大小（以 kb 为单位）。 

你可以使用 Azure 门户、PowerShell、CLI 或资源管理器模板配置批处理设置。 

### <a name="azure-portal"></a>Azure 门户
在 UI 中创建订阅时，在 " **创建事件订阅** " 页上，切换到 " **高级功能** " 选项卡，并设置 **每个批处理的最大事件数** 和 **首选批大小（kb）** 的值。 
    
:::image type="content" source="./media/custom-event-to-function/enable-batching.png" alt-text="在创建订阅时启用批处理&quot;:::

可以在 &quot;**事件网格主题**&quot; 页的 &quot;**功能**" 选项卡上更新现有订阅的这些值。 

:::image type="content" source="./media/custom-event-to-function/features-batch-settings.png" alt-text="在创建订阅时启用批处理&quot;:::

可以在 &quot;**事件网格主题**&quot; 页的 &quot;**功能**":::

### <a name="azure-resource-manager-template"></a>Azure 资源管理器模板
可以在 Azure 资源管理器模板中设置 **maxEventsPerBatch** 和 **preferredBatchSizeInKilobytes** 。 有关详细信息，请参阅 [EventGrid eventSubscriptions template reference](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/eventsubscriptions)。

### <a name="azure-cli"></a>Azure CLI
你可以使用 [az eventgrid event-订阅 create](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_create&preserve-view=true) 或 [az eventgrid 事件-订阅更新](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_update&preserve-view=true) 命令，使用以下参数配置与批处理相关的设置： `--max-events-per-batch` 或 `--preferred-batch-size-in-kilobytes` 。

### <a name="azure-powershell"></a>Azure PowerShell
可以使用 [AzEventGridSubscription](https://docs.microsoft.com/powershell/module/az.eventgrid/new-azeventgridsubscription) 或 [AzEventGridSubscription](https://docs.microsoft.com/powershell/module/az.eventgrid/update-azeventgridsubscription) cmdlet，使用以下参数配置与批处理相关的设置： `-MaxEventsPerBatch` 或 `-PreferredBatchSizeInKiloBytes` 。

## <a name="next-steps"></a>后续步骤
如需支持的事件处理程序的列表，请参阅[事件处理程序](event-handlers.md)一文。 
