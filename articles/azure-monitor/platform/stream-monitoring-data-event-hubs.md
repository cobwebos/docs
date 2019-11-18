---
title: 将 Azure 监视数据流式传输到事件中心
description: 了解如何将 Azure 监视数据流式传输到事件中心，以将数据获取到合作伙伴 SIEM 或分析工具。
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 84cb2b465735532ff44e35ab7a2fe4e9bb224e61
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150016"
---
# <a name="stream-azure-monitoring-data-to-an-event-hub"></a>将 Azure 监视数据流式传输到事件中心
Azure Monitor 提供适用于 Azure 中的应用程序和服务、其他云和本地的完整堆栈监视解决方案。 除了使用 Azure Monitor 分析数据并将其用于不同的监视方案，你可能需要将其发送到环境中的其他监视工具。 在大多数情况下，最有效的方法是将监视数据流式处理到外部工具使用[Azure 事件中心](/azure/event-hubs/)。 本文简要介绍了如何将来自不同源的监视数据流式传输到事件中心，并提供详细指南链接。


## <a name="create-an-event-hubs-namespace"></a>创建事件中心命名空间

在为任何数据源配置流式处理之前，需要[创建一个事件中心命名空间和事件中心](../../event-hubs/event-hubs-create.md)。 此命名空间和事件中心是所有监视数据的目标。 事件中心命名空间是共享相同访问策略的事件中心的逻辑分组，就像存储帐户中有各个 blob 一样。 请考虑以下有关用于流式监视数据的事件中心命名空间和事件中心的详细信息：

* 使用吞吐量单位数，可增加事件中心的吞吐量规模。 通常只需要一个吞吐量单位。 如果需要在日志使用量增加时进行扩展，可以手动增加命名空间的吞吐量单位数，或启用自动膨胀。
* 使用分区数可以在多个使用者之间并行使用。 单个分区每秒最多可以支持20MBps 或约20000条消息。 不一定支持从多个分区使用，具体取决于使用数据的工具。 如果您不确定是否确定要设置的分区数，则必须启动四个分区。
* 你将事件中心上的消息保留至少设置为7天。 如果你使用的工具的使用时间超过一天，则这可确保该工具可以从最多7天的事件中的事件中断位置进行选择。
* 你应使用事件中心的默认使用者组。 除非你打算使用两个不同的工具使用同一事件中心内的相同数据，否则无需创建其他使用者组或使用单独的使用者组。
* 对于 Azure 活动日志，选择一个事件中心命名空间，并 Azure Monitor 在该命名空间中创建一个名为 " _insights-日志-操作-_ 日志" 的事件中心。 对于其他日志类型，可以选择现有的事件中心，或让 Azure Monitor 按日志类别创建事件中心。
* 通常，在使用事件中心的数据的计算机或 VNET 上，必须打开出站端口5671和5672。


## <a name="monitoring-data-available"></a>可用的监视数据
[Azure Monitor 的监视数据源](data-sources.md)介绍了适用于 Azure 应用程序的数据层和每个数据的监视数据的不同层。 下表列出了每个层，并说明了如何将数据流式传输到事件中心。 请按照提供的链接了解更多详细信息。

