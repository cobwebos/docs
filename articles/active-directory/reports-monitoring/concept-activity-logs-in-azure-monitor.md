---
title: Azure Monitor 中的 Azure Active Directory 活动日志（预览版）| Microsoft Docs
description: Azure Monitor 中的 Azure Active Directory 活动日志（预览版）概述
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: concept
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 0c59cac666dec00e02f21ba20c0608b6b8142ba4
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2018
ms.locfileid: "45740642"
---
# <a name="azure-ad-activity-logs-in-azure-monitor-preview"></a>Azure Monitor 中的 Azure AD 活动日志（预览版）

现在可以使用 Azure Monitor 将 Azure Active Directory (Azure AD) 活动日志路由到你自己的存储帐户或事件中心。 Azure Monitor 中的 Azure Active Directory 日志的公共预览版提供以下功能：

* 将 Azure 存储帐户的审核日志存档，以便长期保留数据。
* 使用常用的安全信息和事件管理 (SIEM) 工具（例如 Splunk 和 QRadar）将审核日志流式传输到 Azure 事件中心进行分析。
* 将审核日志流式传输到事件中心，以便与自定义日志解决方案集成。

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

## <a name="supported-reports"></a>支持的报表

可以使用此功能将审核活动日志和登录活动日志路由到 Azure 存储帐户、事件中心或自定义解决方案。 

* **审核日志**： 可以通过[审核日志活动报表](concept-audit-logs.md)访问在租户中执行的每个任务的历史记录。
* **登录日志**： 可以通过[登录活动报表](concept-sign-ins.md)来确定谁执行了审核日志中报告的任务。

> [!NOTE]
> 目前不支持 B2C 相关的审核和登录活动日志。
>

## <a name="prerequisites"></a>先决条件

若要使用此功能，需满足以下条件：

