---
title: 自动增加吞吐量单位 - Azure 事件中心 |Microsoft Docs
description: 在命名空间上启用自动膨胀，以自动按比例增加吞吐量单位。
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 22c12d3233d85a02f6eef8d63e5a4494b4f0cdfa
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273707"
---
# <a name="automatically-scale-up-azure-event-hubs-throughput-units"></a>自动增加 Azure 事件中心吞吐量单位
Azure 事件中心是高度可缩放的数据流式处理平台。 因此，开始使用该服务后事件中心使用量通常会增加。 这样的使用量需要增加预先确定的[吞吐量单位](event-hubs-scalability.md#throughput-units)，以扩展事件中心和处理更大的传输速率。 事件中心的自动膨胀功能通过增加吞吐量单位数进行自动纵向扩展，以便满足使用量需求  。 增加吞吐量单位数可防止出现限制情况，在这些情况下：

* 数据入口速率超过设置的吞吐量单位数。
* 数据出口请求速率超过设置的吞吐量单位数。

当负载的增加超过最小阈值时，事件中心服务会增加吞吐量，不会因服务器繁忙错误导致任何请求失败。

## <a name="how-auto-inflate-works"></a>自动膨胀的工作原理

事件中心流量由[吞吐量单位](event-hubs-scalability.md#throughput-units)控制。 单个吞吐量单位允许入口量为 1 MB/秒，出口量是其两倍。 标准事件中心可以配置 1-20 个吞吐量单位。 利用自动膨胀，可从选择的所需吞吐量单位最小值开始。 然后此功能会自动将所需吞吐量单位增加到最大值，具体取决于增加的流量。 自动膨胀具有以下优势：

- 高效的缩放机制，可从少量开始并随流量增长而增加。
- 自动增加到指定上限，没有限制问题。
- 更好地控制缩放，因为你可控制缩放的时间和程度。

## <a name="enable-auto-inflate-on-a-namespace"></a>在命名空间上启用自动膨胀

可使用下列方法之一在事件中心命名空间上启用或禁用自动膨胀：

- [Azure 门户](https://portal.azure.com)。
- [Azure 资源管理器模板](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate)。

### <a name="enable-auto-inflate-through-the-portal"></a>通过门户启用自动膨胀


#### <a name="enable-at-the-time-of-creation"></a>在创建时启用 
**创建事件中心命名空间时**，可启用自动膨胀功能：
 
![在创建事件中心时启用自动膨胀](./media/event-hubs-auto-inflate/event-hubs-auto-inflate1.png)

启用此选项后，可从少量吞吐量单位开始并随所需使用量的增长而按比例增加。 膨胀的上限不会立即影响定价，定价取决于每小时使用的吞吐量单位数。

#### <a name="enable-auto-inflate-for-an-existing-event-hub"></a>为现有事件中心启用自动膨胀
还可以按照以下说明来启用自动膨胀功能并修改其设置： 
 
1. 在“事件中心命名空间”  页上，选择“自动膨胀吞吐量单位”下的“禁用”   。  

    ![在“事件中心命名空间”页上选择吞吐量单位](./media/event-hubs-auto-inflate/select-throughput-units.png)
2. 在“缩放设置”  页上，选中“启用”复选框  （如果未启用自动缩放功能）。

    ![选择“启用”](./media/event-hubs-auto-inflate/scale-settings.png)
3. 输入吞吐量单位的**最大**数目或使用滚动条设置该值。 
4. （可选）在此页顶部更新吞吐量单位的**最小**数目。 


> [!NOTE]
> 当应用自动膨胀配置以增加吞吐量单位时，事件中心服务会发出诊断日志，提供有关为何以及何时增加吞吐量的信息。 若要启用事件中心的诊断日志记录，请在 Azure 门户的事件中心页上的左侧菜单上选择“诊断设置”  。 有关详细信息，请参阅[设置 Azure 事件中心的诊断日志](event-hubs-diagnostic-logs.md)。 

### <a name="enable-auto-inflate-using-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板启用自动膨胀

可在 Azure 资源管理器模板部署期间启用自动膨胀。 例如，将 `isAutoInflateEnabled` 属性设置为“true”并将 `maximumThroughputUnits` 设置为 10  。 例如：

```json
"resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('namespaceName')]",
            "type": "Microsoft.EventHub/Namespaces",
            "location": "[variables('location')]",
            "sku": {
                "name": "Standard",
                "tier": "Standard"
            },
            "properties": {
                "isAutoInflateEnabled": true,
                "maximumThroughputUnits": 10
            },
            "resources": [
                {
                    "apiVersion": "2017-04-01",
                    "name": "[parameters('eventHubName')]",
                    "type": "EventHubs",
                    "dependsOn": [
                        "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
                    ],
                    "properties": {},
                    "resources": [
                        {
                            "apiVersion": "2017-04-01",
                            "name": "[parameters('consumerGroupName')]",
                            "type": "ConsumerGroups",
                            "dependsOn": [
                                "[parameters('eventHubName')]"
                            ],
                            "properties": {}
                        }
                    ]
                }
            ]
        }
    ]
```

有关完整的模板，请参阅 GitHub 上的[创建事件中心命名空间和启用膨胀模板](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate)。


## <a name="next-steps"></a>后续步骤

访问以下链接可以了解有关事件中心的详细信息：

* [事件中心概述](event-hubs-what-is-event-hubs.md)

