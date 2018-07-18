---
title: Azure 资源运行状况概述 | Microsoft Docs
description: Azure 资源运行状况概述
services: Resource health
documentationcenter: ''
author: shawntabrizi
manager: ''
editor: ''
ms.assetid: 85cc88a4-80fd-4b9b-a30a-34ff3782855f
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 03/27/2018
ms.author: shawn.tabrizi
ms.openlocfilehash: 99e996f182aac774f2e2565d87fd0debaba1b2d1
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2018
ms.locfileid: "30263116"
---
# <a name="azure-resource-health-overview"></a>Azure 资源运行状况概述
 
Azure 资源运行状况有助于在 Azure 服务问题影响资源时进行诊断和获取支持。 它通知你有关资源的当前和过去运行状况的信息， 并提供技术支持以帮助你缓解问题。

[Azure 状态](https://status.azure.com)告知影响大量 Azure 客户的服务问题，而资源运行状况可提供资源运行状况的个性化仪表板。 资源运行状况显示过去由于 Azure 服务问题导致资源不可用的所有时间。 这使你了解是否违反 SLA 变得简单。 

## <a name="resource-definition-and-health-assessment"></a>资源定义和运行状况评估
资源是 Azure 服务中的一个特定实例：例如，虚拟机、Web 应用或 SQL 数据库。

资源运行状况依赖于各种 Azure 服务发出的信号来评估资源是否正常运行。 如果资源运行不正常，资源运行状况将分析其他信息以确定问题根源。 它还识别 Microsoft 采取的用于解决此问题的操作或可以采取的用于解决问题原因的操作。 

有关如何评估运行状况的其他详细信息，请在 [Azure 资源运行状况](resource-health-checks-resource-types.md)中查看资源类型和运行状况检查的完整列表。

## <a name="health-status"></a>运行状况
资源的运行状况显示为以下状态之一。

### <a name="available"></a>可用
状态为“可用”表示服务尚未检测到影响资源运行状况的任何事件。 如果过去 24 小时内资源在计划外停机后进行了恢复，则会看到“最近已解决”通知。

![显示“最近已解决”通知的虚拟机的“可用”状态](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>不可用
状态为“不可用”表示服务已检测到影响资源运行状况的正在发生的平台或非平台事件。

#### <a name="platform-events"></a>平台事件
平台事件是由 Azure 基础结构的多个组件触发的。 它们包括计划的操作（例如计划内维护）和意外的事件（例如计划外的主机重启）。

资源运行状况提供了有关事件和恢复过程的更多详细信息。 还可以通过它联系支持人员，即使你没有有效的 Microsoft 支持协议。

![由于平台事件虚拟机显示“不可用”状态](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>非平台事件
非平台事件是由用户的操作触发的。 例如：停止虚拟机或达到 Redis 缓存的最大连接数。

![由于非平台事件虚拟机显示“不可用”状态](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>Unknown
运行状况为“未知”指示资源运行状况未收到此资源的相关信息已超过 10 分钟。 尽管此状态不是资源状态的最终指示，但它是故障排除过程中一个重要的数据点。

如果资源正在按预期方式运行，资源状态会在几分钟后更改为“可用”。

如果资源遇到问题，“未知”运行状况可能暗示平台中的事件正在影响资源。

![虚拟机的“未知”状态](./media/resource-health-overview/Unknown.png)

### <a name="degraded"></a>已降级
运行状况为“已降级”指示，资源已检测到性能降低，但仍可供使用。
对于何时将资源指定为已降级，不同的资源具有自己的标准。

![虚拟机的“已降级”状态](./media/resource-health-overview/degraded.png)

## <a name="reporting-an-incorrect-status"></a>报告不正确的状态
如果认为当前运行状况不正确，可以通过选择“报告不正确的运行状况”告知我们。 如果某个 Azure 问题正在影响你，建议通过“资源运行状况”来联系支持人员。 

![用于提交不正确状态相关信息的框](./media/resource-health-overview/incorrect-status.png)

## <a name="historical-information"></a>历史信息
可在“资源运行状况”的“运行状况历史记录”部分中访问最多 14 天的运行状况历史记录。 

![过去两周内的“资源运行状况”事件列表](./media/resource-health-overview/history-blade.png)

## <a name="getting-started"></a>入门
若要为某个资源打开“资源运行状况”，请执行以下操作：
1.  登录到 Azure 门户。
2.  浏览资源。
3.  在左窗格中的资源菜单上，选择“资源运行状况”。

![通过资源视图打开“资源运行状况”](./media/resource-health-overview/from-resource-blade.png)

此外，还可以通过选择“所有服务”，并在筛选器文本框中键入“资源运行状况”来访问“资源运行状况”。 在“帮助 + 支持”窗格中，选择[资源运行状况](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth)。

![通过“所有服务”打开“资源运行状况”](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>后续步骤

若要了解有关资源运行状况的详细信息，请参阅以下资源：
-  [Azure 资源运行状况中的资源类型和运行状况检查](resource-health-checks-resource-types.md)
-  [有关 Azure 资源运行状况的常见问题解答](resource-health-faq.md)




