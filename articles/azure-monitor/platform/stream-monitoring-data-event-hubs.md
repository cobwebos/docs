---
title: 将 Azure 监视数据流式传输到事件中心
description: 了解如何将 Azure 监视数据流式传输到事件中心，以将数据获取到合作伙伴 SIEM 或分析工具。
author: nkiest
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: nikiest
ms.subservice: ''
ms.openlocfilehash: 8a4de244d0fa07bfc162625f577015317fca7e6a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67069333"
---
# <a name="stream-azure-monitoring-data-to-an-event-hub-for-consumption-by-an-external-tool"></a>将 Azure 监视数据流式传输到事件中心以便外部工具使用

本文将演练如何将 Azure 环境中的不同数据层设置为发送到单个事件中心命名空间或事件中心，以便由外部工具收集。

> [!VIDEO https://www.youtube.com/embed/SPHxCgbcvSw]

## <a name="what-data-can-i-send-into-an-event-hub"></a>可将哪些数据发送到事件中心？

在 Azure 环境中，有多“层”监视数据，访问每层数据的方法略有不同。 通常情况下，这些层可描述为：

- **应用程序监视数据：** 有关已编写并在 Azure 上运行的代码的性能和功能的数据。 应用程序监视数据的示例包括性能跟踪、应用程序日志及用户遥测数据。 通常以下列的一种方式收集应用程序监视数据：
  - 用 [Application Insights SDK](../../azure-monitor/app/app-insights-overview.md) 等 SDK 检测代码。
  - 运行一个监视代理（如 [Windows Azure 诊断代理](./../../azure-monitor/platform/diagnostics-extension-overview.md)或 [Linux Azure 诊断代理](../../virtual-machines/extensions/diagnostics-linux.md)），以便侦听运行应用程序的计算机上的新应用程序日志。
- **来宾 OS 监视数据**：有关运行应用程序的操作系统的数据。 来宾 OS 监视数据的示例有 Linux syslog 或 Windows 系统日志。 若要收集此类型的数据，需安装代理，如[ Windows Azure 诊断代理](./../../azure-monitor/platform/diagnostics-extension-overview.md)或 [Linux Azure 诊断代理](../../virtual-machines/extensions/diagnostics-linux.md)。
- **Azure 资源监视数据：** 有关 Azure 资源操作的数据。 对于某些 Azure 资源类型（如虚拟机），该 Azure 服务中会监视来宾 OS 和应用程序。 对于其他 Azure 资源（如网络安全组），资源监视数据是可用数据的最高层（因为没有 来宾 OS 或应用程序在这些资源中运行）。 可以使用[资源诊断设置](./../../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings)收集这些数据。
- **Azure 订阅监视数据：** 有关 Azure 订阅操作和管理的数据，以及有关 Azure 本身运行状况和操作的数据。 [活动日志](./../../azure-monitor/platform/activity-logs-overview.md)包含大多数订阅监视数据，例如服务运行状况事件和 Azure 资源管理器审核。 可以使用日志配置文件收集此数据。
- **Azure 租户监视数据：** 有关租户级 Azure 服务（例如 Azure Active Directory）操作的数据。 Azure Active Directory 审核和登录是租户监视数据的示例。 可以使用租户诊断设置收集此数据。

可将任何层的数据发送到事件中心，以便将其拉取到合作伙伴工具。 可将某些源配置为直接向事件中心发送数据，同时，可能需要使用另一个进程（例如逻辑应用）来检索所需的数据。 以下各节描述了如何将每层数据配置为流式传输到事件中心。 这些步骤假定你拥有处于要监视的层的资产。

## <a name="set-up-an-event-hubs-namespace"></a>设置事件中心命名空间

开始之前，需[创建事件中心命名空间和事件中心](../../event-hubs/event-hubs-create.md)。 此命名空间和事件中心是所有监视数据的目标。 事件中心命名空间是共享相同访问策略的事件中心的逻辑分组，就像存储帐户中有各个 blob 一样。 请注意有关所创建的事件中心命名空间和事件中心的一些详细信息：
* 我们建议使用标准事件中心命名空间。
* 通常，只需要一个吞吐量单位。 如果需要在日志使用量增加时纵向扩展，以后始终可以手动增加命名空间的吞吐量单位数或启用自动膨胀。
* 使用吞吐量单位数，可增加事件中心的吞吐量规模。 使用分区数可以在多个使用者之间并行使用。 单个分区最多可以执行 20MBps，或者大约每秒 20,000 条消息。 不一定支持从多个分区使用，具体取决于使用数据的工具。 如果不确定要设置的分区数，我们建议从四个分区开始。
* 我们建议将事件中心的消息保留期设置为 7 天。 如果使用的工具多天出现故障，这可确保该工具可以从它中断的位置重新开始（因为事件最多可保存 7 天）。
* 我们建议将默认使用者组用于事件中心。 除非你打算使用两个不同的工具使用同一事件中心内的相同数据，否则无需创建其他使用者组或使用单独的使用者组。
* 对于 Azure 活动日志中，选择事件中心命名空间和 Azure Monitor 创建名为 insights-日志的操作的日志。 该命名空间中的事件中心 对于其他日志类型，可以选择现有的事件中心 （让你重复使用同一 insights 日志的操作日志事件中心） 或让 Azure Monitor 创建每个日志类别的事件中心。
* 通常情况下，必须使用来自事件中心的数据的 VNET 在计算机上打开出站端口 5671 和端口 5672。

另请参阅 [Azure 事件中心常见问题解答](../../event-hubs/event-hubs-faq.md)。

## <a name="azure-tenant-monitoring-data"></a>Azure 租户监视数据

Azure 租户监视数据目前仅适用于 Azure Active Directory。 可以使用 [Azure Active Directory 报告](../../active-directory/reports-monitoring/overview-reports.md)中的数据，其中包含特定租户中的登录活动历史记录和更改审核跟踪。

### <a name="azure-active-directory-data"></a>Azure Active Directory 数据

若要将 Azure Active Directory 日志中的数据发送到事件中心命名空间，请在 AAD 租户上设置租户诊断设置。 请[按照此指南](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)设置租户诊断设置。

## <a name="azure-subscription-monitoring-data"></a>Azure 订阅监视数据

Azure 订阅监视数据可以在 [Azure 活动日志](./../../azure-monitor/platform/activity-logs-overview.md)中找到。 此日志包含来自资源管理器的创建、更新和删除操作；[Azure 服务运行状况](../../service-health/service-health-overview.md)中可能影响订阅中资源的更改；[资源运行状况](../../service-health/resource-health-overview.md)状态转换；以及若干其他类型的订阅级别事件。 [本文详细介绍了 Azure 活动日志中显示的所有事件类别](./../../azure-monitor/platform/activity-log-schema.md)。

### <a name="activity-log-data"></a>活动日志数据

若要将数据从 Azure 活动日志发送到事件中心命名空间，请在订阅上设置日志配置文件。 [按照本指南](./activity-logs-stream-event-hubs.md)在订阅上设置日志配置文件。 对要监视每个订阅执行一次此操作。

> [!TIP]
> 日志配置文件当前仅允许选择一个事件中心命名空间，并将在其中创建名为“insights-operational-logs”的事件日志。 尚不可在日志配置文件中指定自己的事件中心名称。

## <a name="azure-resource-metrics-and-diagnostics-logs"></a>Azure 资源指标和诊断日志

Azure 资源将发出两种类型的监视数据：
1. [资源诊断日志](diagnostic-logs-overview.md)
2. [指标](data-platform.md)

使用资源诊断设置将两种类型的数据发送到事件中心。 [按照本指南](diagnostic-logs-stream-event-hubs.md)在特定资源上设置资源诊断设置。 在要从其收集日志的每个资源上设置资源诊断设置。

> [!TIP]
> 可使用 Azure Policy，[在策略规则中使用 DeployIfNotExists 效果](../../governance/policy/concepts/definition-structure.md#policy-rule)，确保特定范围内的每个资源始终设置了诊断设置。

## <a name="guest-os-data"></a>来宾 OS 数据

需要安装代理以将来宾 OS 监视数据发送到事件中心。 对于 Windows 或 Linux，请在配置文件中指定要发送到事件中心的数据，以及应将数据发送到的事件中心，并将该配置文件传递给在 VM 上运行的代理。

### <a name="linux-data"></a>Linux 数据

[Linux Azure 诊断代理](../../virtual-machines/extensions/diagnostics-linux.md)用于将来自 Linux 计算机的监视数据发送到事件中心。 在 LAD 配置文件保护的设置 JSON 中添加事件中心作为接收器，即可完成此操作。 [参阅此文章，详细了解如何向 Linux Azure 诊断代理添加事件中心接收器](../../virtual-machines/extensions/diagnostics-linux.md#protected-settings)。

> [!NOTE]
> 不能在门户中将来宾 OS 监视数据设置为流式传输到事件中心。 相反，必须手动编辑配置文件。

### <a name="windows-data"></a>Windows 数据

[Windows Azure 诊断代理](./../../azure-monitor/platform/diagnostics-extension-overview.md)用于将来自 Windows 计算机的监视数据发送到事件中心。 在 WAD 配置文件的 privateConfig 部分添加事件中心作为接收器，即可完成此操作。 [参阅此文章，详细了解如何向 Windows Azure 诊断代理添加事件中心接收器](./../../azure-monitor/platform/diagnostics-extension-stream-event-hubs.md)。

> [!NOTE]
> 不能在门户中将来宾 OS 监视数据设置为流式传输到事件中心。 相反，必须手动编辑配置文件。

## <a name="application-monitoring-data"></a>应用程序监视数据

应用程序监视数据要求代码经过 SDK 检测，因此没有将应用程序监视数据路由到 Azure 中事件中心的通用解决方案。 但是，[Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) 是一项可用于收集 Azure 应用程序级数据的服务。 如果使用 Application Insights，可通过执行以下操作，将监视数据流式传输到事件中心：

1. 将 Application Insights 数据[设置为连续导出](../../azure-monitor/app/export-telemetry.md)到存储帐户。

2. 设置计时器触发逻辑应用，[从 blob 存储拉取数据](../../connectors/connectors-create-api-azureblobstorage.md#add-action)并[将其作为消息推送到事件中心](../../connectors/connectors-create-api-azure-event-hubs.md#add-action)。

## <a name="what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub"></a>可对发送到事件中心的监视数据执行什么操作？

通过 Azure Monitor 将监视数据路由到事件中心，可与合作伙伴 SIEM 和监视工具轻松集成。 大多数工具需要事件中心连接字符串和对 Azure 订阅的某些权限，才能从事件中心读取数据。 下面是与 Azure Monitor 集成的工具的不完整列表：

* **IBM QRadar** -Microsoft Azure DSM 和 Microsoft Azure 事件中心协议均可从 [IBM 支持网站](https://www.ibm.com/support)下载。 可以[在此处了解 Azure 集成](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0)。
* **Splunk** - 有两种方法，具体取决于 Splunk 设置：
    1. [适用于 Splunk 的 Azure Monitor 加载项](https://splunkbase.splunk.com/app/3534/)可在 Splunkbase 中找到，它是一个开源项目。 [文档见此处](https://github.com/Microsoft/AzureMonitorAddonForSplunk/wiki/Azure-Monitor-Addon-For-Splunk)。
    2. 如果无法在 Splunk 实例中安装加载项（例如， 如果使用代理或在 Splunk Cloud 上运行），可以使用[此函数（由事件中心中的新消息触发）](https://github.com/Microsoft/AzureFunctionforSplunkVS)将这些事件转发到 Splunk HTTP 事件收集器。
* **SumoLogic** - 将 SumoLogic 设置为使用来自事件中心的数据的说明[见此处](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub)
* **ArcSight** - ArcSight Azure 事件中心智能连接器作为[此处的 ArcSight 智能连接器集合](https://community.softwaregrp.com/t5/Discussions/Announcing-General-Availability-of-ArcSight-Smart-Connectors-7/m-p/1671852)的一部分提供。
* **Syslog 服务器** - 如果要将 Azure Monitor 数据直接流式传输到 syslog 服务器，可查看[此 GitHub 存储库](https://github.com/miguelangelopereira/azuremonitor2syslog/)。

## <a name="next-steps"></a>后续步骤
* [活动日志存档到存储帐户](../../azure-monitor/platform/archive-activity-log.md)
* [读取 Azure 活动日志概述](../../azure-monitor/platform/activity-logs-overview.md)
* [设置警报基于活动日志事件](../../azure-monitor/platform/alerts-log-webhook.md)


