---
title: "Azure 资源运行状况概述 | Microsoft 文档"
description: "Azure 资源运行状况概述"
services: Resource health
documentationcenter: 
author: BernardoAMunoz
manager: 
editor: 
ms.assetid: 85cc88a4-80fd-4b9b-a30a-34ff3782855f
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 07/01/2017
ms.author: BernardoAMunoz
ms.openlocfilehash: 9912911d6ed43a70a7a0f9316079d8f66d063f64
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="azure-resource-health-overview"></a>Azure 资源运行状况概述
 
资源运行状况在 Azure 问题影响资源时帮助你进行诊断并获得支持。 它通知你有关资源的当前和过去运行状况的信息，并帮助你缓解问题。 在需要有关 Azure 服务问题的帮助时，资源运行状况将提供技术支持。

[Azure 状态](https://status.azure.com)告知影响大量 Azure 客户的服务问题，而资源运行状况可提供资源运行状况的个性化仪表板。 资源运行状况显示过去由于 Azure 服务问题导致的资源不可用的所有时间，这使得你可以轻松了解是否违反了某个 SLA。 

## <a name="what-is-considered-a-resource-and-how-does-resource-health-decides-if-a-resource-is-healthy-or-not"></a>什么是资源？资源运行状况如何决定资源是否正常运行？
资源是 Azure 服务中的一个特定实例，例如：虚拟机、Web 应用或 SQL 数据库。

资源运行状况依赖于各种 Azure 服务发出的信号来评估资源是否正常运行。 如果资源运行不正常，资源运行状况将分析其他信息以确定问题根源。 它还识别 Microsoft 采取的用于解决此问题的操作或可以采取的用于解决问题原因的操作。 

有关如何评估运行状况的其他详细信息，请在 [Azure 资源运行状况](resource-health-checks-resource-types.md)中查看资源类型和运行状况检查的完整列表。

## <a name="health-status-provided-by-resource-health"></a>资源运行状况提供的运行状况状态
资源的运行状况为以下状态之一：

### <a name="available"></a>可用
服务未检测到任何影响资源运行状况的事件。 如果过去 24 小时内资源在计划外停机后进行了恢复，则会看到**最近恢复**通知。

![资源运行状况 - 虚拟机可用](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>不可用
服务检测到影响资源运行状况的正在运行的平台或非平台事件。

#### <a name="platform-events"></a>平台事件
这些事件是由 Azure 基础结构的多个组件触发的。 它们包括计划的操作（例如计划内维护）和意外的事件（例如计划外的主机重启）。

资源运行状况提供了有关事件和恢复过程的更多详细信息。 还可以通过它联系支持人员，即使你没有有效的 Microsoft 支持协议。

![资源运行状况 - 由于平台事件，虚拟机不可用](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>非平台事件
这些事件是由用户采取的操作触发的。 例如：停止虚拟机或达到 Redis 缓存的最大连接数。

![资源运行状况 - 由于非平台事件，虚拟机不可用](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>Unknown
此运行状况状态指示资源运行状况未收到此资源的相关信息已超过 10 分钟。 尽管此状态不是资源状态的最终指示，但它是故障排除过程中一个重要的数据点：
* 如果资源正在按预期方式运行，资源状态会在几分钟后更新为“可用”。
* 如果资源遇到问题，“未知”运行状况状态可能暗示资源受到平台中的事件影响。

![资源运行状况 - 虚拟机未知](./media/resource-health-overview/Unknown.png)

### <a name="degraded"></a>已降级
此运行状况状态指示，检测到资源的性能降低，但资源仍可供使用。
对于何时将资源指定为已降级，不同的资源具有自己的标准。

![资源运行状况 - 虚拟机已降级](./media/resource-health-overview/degraded.png)

## <a name="report-an-incorrect-status"></a>报告不正确的状态
任何时候，如果认为当前运行状况状态不正确，均可以通过单击“报告不正确的运行状况状态”告知我们。 如果受到某个 Azure 问题影响，建议通过“资源运行状况”来联系支持人员。 

![资源运行状况 - 报告不正确状态](./media/resource-health-overview/incorrect-status.png)

## <a name="historical-information"></a>历史信息
可以通过单击“资源运行状况”中的“查看历史记录”访问最多 14 天的运行状况历史记录。 

![资源运行状况 - 报表历史记录](./media/resource-health-overview/history-blade.png)

## <a name="getting-started"></a>入门
打开某个资源的资源运行状况
1.  登录 Azure 门户。
2.  导航到资源。
3.  在位于左侧的资源菜单中，单击“资源运行状况”。

![通过资源视图打开资源运行状况](./media/resource-health-overview/from-resource-blade.png)

此外，还可以通过单击“所有服务”，并在筛选器文本框中键入**资源运行状况**来打开“帮助和支持”边栏选项卡，访问资源运行状况。 最后单击[“资源运行状况”](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth)。

![通过“所有服务”打开资源运行状况](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>后续步骤

若要了解有关资源运行状况的详细信息，请参阅以下资源：
-  [Azure 资源运行状况中的资源类型和运行状况检查](resource-health-checks-resource-types.md)
-  [有关 Azure 资源运行状况的常见问题](resource-health-faq.md)




