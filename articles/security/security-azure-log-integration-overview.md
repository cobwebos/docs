---
title: "将 Azure 资源日志集成到 SIEM 系统 | Microsoft Docs"
description: "了解 Azure 日志集成及其主要功能和工作原理。"
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear
ms.assetid: 9c1346e1-baf8-4975-b2f2-42ae05b2dc0a
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2018
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: e427e2f7dafb6db9bc5c15d841fbbf83a02fc0e1
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="introduction-to-microsoft-azure-log-integration"></a>Microsoft Azure 日志集成简介

在你的 SIEM 供应商尚未提供 [Azure Monitor](../monitoring-and-diagnostics/monitoring-get-started.md) 连接器的情况下，可以通过 Azure 日志集成将 Azure 资源中的原始日志与本地安全信息和事件管理 (SIEM) 系统进行集成。

用于集成 Azure 日志的首选方法是通过使用 SIEM 供应商的 Azure Monitor 连接器并遵循这些[说明](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md)。 但是，如果你的 SIEM 供应商未提供 Azure Monitor 连接器，你或许可以使用 Azure 日志集成作为临时解决方案（如果你的 SIEM 受 Azure 日志集成支持），直到有此类连接器可用。

>[!IMPORTANT]
>如果你的主要兴趣是收集虚拟机日志，则通常可参阅 SIEM 供应商的解决方案中提供的此类日志。 通常应首选 SIEM 供应商的连接器作为替代方法。

Azure 日志集成从 Windows 事件查看器日志，以及来自 Azure 资源的 [Azure 活动日志](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)、[Azure 安全中心警报](../security-center/security-center-intro.md)和 [Azure 诊断日志](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)收集 Windows 事件。 此集成帮助 SIEM 解决方案为本地或云中的所有资产提供统一的仪表板，以便可以针对安全事件进行聚合、关联、分析和发出警报。

>[!NOTE]
此时，唯一受支持的云为 Azure 商业云和 Azure 政府云。 不支持其他云。

![Azure 日志集成][1]

## <a name="what-logs-can-i-integrate"></a>可以集成哪些日志？

Azure 针对每个 Azure 服务生成大量日志记录。 这些日志代表三种日志类型：

* **控制/管理日志**，提供 [Azure 资源管理器](../azure-resource-manager/resource-group-overview.md)创建、更新和删除操作的相关信息。 Azure 活动日志是此类日志的示例。
* **数据平面日志**提供作为 Azure 资源使用的一部分而被引发的事件的相关信息。 此日志类型的一个示例是位于 Windows 虚拟机上 Windows 事件查看器的**系统**、**安全**和**应用程序**频道。 另一个示例是通过 Azure Monitor 配置的诊断日志记录
* “已处理事件”提供代表你处理的已分析事件和警报信息。 此日志类型的一个示例是 Azure 安全中心警报，由 Azure 安全中心处理和分析订阅，提供当前安全状态的相关警报。

Azure 日志集成支持 ArcSight、QRadar 和 Splunk。 在所有情况下，都应当与你的 SIEM 供应商核实，确定他们是否具有本机连接器。 有本机连接器可用时，不应使用 Azure 日志集成。

如果没有其他选项可用，可以考虑使用 Azure 日志集成。 下表列出了我们的建议。

|**SIEM** | **已在使用日志集成器的客户** | **正在考察 SIEM 集成选项的客户**|
|---------|--------------------------|-------------------------------------------|
|**SPLUNK** | 开始迁移到[适用于 Splunk 的 Azure Monitor 加载项](https://splunkbase.splunk.com/app/3534/) | 使用 [SPLUNK 连接器](https://splunkbase.splunk.com/app/3534/) |
|**IBM QRADAR** | 迁移到或开始使用 http://aka.ms/azmoneventhub 末尾列出的 QRadar 连接器。 | 使用 http://aka.ms/azmoneventhub 末尾列出的 QRadar 连接器。  |
|**ARCSIGHT** | 继续使用日志集成器，直到有连接器可用，然后迁移到基于连接器的解决方案。  | 考虑使用 Azure Log Analytics 作为替代方法。 除非你愿意在有连接器变得可用时经历迁移过程，否则不要采用 Azure 日志集成。 |

>[!NOTE]
>虽然 Azure 日志集成解决方案免费，但日志文件信息存储会导致产生 Azure 存储费用。

如果需要帮助，可以提出[支持请求](../azure-supportability/how-to-create-azure-support-request.md)。 若要执行此操作，请选择“日志集成”作为需要请求支持的服务。

## <a name="next-steps"></a>后续步骤

本文档介绍了 Azure 日志集成。 若要详细了解 Azure 日志集成和支持的日志类型，请参阅以下内容：

* [Azure 日志集成入门](security-azure-log-integration-get-started.md) - 本教程会指导用户完成 Azure 日志集成的安装，以及集成来自 Azure WAD 存储的日志、Azure 活动日志、Azure 安全中心警报和 Azure Active Directory 审核日志。
* [Azure 日志集成常见问题解答 (FAQ)](security-azure-log-integration-faq.md) - 此常见问题解答回答了有关 Azure 日志集成的问题。
* [将 Azure 监视数据流式传输到事件中心以便外部工具使用](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md)

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png
