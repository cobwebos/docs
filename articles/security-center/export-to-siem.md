---
title: 将警报从 Azure 安全中心流式传输到安全信息和事件管理 (SIEM) 系统和其他监视解决方案
description: 了解如何将安全警报流式传输到 Azure Sentinel、第三方 Siem、之忠诚度或 ITSM 解决方案
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 10/06/2020
ms.author: memildin
ms.openlocfilehash: 0800c0d6fb2cf57b919d29ac354d2d89c06c7aeb
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946602"
---
# <a name="stream-alerts-to-a-siem-soar-or-it-service-management-solution"></a>将警报流式传输到 SIEM、之忠诚度或 IT 服务管理解决方案

Azure 安全中心可以将安全警报流式传输到最常见的安全信息和事件管理中， (SIEM) 、安全业务流程自动响应 (之忠诚度) 和 IT 服务管理 (ITSM) 解决方案。

Azure 本机工具可确保你可以查看当前使用的所有最常用解决方案中的警报数据，其中包括：

- **Azure Sentinel**
- **Splunk Enterprise and Splunk Cloud**
- **IBM 的 QRadar**
- **ServiceNow**
- **ArcSight**
- **Power BI**
- **Palo Alto Networks**

## <a name="stream-alerts-to-azure-sentinel"></a>将警报流式传输到 Azure Sentinel 

安全中心本机集成了 Azure Sentinel、Azure 的云本机 SIEM 和之忠诚度解决方案。 

[了解有关 Azure Sentinel 的详细信息](../sentinel/overview.md)。

### <a name="azure-sentinels-connectors-for-security-center"></a>用于安全中心的 Azure Sentinel 连接器

Azure Sentinel 包括 Azure 安全中心在订阅和租户级别的内置连接器：

- [在订阅级别将警报流式传输到 Azure Sentinel](../sentinel/connect-azure-security-center.md)
- [将租户中的所有订阅连接到 Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-security-center-auto-connect-to-sentinel/ba-p/1387539) 

### <a name="configure-ingestion-of-all-audit-logs-into-azure-sentinel"></a>配置将所有审核日志引入 Azure Sentinel 

调查 Azure Sentinel 中的安全中心警报的另一种方法是将审核日志流式传输到 Azure Sentinel：
    - [连接 Windows 安全事件](../sentinel/connect-windows-security-events.md)
    - [使用 Syslog 从基于 Linux 的源收集数据](../sentinel/connect-syslog.md)
    - [连接 Azure 活动日志中的数据](../sentinel/connect-azure-activity.md)

> [!TIP]
> Azure Sentinel 根据 Azure Sentinel 中用于分析的数据引入量计费，并存储在 "Azure Monitor Log Analytics" 工作区中。 Azure Sentinel 提供灵活且可预测的定价模型。 有关详细信息，请参阅[Azure Sentinel 定价页](https://azure.microsoft.com/pricing/details/azure-sentinel/)。


## <a name="stream-alerts-with-microsoft-graph-security-api"></a>Microsoft Graph 安全 API 流式传输警报

安全中心提供与 Microsoft Graph 安全 API 的现成集成。 无需进行配置，也不需要额外的费用。 

你可以使用此 API 将来自你的 **整个租户** (和数据中的警报流式传输) 到第三方 siem 和其他常用平台：

- **Splunk Enterprise And Splunk Cloud**  - [将 Microsoft Graph 安全 API Add-On 用于 Splunk](https://splunkbase.splunk.com/app/4564/) 
- **Power BI**  - [连接到 Power BI Desktop 中的 Microsoft Graph 安全 API](https://docs.microsoft.com/power-bi/connect-data/desktop-connect-graph-security)
- **ServiceNow**  - [按照说明从 ServiceNow 存储安装和配置 Microsoft Graph 安全 API 应用程序](https://docs.servicenow.com/bundle/orlando-security-management/page/product/secops-integration-sir/secops-integration-ms-graph/task/ms-graph-install.html)
- **QRadar**  - [Azure 安全中心的 IBM 设备支持模块（通过 MICROSOFT GRAPH API](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/c_dsm_guide_ms_azure_security_center_overview.html) ） 
- **Palo Alto 网络**、 **Anomali**、 **Lookout**、 **InSpark**和更 [Microsoft Graph 安全 API](https://www.microsoft.com/security/business/graph-security-api#office-MultiFeatureCarousel-09jr2ji)

[详细了解 Microsoft Graph 安全 API](https://www.microsoft.com/security/business/graph-security-api)。


## <a name="stream-alerts-with-azure-monitor"></a>Azure Monitor 传输警报 

将警报流式传输到 **ArcSight**、 **Splunk**、 **SumoLogic**、Syslog 服务器、 **LogRhythm**、 **Logz.io Cloud 可观察性平台**和其他监视解决方案。 通过 Azure 事件中心将安全中心与 Azure monitor 连接：

1. 启用 [连续导出](continuous-export.md) 以在订阅级别将安全中心警报流式传输到专用 Azure 事件中心。 
    > [!TIP]
    > 若要在管理组级别使用 Azure 策略执行此操作，请参阅 [按比例创建连续导出自动化配置](continuous-export.md?tabs=azure-policy#configure-continuous-export-at-scale-using-the-supplied-policies)

1. [使用 Azure Monitor 的内置连接器将 Azure 事件中心连接到首选解决方案](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)。

1. （可选）将原始日志流式传输到 Azure 事件中心并连接到首选解决方案。 在 [监视数据可用](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#monitoring-data-available)中了解详细信息。

> [!TIP]
> 若要查看导出的数据类型的事件架构，请访问[事件中心事件架构](https://aka.ms/ASCAutomationSchemas)。


## <a name="next-steps"></a>后续步骤

本页面介绍了如何确保你的 Azure 安全中心警报数据在所选的 SIEM、之忠诚度或 ITSM 工具中可用。 有关相关材料，请参阅：

- [什么是 Azure Sentinel？](../sentinel/overview.md)
- [Azure 安全中心的警报验证](security-center-alert-validation.md) -验证是否正确配置了警报
- [持续导出安全警报和建议](continuous-export.md)