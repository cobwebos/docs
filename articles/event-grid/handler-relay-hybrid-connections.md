---
title: 将中继混合连接用作 Azure 事件网格事件的事件处理程序
description: 介绍了如何将 Azure 中继混合连接用作 Azure 事件网格事件的事件处理程序。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: 04fbc7b739fa8ea7b08a3341c2f78244c445e721
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800886"
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

## <a name="next-steps"></a>后续步骤
如需支持的事件处理程序的列表，请参阅[事件处理程序](event-handlers.md)一文。 