---
title: 将 Azure 安全中心警报和建议导出到 SIEM |微软文档
description: 本文介绍如何设置安全警报和向 SIEM 提供的安全警报和建议的连续导出
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: memildin
ms.openlocfilehash: 19fdcc7b590c6ad6873c7808ae26d218bbda7f5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158957"
---
# <a name="export-security-alerts-and-recommendations"></a>导出安全警报和建议

Azure 安全中心生成详细的安全警报和建议。 您可以在门户中或通过编程工具查看它们。 您可能还需要导出此信息或将其发送到环境中的其他监视工具。 

本文介绍了允许您手动或持续导出警报和建议的工具集。

使用这些工具，您可以：

* 持续导出到日志分析工作区
* 持续导出到 Azure 事件中心（用于与第三方 SIEM 的集成）
* 导出到 CSV（一次）


## <a name="setting-up-a-continuous-export"></a>设置连续导出

无论是设置日志分析工作区还是 Azure 事件中心，都有必要执行以下步骤。

1. 从安全中心的侧边栏中，选择**定价&设置**。

1. 选择要为其配置数据导出的特定订阅。
    
1. 从该订阅的设置页的边栏中，选择 **"连续导出**"。

    [Azure 安全中心的导出选项![](media/continuous-export/continuous-export-options-page.png)](media/continuous-export/continuous-export-options-page.png#lightbox)在这里，您可以看到导出选项。 每个可用的导出目标都有一个选项卡。 

1. 选择要导出的数据类型，并从每种类型的筛选器中进行选择（例如，仅导出高严重性警报）。

1. 从"导出目标"区域中，选择希望将数据保存的位置。 数据可以保存在目标上不同的订阅（例如，在中央事件中心实例或中央日志分析工作区）。

1. 单击“保存”。****



## <a name="configuring-siem-integration-via-azure-event-hubs"></a>通过 Azure 事件中心配置 SIEM 集成

Azure 事件中心是程序化使用任何流数据的绝佳解决方案。 对于 Azure 安全中心警报和建议，它是与第三方 SIEM 集成的首选方式。

> [!NOTE]
> 在大多数情况下，将监视数据流式传输到外部工具的最有效方法是使用 Azure 事件中心。 [本文](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs)简要介绍了如何将监视数据从不同源流式传输到事件中心，以及指向详细指南的链接。

> [!NOTE]
> 如果以前使用 Azure 活动日志将安全中心警报导出到 SIEM，则下面的过程将替换该方法。

要查看导出数据类型的事件架构，请访问[事件中心事件架构](https://aka.ms/ASCAutomationSchemas)。


### <a name="to-integrate-with-a-siem"></a>与 SIEM 集成 

将所选安全中心数据连续导出到 Azure 事件中心后，可以为 SIEM 设置适当的连接器：

* **Azure 哨兵**- 使用本地 Azure 安全中心警报数据[连接器](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)。
* **Splunk** - 使用[Azure 监视器加载项进行斯普伦克](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md)
* **IBM QRadar** - 使用[手动配置的日志源](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/t_dsm_guide_microsoft_azure_enable_event_hubs.html)
* **弧线**视觉 – 使用[智能连接器](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)

此外，如果要将连续导出的数据自动从配置的事件中心移动到 Azure 数据资源管理器，请使用[将数据从事件中心引入到 Azure 数据资源管理器](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub)中的说明。



## <a name="continuous-export-to-a-log-analytics-workspace"></a>持续导出到日志分析工作区

如果要分析日志分析工作区内的 Azure 安全中心数据或与安全中心一起使用 Azure 警报，请设置连续导出到日志分析工作区。

要导出到日志分析工作区，必须在工作区上启用安全中心的日志分析解决方案。 如果使用 Azure 门户，则启用连续导出时，安全中心的免费层解决方案将自动启用。 但是，如果要以编程方式配置连续导出设置，则必须在**定价&设置**中手动选择所需工作区的免费或标准定价层。  

### <a name="log-analytics-tables-and-schemas"></a>日志分析表和架构

安全警报和建议分别存储在*安全警报**和安全建议*表中。 包含这些表的日志分析解决方案的名称取决于您是位于免费层还是标准层（请参阅[定价](security-center-pricing.md)）：安全性（"安全和审核"）还是安全中心免费。

![日志分析中的 [安全警报] 表](./media/continuous-export/log-analytics-securityalert-solution.png)

要查看导出数据类型的事件架构，请访问[日志分析表架构](https://aka.ms/ASCAutomationSchemas)。

###  <a name="view-exported-security-alerts-and-recommendations-in-azure-monitor"></a>在 Azure 监视器中查看导出的安全警报和建议

在某些情况下，您可以选择在[Azure 监视器](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)中查看导出的安全警报和/或建议。 

Azure 监视器为各种 Azure 警报提供了统一的警报体验，包括诊断日志、指标警报和基于日志分析工作区查询的自定义警报。

要查看 Azure 监视器中安全中心的警报和建议，请根据日志分析查询（日志警报）配置警报规则：

1. 在 Azure 监视器的**警报页中**，单击 **"新警报规则**"。

    ![Azure 监视器的警报页](./media/continuous-export/azure-monitor-alerts.png)

1. 在创建规则页中，配置新规则（与[在 Azure 监视器中配置日志警报规则](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)的方式相同）：

    * 对于**资源**，选择导出安全警报和建议的日志分析工作区。

    * 对于**条件**，选择**自定义日志搜索**。 在显示的页面中，配置查询、回望周期和频率周期。 在搜索查询中，可以键入*安全警报*或*安全建议*，以查询安全中心在启用"连续导出到日志分析"功能时连续导出到的数据类型。 
    
    * 或者，配置要触发[的操作组](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)。 操作组可以触发电子邮件发送、ITSM 票证、WebHook 等。
    ![Azure 监视器警报规则](./media/continuous-export/azure-monitor-alert-rule.png)

现在，您将在 Azure 监视器警报中看到新的 Azure 安全中心警报或建议（取决于您的配置），并自动触发操作组（如果提供）。

## <a name="manual-one-time-export-of-security-alerts"></a>手动一次性导出安全警报

要下载 CSV 报告以查找警报或建议，请打开**安全警报**或**建议**页面，然后单击 **"下载 CSV 报告**"按钮。

[![将警报数据下载为 CSV 文件](media/continuous-export/download-alerts-csv.png)](media/continuous-export/download-alerts-csv.png#lightbox)

> [!NOTE]
> 这些报告包含当前所选订阅中的资源的警报和建议。

## <a name="next-steps"></a>后续步骤

在本文中，您学习了如何配置建议和警报的连续导出。 您还学习了如何将警报数据下载为 CSV 文件。 

有关相关材料，请参阅以下文档： 

- [Azure 事件中心文档](https://docs.microsoft.com/azure/event-hubs/)
- [Azure Sentinel 文档](https://docs.microsoft.com/azure/sentinel/)
- [Azure Monitor 文档](https://docs.microsoft.com/azure/azure-monitor/)
- [工作流自动化和连续导出数据类型架构](https://aka.ms/ASCAutomationSchemas)
