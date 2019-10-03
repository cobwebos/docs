---
title: Azure 资源运行状况概述 | Microsoft Docs
description: Azure 资源运行状况概述
author: stephbaron
ms.author: stbaron
ms.topic: conceptual
ms.service: service-health
ms.date: 05/10/2019
ms.openlocfilehash: 9c2096f94f38d13288c6ce3742252bc6d576835a
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854236"
---
# <a name="resource-health-overview"></a>资源运行状况概述
 
Azure 资源运行状况有助于诊断和获取影响 Azure 资源的服务问题。 它报告资源当前和过去的运行状况。

[Azure 状态](https://status.azure.com)报告影响广泛的 Azure 客户的服务问题。 资源运行状况提供资源运行状况的个性化仪表板。 资源运行状况显示你的资源因 Azure 服务问题而不可用的所有时间。 此数据可让你轻松查看是否违反了 SLA。

## <a name="resource-definition-and-health-assessment"></a>资源定义和运行状况评估

*资源*是 Azure 服务的特定实例, 如虚拟机、web 应用或 SQL 数据库。 资源运行状况依赖不同 Azure 服务发出的信号来评估资源是否正常。 如果资源运行不正常，资源运行状况将分析其他信息以确定问题根源。 它还报告 Microsoft 为解决该问题所采取的操作, 并确定可以执行哪些操作来解决问题。

有关如何评估运行状况的详细信息, 请参阅[Azure 资源运行状况](resource-health-checks-resource-types.md)上的资源类型和运行状况检查的列表。

## <a name="health-status"></a>运行状况

资源的运行状况显示为以下状态之一。

### <a name="available"></a>可用

"*可用*" 表示没有检测到影响资源运行状况的事件。 如果资源在过去24小时内从计划外停机中恢复, 你会看到 "最近已解决" 通知。

![\* 适用于具有 "最近已解决" 通知的虚拟机的状态](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>不可用

"*不可用*" 表示服务检测到一个正在进行的平台或非平台事件会影响资源的运行状况。

#### <a name="platform-events"></a>平台事件

平台事件是由 Azure 基础结构的多个组件触发的。 它们包括计划的操作（例如计划内维护）和意外的事件（例如计划外的主机重启）。

资源运行状况提供有关事件和恢复过程的更多详细信息。 它还使您可以与 Microsoft 支持部门联系, 即使您没有活动的支持协议也是如此。

![由于平台事件, 虚拟机的 "状态" 为 "不可用"](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>非平台事件

非平台事件是由用户操作触发的。 示例包括停止虚拟机或达到 Redis 的 Azure 缓存的最大连接数。

![由于非平台事件, 虚拟机的 "不可用" 状态](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>Unknown

"*未知*" 表示资源运行状况未收到有关资源的信息超过10分钟。 尽管此状态不是资源状态的最终指示, 但它是用于故障排除的重要数据点。

如果资源正在按预期方式运行，资源状态会在几分钟后更改为“可用”  。

如果资源遇到问题, 则 "*未知*" 运行状况可能表示平台中的事件影响资源。

![虚拟机的状态 * 未知 *](./media/resource-health-overview/Unknown.png)

### <a name="degraded"></a>已降级

"*降级*" 表示资源检测到性能降低, 但仍可供使用。

不同的资源在报告其降级时具有其自己的条件。

![虚拟机的状态 * 降级 *](./media/resource-health-overview/degraded.png)

## <a name="reporting-an-incorrect-status"></a>报告不正确的状态

如果您认为当前运行状况状态不正确, 则可以通过选择 "**报告不正确的运行状况**" 来告诉我们。 在 Azure 问题影响你的情况下, 我们鼓励你与资源运行状况联系支持部门。

![提交有关错误状态的信息的窗体](./media/resource-health-overview/incorrect-status.png)

## <a name="history-information"></a>历史记录信息

你可以在资源运行状况的**运行状况历史记录**部分中访问14天的历史记录。

![过去两周内的“资源运行状况”事件列表](./media/resource-health-overview/history-blade.png)

## <a name="get-started"></a>入门

若要为某个资源打开“资源运行状况”，请执行以下操作：

1. 登录到 Azure 门户。
2. 浏览资源。
3. 在左窗格中的资源菜单上，选择“资源运行状况”  。

![通过资源视图打开“资源运行状况”](./media/resource-health-overview/from-resource-blade.png)

此外，还可以通过选择“所有服务”  ，并在筛选器文本框中键入“资源运行状况”  来访问“资源运行状况”。 在“帮助 + 支持”  窗格中，选择 [资源运行状况](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth)。

![通过“所有服务”打开“资源运行状况”](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>后续步骤

查看这些参考资料, 详细了解资源运行状况:
-  [Azure 资源运行状况中的资源类型和运行状况检查](resource-health-checks-resource-types.md)
-  [有关 Azure 资源运行状况的常见问题解答](resource-health-faq.md)
