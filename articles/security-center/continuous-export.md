---
title: 将 Azure 安全中心警报和建议导出到 Siem |Microsoft Docs
description: 本文介绍如何将安全警报和建议的连续导出设置为 Siem
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: memildin
ms.openlocfilehash: 4d5cff416c1ac54e54d06e8def121db65bb7d191
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2020
ms.locfileid: "89433920"
---
# <a name="export-security-alerts-and-recommendations"></a>导出安全警报和建议

Azure 安全中心生成详细的安全警报和建议。 可以在门户中或通过编程工具查看它们。 你可能还需要导出此信息，或将其发送到你环境中的其他监视工具。 

本文介绍了一组工具，这些工具可用于手动或以持续、持续的方式导出警报和建议。

使用这些工具可以：

* 持续导出到 Log Analytics 工作区
* 持续导出到 Azure 事件中心 (与第三方 Siem) 的集成
* 导出到 CSV (一次) 



## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|正式版|
|计价|免费层|
|必需的角色和权限：|资源组上的**安全管理员角色** (或**所有者**) <br>还必须对目标资源具有写入权限|
|云：|![是](./media/icons/yes-icon.png) 商业云<br>![是](./media/icons/yes-icon.png) US Gov<br>![是](./media/icons/yes-icon.png) 中国 Gov (到事件中心) ，其他 Gov|
|||



## <a name="set-up-a-continuous-export"></a>设置连续导出

无论是将连续导出设置为 Log Analytics 工作区还是 Azure 事件中心，都需要执行以下步骤。

1. 从安全中心的边栏中，选择 " **定价 & 设置**"。

1. 选择要为其配置数据导出的特定订阅。
    