* Azure 订阅。 如果没有 Azure 订阅，可以[注册免费试用版](https://azure.microsoft.com/free/)。
* 在 Azure 门户中访问 Azure AD 审核日志所需的 Azure AD Free、Basic、Premium 1 或 Premium 2 [许可证](https://azure.microsoft.com/pricing/details/active-directory/)。 
* 在 Azure 门户中访问 Azure AD 登录日志所需的 Azure AD Premium 1 或 Premium 2 [许可证](https://azure.microsoft.com/pricing/details/active-directory/)。 

根据审核日志数据要路由到的位置，需满足以下条件之一：

* 你对其拥有 *ListKeys* 权限的 Azure 存储帐户。 建议使用常规存储帐户而非 Blob 存储帐户。 有关存储定价信息，请查看 [Azure 存储定价计算器](https://azure.microsoft.com/pricing/calculator/?service=storage)。 
* 用于与第三方解决方案集成的 Azure 事件中心命名空间。

> [!NOTE]
> 必须是 Azure AD 租户中的全局管理员或安全管理员才能访问 Azure AD 活动日志。
>

## <a name="cost-considerations"></a>成本注意事项

如果已经有 Azure AD 许可证，则还需要一个 Azure 订阅才能设置存储帐户和事件中心。 Azure 订阅可以免费获取，但若要使用 Azure 资源（包括用于存档的存储帐户以及用于流式处理的事件中心），则需付费。 数据量以及因此引发的费用可能因租户大小的不同而差异很大。 

### <a name="storage-size-for-activity-logs"></a>用于活动日志的存储大小

每个审核日志事件使用大约 2 KB 的数据存储。 如果一个租户有 100,000 个用户，每天会引发大约 150 万个事件，则每天需要大约 3 GB 的数据存储。 由于写入时每批需要大约五分钟的时间，则可预计每月大约有 9,000 次写入操作。 

下表包含的内容是根据租户大小进行的成本估算。这是一个常规用途的 v2 存储帐户，位于“美国西部”区域，保留期至少为一年。 若要针对应用程序的预期数据量进行更准确的估算，请使用 [Azure 存储定价计算器](https://azure.microsoft.com/pricing/details/storage/blobs/)。 

| 日志类别 | 用户数 | 每日事件数 | 每月数据量（估算） | 每月成本（估算） | 每年成本（估算） |
|--------------|-----------------|----------------------|--------------------------------------|----------------------------|---------------------------|
| 审核 | 100,000 | 150&nbsp;万 | 90 GB | $1.93 | $23.12 |
| 审核 | 1,000 | 15,000 | 900 MB | $0.02 | $0.24 |
| 登录 | 1,000 | 34,800 | 4 GB | $0.13 | $1.56 |
| 登录 | 100,000 | 1,500&nbsp;万 | 1.7 TB | $35.41 | $424.92 | 


### <a name="event-hub-messages-for-activity-logs"></a>活动日志的事件中心消息

事件按大约五分钟的时间间隔进行批处理，并以单条消息的形式发送，每条包含该时间范围内的所有事件。 事件中心的消息的最大大小为 256 KB，如果该时间范围内所有消息的总大小超出该大小，则会发送多条消息。 

例如，对于用户数超出 100,000 的大型租户来说，通常情况下每秒大约有 18 个事件，该频率相当于每五分钟 5,400 个事件。 由于审核日志大约每个事件 2 KB，上述事件相当于 10.8 MB 的数据， 因此会在五分钟的时间间隔内向事件中心发送 43 条消息。 

下表包含的内容是根据事件数据的量对“美国西部”区域一个基本事件中心进行的每月成本估算。 若要针对应用程序的预期数据量进行准确的估算，请使用[事件中心定价计算器](https://azure.microsoft.com/pricing/details/event-hubs/)。

| 日志类别 | 用户数 | 每秒事件数 | 每五分钟时间间隔的事件数 | 每个时间间隔的数据量 | 每个时间间隔的消息数 | 每月消息数 | 每月成本（估算） |
|--------------|-----------------|-------------------------|----------------------------------------|---------------------|---------------------------------|------------------------------|----------------------------|
| 审核 | 100,000 | 18 | 5,400 | 10.8 MB | 43 | 371,520 | $10.83 |
| 审核 | 1,000 | 0.1 | 52 | 104 KB | 1 | 8,640 | $10.80 |
| 登录 | 1,000 | 178 | 53,400 | 106.8&nbsp;MB | 418 | 3,611,520 | $11.06 |  


## <a name="frequently-asked-questions"></a>常见问题

此部分回答 Azure Monitor 中 Azure AD 日志的常见问题并讨论已知问题。

**问：我应该从何处着手？** 

**答**： 本文讨论部署此功能需要做哪些准备。 符合先决条件以后，请查看各个教程，了解如何配置日志并将其路由到事件中心。

---

**问：哪些日志包括在其中？**

**答**： 登录活动日志和审核日志均可通过此功能进行路由，虽然与 B2C 相关的审核事件目前未包括在其中。 若要了解目前支持哪些类型的日志和哪些基于功能的日志，请参阅[审核日志架构](reference-azure-monitor-audit-log-schema.md)和[登录日志架构](reference-azure-monitor-sign-ins-log-schema.md)。 

---

**问：执行某项操作之后，相应的日志多快会显示在事件中心？**

**答**： 日志会在执行操作后两到五分钟内显示在事件中心。 有关事件中心的详细信息，请参阅[什么是 Azure 事件中心？](../../event-hubs/event-hubs-about.md)。

---

**问：执行某项操作之后，相应的日志多快会显示在存储帐户中？**

**答**： 就 Azure 存储帐户来说，执行操作之后，日志在其中的显示会有一个 5 到 15 分钟的延迟。

---

**问：存储数据的费用是多少？**

**答**： 存储费用取决于日志大小以及所选保留期。 如需租户估算费用（取决于生成的日志量）的列表，请参阅[活动日志的存储大小](#storage-size-for-activity-logs)部分。

---

**问：将数据流式传输到事件中心的费用是多少？**

**答**： 流式传输费用取决于你每分钟收到的消息数。 本文介绍了费用计算方法并列出了根据消息数估算的费用。 

---

**问：如何将 Azure AD 活动日志与 SIEM 系统集成？**

**答**： 可通过两种方式实现此目的：

- 将 Azure Monitor 与事件中心配合使用，以将日志流式传输到 SIEM 系统。 首先，[将日志流式传输到事件中心](tutorial-azure-monitor-stream-logs-to-event-hub.md)，然后使用配置的事件中心[设置 SIEM 工具](tutorial-azure-monitor-stream-logs-to-event-hub.md#access-data-from-your-event-hub)。 

- 使用[报告图形 API](concept-reporting-api.md) 访问数据，并使用自己的脚本将其推送到 SIEM 系统。

---

**问：目前支持哪些 SIEM 工具？** 

**答**： 目前，[Splunk](tutorial-integrate-activity-logs-with-splunk.md)、QRadar 和 [Sumo Logic](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory) 支持 Azure Monitor。 若要详细了解连接器的工作方式，请参阅[将 Azure 监视数据流式传输到事件中心供外部工具使用](../../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md)。

---

**问：如何将 Azure AD 活动日志与 Splunk 实例集成？**

**答**： 首先，[将 Azure AD 活动日志路由到事件中心](quickstart-azure-monitor-stream-logs-to-event-hub.md)，然后按照步骤[将活动日志与 Splunk 集成](tutorial-integrate-activity-logs-with-splunk.md)。

---

**问：如何将 Azure AD 活动日志与 Sumo Logic 集成？** 

**答**： 首先，[将 Azure AD 活动日志路由到事件中心](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Collect_Logs_for_Azure_Active_Directory)，然后按照步骤[安装 Azure AD 应用程序并查看 SumoLogic 中的仪表板](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards)。

---

**问：是否可以在不使用外部 SIEM 工具的情况下，从事件中心访问数据？** 

**答**： 可以。 若要通过自定义应用程序来访问日志，可以使用[事件中心 API](../../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)。 

---


## <a name="next-steps"></a>后续步骤

* [将活动日志存档到存储帐户](quickstart-azure-monitor-route-logs-to-storage-account.md)
* [将活动日志路由到事件中心](quickstart-azure-monitor-stream-logs-to-event-hub.md)
* [详细了解 Azure 诊断日志](../../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)
