---
title: 在 Azure 安全中心集成安全解决方案 | Microsoft Docs
description: 了解如何将 Azure 安全中心与合作伙伴集成，以增强 Azure 资源的总体安全性。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2019
ms.author: memildin
ms.openlocfilehash: 435ae9f08f718a9310fd1687fb7859058edf8b45
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384246"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>在 Azure 安全中心集成安全解决方案
本文档介绍如何管理已连接到 Azure 安全中心的安全解决方案，以及如何添加新的安全解决方案。

> [!NOTE]
> A subset of security solutions has been retired on July 31st, 2019. For more information and alternative services, see [Retirement of Security Center features (July 2019)](security-center-features-retirement-july2019.md#menu_solutions).

## <a name="integrated-azure-security-solutions"></a>集成式 Azure 安全解决方案
可以通过安全中心轻松地在 Azure 中启用集成式安全解决方案。 优势包括：

- **简化部署**：安全中心提供对集成式合作伙伴解决方案的简化预配。 For solutions like antimalware and vulnerability assessment, Security Center can provision the agent on your virtual machines. For firewall appliances, Security Center can take care of much of the network configuration required.
- **集成检测**：自动收集、聚合合作伙伴解决方案中的安全事件，并将其作为安全中心警报和事件的一部分进行显示。 这些事件还与来自其他源的检测融合在一起，以提供高级威胁检测功能。
- **统一运行状况监视和管理**：利用集成运行状况事件，客户可以一目了然地监视所有合作伙伴解决方案。 可通过使用合作伙伴解决方案轻松地访问高级设置，进行基本管理。

Currently, integrated security solutions include vulnerability assessment by [Qualys](https://www.qualys.com/public-cloud/#azure) and [Rapid7](https://www.rapid7.com/products/insightvm/) and Microsoft Application Gateway Web application firewall.

> [!NOTE]
> Security Center does not install the Microsoft Monitoring Agent on partner virtual appliances because most security vendors prohibit external agents running on their appliances.
>
>

## <a name="how-security-solutions-are-integrated"></a>安全中心如何集成
从安全中心部署的 Azure 安全解决方案是自动连接的。 You can also connect other security data sources, including computers running on-premises or in other clouds.

![合作伙伴解决方案集成](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>管理集成式 Azure 安全解决方案和其他数据源

1. 登录到 [Azure 门户](https://azure.microsoft.com/features/azure-portal/)。

2. 在 **Microsoft Azure 菜单**上选择“安全中心”。 此时会打开“安全中心 - 概览”。

3. 在“安全中心”菜单下，选择“安全解决方案”。

   ![安全中心概述](./media/security-center-partner-integration/overview.png)

In **Security solutions**, you can see the health of integrated Azure security solutions and run basic management tasks.

### <a name="connected-solutions"></a>已连接解决方案

The **Connected solutions** section includes security solutions that are currently connected to Security Center. It also shows the health status of each solution.  

![已连接解决方案](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

合作伙伴解决方案的状态可能为：

* Healthy (green) - no health issues.
* Unhealthy (red) - there's a health issue that requires immediate attention.
* 运行状况问题（橙色）- 解决方案已停止报告其运行状况。
* Not reported (gray) - the solution hasn't reported anything yet and no health data is available. A solution's status may be unreported if it was connected recently and is still deploying.

> [!NOTE]
> 如果没有运行状况数据可用，则安全中心会显示上次收到事件的日期和时间以指示解决方案是否正在报告。 If no health data is available and no alerts were received within the last 14 days, Security Center indicates that the solution is unhealthy or not reporting.
>
>

1. Select **VIEW** for additional information and options such as:

   - **解决方案控制台**。 打开此解决方案的管理体验。
   - **链接 VM**。 Opens the Link Applications page. 此处，可将资源连接到合作伙伴解决方案。
   - **删除解决方案**。
   - **配置**。

   ![合作伙伴解决方案详细信息](./media/security-center-partner-solutions/partner-solutions-detail.png)

### <a name="discovered-solutions"></a>已发现解决方案

Security Center automatically discovers security solutions running in Azure but not connected to Security Center and displays the solutions in the **Discovered solutions** section. These  solutions include Azure solutions, like [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection), and partner solutions.

> [!NOTE]
> 在订阅级别，安全中心标准层是已发现解决方案功能所必需的。 See [Pricing](security-center-pricing.md) to learn more about the pricing tiers.
>
>

Select **CONNECT** under a solution to integrate with Security Center and be notified of security alerts.

### <a name="add-data-sources"></a>添加数据源

“添加数据源”部分包括其他可以连接的可用数据源。 如需从任何此类源添加数据的说明，请单击“添加”。

![数据源](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)

## <a name="exporting-data-to-a-siem"></a>将数据导出到 SIEM

You can configure your SIEMs or other monitoring tools to receive Azure Security Center events.

All events from Azure Security Center are published to Azure Monitor's Azure [Activity log](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md). Azure Monitor uses [a consolidated pipeline](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) to  stream the data to an Event Hub where it can then be pulled into your monitoring tool.

以下各节介绍如何将数据配置为流式传输到事件中心。 步骤假设 Azure 订阅中已配置了 Azure 安全中心。

### <a name="high-level-overview"></a>综合概述

![高级概述](media/security-center-export-data-to-siem/overview.png)

### <a name="what-is-the-azure-security-data-exposed-to-siem"></a>公开给 SIEM 的 Azure 安全数据是什么？

In this version, we expose the [security alerts.](../security-center/security-center-managing-and-responding-alerts.md) 在即将发布的版本中，我们将通过安全建议丰富数据集。

### <a name="how-to-set-up-the-pipeline"></a>How to set up the pipeline

#### <a name="create-an-event-hub"></a>创建事件中心

Before you begin, [create an Event Hubs namespace](../event-hubs/event-hubs-create.md) - the destination for all your monitoring data.

#### <a name="stream-the-azure-activity-log-to-event-hubs"></a>将 Azure 活动日志流式传输到事件中心

See the following article [stream activity log to Event Hubs](../azure-monitor/platform/activity-logs-stream-event-hubs.md)

#### <a name="install-a-partner-siem-connector"></a>安装合作伙伴 SIEM 连接器 

通过 Azure Monitor 将监视数据路由到事件中心，可与合作伙伴 SIEM 和监视工具轻松集成。

See the following article for the list of [supported SIEMs](../azure-monitor/platform/resource-logs-stream-event-hubs.md#what-you-can-do-with-resource-logs-sent-to-an-event-hub)

### <a name="example-for-querying-data"></a>查询数据示例 

Here are some Splunk queries you can use to pull alert data:

| **查询说明** | **查询** |
|----|----|
| 所有警报| index=main Microsoft.Security/locations/alerts|
| 按名称汇总操作计数| index=main sourcetype="amal:security" \| table operationName \| stats count by operationName|
| 获取警报信息：时间、名称、状态、ID 和订阅 | index=main Microsoft.Security/locations/alerts \| table \_time, properties.eventName, State, properties.operationId, am_subscriptionId |


## <a name="next-steps"></a>后续步骤

本文介绍了如何在安全中心集成合作伙伴的解决方案。 若要详细了解安全中心，请参阅以下文章：

* [在安全中心进行安全运行状况监视](security-center-monitoring.md)。 了解如何监视 Azure 资源的运行状况。
* [Azure 安全中心常见问题解答](security-center-faq.md)。 获取安全中心使用方面的常见问题解答。
* [Azure 安全性博客](https://blogs.msdn.com/b/azuresecurity/)。 查找关于 Azure 安全性及合规性的博客文章。
