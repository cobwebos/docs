---
title: 将 Azure 监视数据流式传输到事件中心
description: 了解如何将 Azure 监视数据流式传输到事件中心，以将数据获取到合作伙伴 SIEM 或分析工具。
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 915df5d6356e2246c8937cb167c8068b00e0917b
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82854618"
---
# <a name="stream-azure-monitoring-data-to-an-event-hub"></a>将 Azure 监视数据流式传输到事件中心
Azure Monitor 为 Azure、其他云和本地的应用程序与服务提供全堆栈监视解决方案。 除了使用 Azure Monitor 分析数据并将数据用于不同的监视方案以外，可能还需要将其发送到环境中的其他监视工具。 在大多数情况下，将监视数据流式传输到外部工具的最有效方法是使用 [Azure 事件中心](/azure/event-hubs/)。 本文简要介绍了如何将不同源中的监视数据流式传输到事件中心，并提供详细指南的链接。


## <a name="create-an-event-hubs-namespace"></a>创建事件中心命名空间

在针对任何数据源配置流式传输之前，需要[创建事件中心命名空间和事件中心](../../event-hubs/event-hubs-create.md)。 此命名空间和事件中心是所有监视数据的目标。 事件中心命名空间是共享相同访问策略的事件中心的逻辑分组，就像存储帐户中有各个 blob 一样。 请注意以下有关用于流式传输监视数据的事件中心命名空间和事件中心的详细信息：

* 使用吞吐量单位数，可增加事件中心的吞吐量规模。 通常只需要一个吞吐量单位。 如果需要在日志使用量增加时纵向扩展，可以手动增加命名空间的吞吐量单位数或启用自动扩充。
* 使用分区数可以在多个使用者之间并行使用。 单个分区最多支持 20MBps，或者大约每秒 20,000 条消息。 不一定支持从多个分区使用，具体取决于使用数据的工具。 如果你不确定要设置的分区数，则可以从四个分区开始。
* 将事件中心的消息保留期设置为至少 7 天。 如果使用的工具多天出现故障，这可确保该工具可以从它中断的位置重新开始（因为事件最多可保存 7 天）。
* 应该对事件中心使用默认的使用者组。 除非你打算使用两个不同的工具使用同一事件中心内的相同数据，否则无需创建其他使用者组或使用单独的使用者组。
* 对于 Azure 活动日志，可选择事件中心命名空间，Azure Monitor 将在该命名空间内创建名为 _insights-logs-operational-logs_ 的事件中心。 对于其他日志类型，可以选择现有的事件中心，或者让 Azure Monitor 为每个日志类别创建一个事件中心。
* 通常，必须在使用事件中心数据的计算机或 VNET 中上打开端口 5671 和 5672。

## <a name="monitoring-data-available"></a>提供的监视数据
[Azure Monitor 的监视数据源](data-sources.md)介绍了 Azure 应用程序的各种数据层，以及为每个层提供的监视数据类型。 下表列出了每个层，并描述了如何将该数据流式传输到事件中心。 可单击提供的链接了解更多详细信息。

