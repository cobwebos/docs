---
title: 将中继混合连接用作 Azure 事件网格事件的事件处理程序
description: 介绍了如何将 Azure 中继混合连接用作 Azure 事件网格事件的事件处理程序。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 3807e2d125d652b8f5ed7c9dec1b972d69f699f6
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91270196"
---
# <a name="relay-hybrid-connection-as-an-event-handler-for-azure-event-grid-events"></a>将中继混合连接用作 Azure 事件网格事件的事件处理程序
事件处理程序是发送事件的位置。 处理程序将执行一些进一步的操作来处理事件。 一些 Azure 服务被自动配置为处理事件，Azure 中继就是其中之一。 

使用 Azure 中继混合连接将事件发送到企业网络内没有公共访问终结点的应用。

## <a name="tutorials"></a>教程
有关将 Azure 中继混合连接用作事件处理程序的示例，请参阅以下教程。 

|标题  |说明  |
|---------|---------|
| [教程：将事件发送到混合连接](custom-event-to-hybrid-connection.md) | 将自定义事件发送到现有混合连接以供侦听器应用程序处理。 |

## <a name="rest-example-for-put"></a>REST 示例（适用于 PUT）

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "HybridConnection",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Relay/namespaces/<RELAY NAMESPACE NAME>/hybridconnections/<HYBRID CONNECTION NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

> [!NOTE]
> 不支持将事件传递到 **另一个租户** 中的 Azure 中继混合连接。 

## <a name="next-steps"></a>后续步骤
如需支持的事件处理程序的列表，请参阅[事件处理程序](event-handlers.md)一文。 