1. 在该订阅的 "设置" 页的侧栏中，选择 " **连续导出**"。

    [ ![ 在 Azure 安全中心的 "导出选项" 中](media/continuous-export/continuous-export-options-page.png)](media/continuous-export/continuous-export-options-page.png#lightbox)，可以看到导出选项。 每个可用的导出目标有一个选项卡。 

1. 选择要导出的数据类型，然后从每种类型的筛选器中进行选择 (例如，仅导出高严重性警报) 。

1. （可选）如果你的选择包含以下四个建议中的一个，你可以将漏洞评估发现与它们一起包括：

    - 应修正 SQL 数据库上的漏洞评估结果
    - 计算机上的 SQL server 上的漏洞评估结果应 (预览版进行修正) 
    - 应修正 Azure 容器注册表映像中的漏洞（由 Qualys 提供技术支持）
    - 应修正虚拟机中的漏洞

    若要在这些建议中包含发现，请启用 " **包括安全发现** " 选项。

    :::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="在连续导出配置中包括安全发现切换" :::


1. 从 "导出目标" 区域中，选择要将数据保存到的位置。 数据可以保存在不同订阅的目标中 (例如，在中央事件中心实例或中心 Log Analytics 工作区) 上保存。

1. 选择“保存”。


## <a name="set-up-continuous-export-via-the-rest-api"></a>通过 REST API 设置连续导出

可以通过 Azure 安全中心 [自动化 API](https://docs.microsoft.com/rest/api/securitycenter/automations)配置和管理连续导出功能。 使用此 API 创建或更新自动化，以便导出到以下任何可能的目标：

- Azure 事件中心
- Log Analytics 工作区
- Azure 逻辑应用 

API 提供 Azure 门户中没有的其他功能，例如：

* **更大的卷** -API 允许在单个订阅上创建多个导出配置。 安全中心门户 UI 中的 **连续导出** 页面仅支持每个订阅一个导出配置。

* **其他功能** -API 提供 UI 中未显示的其他参数。 例如，你可以将标记添加到自动化资源，并根据一组更广泛的警报和建议属性定义导出，而不是在安全中心的门户 UI 中的 **连续导出** 页面中提供。

* **更具针对性的作用域** -API 为导出配置的作用域提供更精细的级别。 使用 API 定义导出时，可以在资源组级别执行此操作。 如果在安全中心的门户 UI 中使用 **连续导出** 页面，则必须在订阅级别定义它。

    > [!TIP]
    > 如果使用 API 设置了多个导出配置，或者如果使用了仅限 API 的参数，则这些额外功能不会显示在安全中心 UI 中。 相反，会出现一个横幅，通知您存在其他配置。

在 [REST API 文档](https://docs.microsoft.com/rest/api/securitycenter/automations)中了解有关自动化 API 的详细信息。



## <a name="configure-siem-integration-via-azure-event-hubs"></a>通过 Azure 事件中心配置 SIEM 集成

Azure 事件中心是一种很好的解决方案，可用于以编程方式使用任何流数据。 对于 Azure 安全中心警报和建议，这是与第三方 SIEM 集成的首选方法。

> [!NOTE]
> 在大多数情况下，将监视数据流式传输到外部工具的最有效方法是使用 Azure 事件中心。 [本文](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs) 简要介绍了如何将来自不同源的监视数据流式传输到事件中心，并提供详细指南链接。

> [!NOTE]
> 如果以前使用 Azure 活动日志将安全中心警报导出到 SIEM，则以下过程将取代该方法。

若要查看导出的数据类型的事件架构，请访问 [事件中心事件架构](https://aka.ms/ASCAutomationSchemas)。


### <a name="to-integrate-with-a-siem"></a>与 SIEM 集成 

将所选安全中心数据连续导出到 Azure 事件中心后，可以为 SIEM 设置相应的连接器：

* **Azure Sentinel** -使用此处提供的本机 Azure 安全中心警报 [数据连接器](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center) 。
* **Splunk** -使用 [Splunk 的 Azure Monitor 外接程序](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md)
* **IBM QRadar** -使用 [手动配置的日志源](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/t_dsm_guide_microsoft_azure_enable_event_hubs.html)
* **ArcSight** –使用 [SmartConnector](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)

此外，如果你想要从已配置的事件中心自动将连续导出的数据移至 Azure 数据资源管理器，请按照将 [数据从事件中心引入 azure 数据资源管理器](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub)中的说明进行操作。



## <a name="continuous-export-to-a-log-analytics-workspace"></a>连续导出到 Log Analytics 工作区

如果要在 Log Analytics 工作区内分析 Azure 安全中心数据或将 Azure 警报与安全中心一起使用，请将 "连续导出" 设置为 Log Analytics 工作区。

若要导出到 Log Analytics 工作区，必须在工作区中启用安全中心的 Log Analytics 解决方案。 如果你使用的是 Azure 门户，则当你启用连续导出时，将自动启用安全中心的免费层解决方案。 但是，如果要以编程方式配置连续导出设置，则必须从 **定价 & 设置**中手动选择所需工作区的 "免费" 或 "标准" 定价层。  

### <a name="log-analytics-tables-and-schemas"></a>Log Analytics 表和架构

安全警报和建议分别存储在 *SecurityAlert* 和 *SecurityRecommendations* 表中。 包含这些表的 Log Analytics 解决方案的名称取决于你是在 "免费" 层还是 "标准" 层 (请参阅 [定价](security-center-pricing.md)) ：安全 ( "安全和审核" ) 或 SecurityCenterFree。

![Log Analytics 中的 * SecurityAlert * 表](./media/continuous-export/log-analytics-securityalert-solution.png)

若要查看导出的数据类型的事件架构，请访问 [Log Analytics 表架构](https://aka.ms/ASCAutomationSchemas)。

###  <a name="view-exported-security-alerts-and-recommendations-in-azure-monitor"></a>在 Azure Monitor 中查看导出的安全警报和建议

在某些情况下，你可以选择在 [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)中查看导出的安全警报和/或建议。 

Azure Monitor 提供了一种统一的警报体验，包括诊断日志、指标警报以及基于 Log Analytics 工作区查询的自定义警报。

若要在 Azure Monitor 中查看安全中心的警报和建议，请根据 Log Analytics 查询 (日志警报) 来配置警报规则：

1. 在 Azure Monitor 的 " **警报** " 页中，选择 " **新建警报规则**"。

    ![Azure Monitor 的 "警报" 页](./media/continuous-export/azure-monitor-alerts.png)

1. 在 "创建规则" 页中，配置新的规则 (采用 Azure Monitor) 中配置 [日志警报规则](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log) 的相同方式：

    * 对于 " **资源**"，请选择要向其中导出安全警报和建议的 "Log Analytics" 工作区。

    * 对于 " **条件**"，选择 " **自定义日志搜索**"。 在出现的页中，配置查询、lookback 周期和频率时间段。 在搜索查询中，你可以键入 *SecurityAlert* 或 *SecurityRecommendation* ，以在你启用连续导出到 Log Analytics 功能时查询安全中心持续导出到的数据类型。 
    
    * 还可以配置要触发的 [操作组](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) 。 操作组可以触发电子邮件发送、ITSM 票证、Webhook 等。
    ![Azure Monitor 预警规则](./media/continuous-export/azure-monitor-alert-rule.png)

现在，你将看到新的 Azure 安全中心警报或建议 (根据 Azure Monitor 警报中的配置) ，如果) 提供了 (，则会自动触发操作组。

## <a name="manual-one-time-export-of-security-alerts"></a>手动一次性导出安全警报

若要为警报或建议下载 CSV 报表，请打开 " **安全警报** " 或 " **建议** " 页，然后选择 " **下载 CSV 报表** " 按钮。

[![将警报数据作为 CSV 文件下载](media/continuous-export/download-alerts-csv.png)](media/continuous-export/download-alerts-csv.png#lightbox)

> [!NOTE]
> 这些报表包含当前所选订阅中的资源的警报和建议。



## <a name="faq---continuous-export"></a>常见问题解答-连续导出

### <a name="what-are-the-costs-involved-in-exporting-data"></a>导出数据所涉及的成本是多少？

启用连续导出不会产生费用。 根据你的配置，在 Log Analytics 工作区中引入和保留数据可能会产生成本。 

了解 [Log Analytics 工作区定价](https://azure.microsoft.com/pricing/details/monitor/)的详细信息。

详细了解 [Azure 事件中心定价](https://azure.microsoft.com/pricing/details/event-hubs/)。


## <a name="next-steps"></a>后续步骤

本文介绍了如何配置建议和警报的连续导出。 还了解了如何将警报数据作为 CSV 文件下载。 

有关相关材料，请参阅以下文档： 

- [Azure 事件中心文档](https://docs.microsoft.com/azure/event-hubs/)
- [Azure Sentinel 文档](https://docs.microsoft.com/azure/sentinel/)
- [Azure Monitor 文档](https://docs.microsoft.com/azure/azure-monitor/)
- [工作流自动化和连续导出数据类型架构](https://aka.ms/ASCAutomationSchemas)