| 层 | 数据 | 方法 |
|:---|:---|:---|
| [Azure 租户](data-sources.md#azure-tenant) | Azure Active Directory 审核日志 | 在 AAD 租户上配置租户诊断设置。 有关详细信息，请参阅[教程：将 Azure Active Directory 日志流式传输到 Azure 事件中心](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)。 |
| [Azure 订阅](data-sources.md#azure-subscription) | Azure 活动日志 | 创建日志配置文件，将活动日志事件导出到事件中心。  有关详细信息，请参阅[将 Azure 活动日志导出到存储或 Azure 事件中心](activity-log-export.md)。 |
| [Azure 资源](data-sources.md#azure-resources) | 平台指标<br>诊断日志 |使用资源诊断设置将两种类型的数据发送到事件中心。 有关详细信息，请参阅[将 Azure 诊断日志流式传输到事件中心](resource-logs-stream-event-hubs.md)。 |
| [操作系统（来宾）](data-sources.md#operating-system-guest) | Azure 虚拟机 | 在 Azure 中的 Windows 和 Linux 虚拟机上安装[Azure 诊断扩展](diagnostics-extension-overview.md)。 有关 Windows Vm 的详细信息，请参阅[使用事件中心流式传输 Azure 诊断热路径中的数据](diagnostics-extension-stream-event-hubs.md)，并[使用 linux 诊断扩展监视指标和日志](../../virtual-machines/extensions/diagnostics-linux.md#protected-settings)以了解 Linux vm 的详细信息。 |
| [应用程序代码](data-sources.md#application-code) | Application Insights | Application Insights 不提供直接方法将数据流式传输到事件中心。 可以将 Application Insights 数据[连续导出](../../azure-monitor/app/export-telemetry.md)到存储帐户，然后使用逻辑应用将数据发送到事件中心，如[使用逻辑应用进行手动流式传输](#manual-streaming-with-logic-app)中所述。 |

## <a name="manual-streaming-with-logic-app"></a>通过逻辑应用进行手动流式处理
对于无法直接流式传输到事件中心的数据，可以写入 Azure 存储，然后使用[从 blob 存储提取数据](../../connectors/connectors-create-api-azureblobstorage.md#add-action)并将[其作为消息推送到事件中心](../../connectors/connectors-create-api-azure-event-hubs.md#add-action)的时间触发的逻辑应用。 


## <a name="tools-with-azure-monitor-integration"></a>Azure Monitor 集成的工具

使用 Azure Monitor 将监视数据路由到事件中心，可以轻松地与外部 SIEM 和监视工具集成。 Azure Monitor 集成的工具示例包括：

| 工具 | 说明 |
|:---|:---|
|  IBM QRadar | Microsoft Azure DSM 和 Microsoft Azure 事件中心协议可从 [IBM 支持网站](https://www.ibm.com/support)下载。 可在[QRADAR DSM 配置](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0)中了解有关与 Azure 的集成的详细信息。 |
| Splunk | [Splunk 的 Azure Monitor 外接](https://splunkbase.splunk.com/app/3534/)程序是 Splunkbase 中可用的开放源代码项目。 [Splunk 的 Azure Monitor 加载](https://github.com/Microsoft/AzureMonitorAddonForSplunk/wiki/Azure-Monitor-Addon-For-Splunk)项中提供了文档。<br><br> 如果无法在 Splunk 实例中安装外接程序，如使用代理或在 Splunk 云上运行，则可以使用[Azure Function For Splunk](https://github.com/Microsoft/AzureFunctionforSplunkVS)（由事件中心中的新消息触发）将这些事件转发到 Splunk HTTP 事件收集器。 |
| SumoLogic | [从事件中心的 Azure 审核应用程序收集日志中](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub)提供了有关设置 SumoLogic 以使用事件中心数据的说明。 |
| ArcSight | ArcSight Azure 事件中心智能连接器作为[ArcSight 智能连接器集合](https://community.softwaregrp.com/t5/Discussions/Announcing-General-Availability-of-ArcSight-Smart-Connectors-7/m-p/1671852)的一部分提供。 |
| Syslog 服务器 | 如果要将 Azure Monitor 数据直接流式传输到 syslog 服务器，可以使用[基于 Azure 函数的解决方案](https://github.com/miguelangelopereira/azuremonitor2syslog/)。
| LogRhythm | [此处](https://logrhythm.com/six-tips-for-securing-your-azure-cloud-environment/)提供了设置 up LogRhythm 从事件中心收集日志的说明。 


## <a name="next-steps"></a>后续步骤
* [将活动日志存档到存储帐户](../../azure-monitor/platform/archive-activity-log.md)
* [阅读 Azure 活动日志概述](../../azure-monitor/platform/activity-logs-overview.md)
* [根据活动日志事件设置警报](../../azure-monitor/platform/alerts-log-webhook.md)