| 层 | data | 方法 |
|:---|:---|:---|
| [Azure 租户](data-sources.md#azure-tenant) | Azure Active Directory 审核日志 | 在 AAD 租户上配置租户诊断设置。 有关详细信息，请参阅[教程：将 Azure Active Directory 日志流式传输到 Azure 事件中心](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)。 |
| [Azure 订阅](data-sources.md#azure-subscription) | Azure 活动日志 | 创建日志配置文件，以将活动日志事件导出到事件中心。  有关详细信息，请参阅[将 azure 平台日志流式传输到 Azure 事件中心](resource-logs-stream-event-hubs.md)。 |
| [Azure 资源](data-sources.md#azure-resources) | 平台指标<br> 资源日志 |使用资源诊断设置将两种类型的数据发送到事件中心。 有关详细信息，请参阅[将 Azure 资源日志流式传输到事件中心](resource-logs-stream-event-hubs.md)。 |
| [操作系统（来宾）](data-sources.md#operating-system-guest) | Azure 虚拟机 | 在 Azure 中的 Windows 和 Linux 虚拟机上安装 [Azure 诊断扩展](diagnostics-extension-overview.md)。 有关 Windows VM 的详细信息，请参阅[使用事件中心流式传输热路径中的 Azure 诊断数据](diagnostics-extension-stream-event-hubs.md)；有关 Linux VM 的详细信息，请参阅[使用 Linux 诊断扩展监视指标和日志](../../virtual-machines/extensions/diagnostics-linux.md#protected-settings)。 |
| [应用程序代码](data-sources.md#application-code) | Application Insights | Application Insights 不提供直接方法用于将数据流式传输到事件中心。 可以设置将 Application Insights 数据[连续导出](../../azure-monitor/app/export-telemetry.md)到存储帐户，然后根据[使用逻辑应用手动进行流式传输](#manual-streaming-with-logic-app)中所述，使用逻辑应用将数据发送到事件中心。 |

## <a name="manual-streaming-with-logic-app"></a>使用逻辑应用手动进行流式传输
对于无法直接流式传输到事件中心的数据，可以写入 Azure 存储，然后使用[从 blob 存储提取数据](../../connectors/connectors-create-api-azureblobstorage.md#add-action)并将[其作为消息推送到事件中心](../../connectors/connectors-create-api-azure-event-hubs.md#add-action)的时间触发的逻辑应用。 


## <a name="partner-tools-with-azure-monitor-integration"></a>与 Azure Monitor 集成的合作伙伴工具

通过 Azure Monitor 将监视数据路由到事件中心，可与外部 SIEM 和监视工具轻松集成。 与 Azure Monitor 集成的工具示例包括：

| 工具 | 在 Azure 中托管 | 说明 |
|:---|:---| :---|
|  IBM QRadar | 否 | Microsoft Azure DSM 和 Microsoft Azure 事件中心协议可从 [IBM 支持网站](https://www.ibm.com/support)下载。 可以在 [QRadar DSM 配置](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0)中详细了解与 Azure 的集成。 |
| Splunk | 否 | [适用于 Splunk 的 Azure Monitor 加载项](https://splunkbase.splunk.com/app/3534/)是在 Splunkbase 中提供的一个开源项目。 相关文档已在 [适用于 Splunk 的 Azure Monitor 加载项](https://github.com/Microsoft/AzureMonitorAddonForSplunk/wiki/Azure-Monitor-Addon-For-Splunk)中提供。<br><br> 如果无法在 Splunk 实例中安装加载项（例如，如果使用代理或在 Splunk Cloud 上运行），则可以使用[适用于 Splunk 的 Azure 函数](https://github.com/Microsoft/AzureFunctionforSplunkVS)（由事件中心内的新消息触发）将这些事件转发到 Splunk HTTP 事件收集器。 |
| SumoLogic | 否 | [从事件中心收集 Azure 审核应用的日志](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub)中提供了有关设置 SumoLogic，以使用事件中心数据的说明。 |
| ArcSight | 否 | ArcSight Azure 事件中心智能连接器作为 [ArcSight 智能连接器集合](https://community.softwaregrp.com/t5/Discussions/Announcing-General-Availability-of-ArcSight-Smart-Connectors-7/m-p/1671852)的一部分提供。 |
| Syslog 服务器 | 否 | 若要将 Azure Monitor 数据直接流式传输到 syslog 服务器，可以使用[基于 Azure 函数的解决方案](https://github.com/miguelangelopereira/azuremonitor2syslog/)。
| LogRhythm | 否| [此处](https://logrhythm.com/six-tips-for-securing-your-azure-cloud-environment/)提供了有关设置 LogRhythm，以从事件中心收集日志的说明。 
|Logz.io | 是 | 有关详细信息，请参阅[开始使用用于在 Azure 上运行的 Java 应用的 Logz.io 进行监视和日志记录](https://docs.microsoft.com/azure/developer/java/fundamentals/java-get-started-with-logzio)


## <a name="next-steps"></a>后续步骤
* [将活动日志存档到存储帐户](../../azure-monitor/platform/archive-activity-log.md)
* [阅读 Azure 活动日志概述](../../azure-monitor/platform/platform-logs-overview.md)
* [根据活动日志事件设置警报](../../azure-monitor/platform/alerts-log-webhook.md)


