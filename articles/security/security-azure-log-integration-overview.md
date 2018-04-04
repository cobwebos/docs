---
title: 将来自 Azure 资源的日志与 SIEM 系统相集成 | Microsoft Docs
description: 了解 Azure 日志集成及其主要功能和工作原理。
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
ms.openlocfilehash: 6d91692a64a4d3def80990a439fe0a0898bf2f09
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="introduction-to-azure-log-integration"></a>Azure 日志集成简介

可以使用 Azure 日志集成将来自 Azure 资源的原始日志与本地安全信息和事件管理 (SIEM) 系统相集成。 仅当你的 SIEM 供应商没有提供 [Azure Monitor](../monitoring-and-diagnostics/monitoring-get-started.md) 连接器时才使用 Azure 日志集成。

用于集成 Azure 日志的首选方法是使用 SIEM 供应商的 Azure Monitor 连接器。 若要使用连接器，请按照[针对数据事件中心的监视器流监视](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md)中的说明进行操作。 

但是，如果你的 SIEM 供应商未提供 Azure Monitor 连接器，你或许可以使用 Azure 日志集成作为临时解决方案，直到有连接器可用。 仅当 Azure 日志集成支持你的 SIEM 时，才可选择使用 Azure 日志集成。

> [!IMPORTANT]
> 如果你的主要兴趣是收集虚拟机日志，则大多数 SIEM 供应商在其解决方案中都包括了此选项。 使用 SIEM 供应商的连接器始终是首选替代方法。

Azure 日志集成从 Windows 事件查看器日志收集 Windows 事件，从 Azure 资源收集 [Azure 活动日志](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)、[Azure 安全中心警报](../security-center/security-center-intro.md)和 [Azure 诊断日志](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)。 此集成可帮助你的 SIEM 解决方案为你的所有资产（无论是本地的还是云中的）提供统一的仪表板。 可以使用仪表板接收、聚合、关联和分析安全事件的警报。

> [!NOTE]
> 当前，Azure 日志集成仅支持 Azure 商业版云和 Azure 政府版云。 不支持其他云。

![Azure 日志集成流程的图示][1]

## <a name="what-logs-can-i-integrate"></a>可以集成哪些日志

Azure 针对每个 Azure 服务生成大量日志记录。 这些日志提供三种日志类型：

* **控制/管理日志**：提供 [Azure 资源管理器](../azure-resource-manager/resource-group-overview.md)创建、更新和删除操作的相关信息。 Azure 活动日志是此类日志的示例。
* **数据平面日志**：提供使用 Azure 资源时引发的事件的相关信息。 此日志类型的一个示例是位于 Windows 虚拟机上 Windows 事件查看器的**系统**、**安全**和**应用程序**频道。 另一个示例是通过 Azure Monitor 配置的 Azure 诊断日志记录。
* **已处理的事件**：提供已为你处理的已分析事件和警报信息。 此类事件的一个示例是 Azure 安全中心警报。 Azure 安全中心处理并分析你的订阅来提供与你当前的安全状况相关的警报。

Azure 日志集成支持 ArcSight、QRadar 和 Splunk。 与你的 SIEM 供应商核实，确定该供应商是否具有原生连接器。 如果有原生连接器可用，则不要使用 Azure 日志集成。

如果没有其他选项可用，请考虑使用 Azure 日志集成。 下表列出了我们的建议：

|SIEM | 已在使用 Azure 日志集成器的客户 | 正在考察 SIEM 集成选项的客户|
|---------|--------------------------|-------------------------------------------|
|**Splunk** | 开始迁移到[适用于 Splunk 的 Azure Monitor 加载项](https://splunkbase.splunk.com/app/3534/)。 | 使用 [Splunk 连接器](https://splunkbase.splunk.com/app/3534/)。 |
|**QRadar** | 迁移到或开始使用[将 Azure 监视数据流式传输到事件中心以便外部工具使用](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md)的最后一部分中提到的 QRadar 连接器。 | 使用[将 Azure 监视数据流式传输到事件中心以便外部工具使用](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md)的最后一部分中提到的 QRadar 连接器。 |
|**ArcSight** | 继续使用 Azure 日志集成器，直到有连接器可用。 然后，迁移到基于连接器的解决方案。  | 考虑使用 Azure Log Analytics 作为替代方法。 除非你愿意在有连接器变得可用时经历迁移过程，否则不要采用 Azure 日志集成。 |

> [!NOTE]
> 虽然 Azure 日志集成是免费解决方案，但是存在与日志文件信息存储相关的 Azure 存储费用。

如果需要帮助，可以创建[支持请求](../azure-supportability/how-to-create-azure-support-request.md)。 对于服务，请选择“日志集成”。

## <a name="next-steps"></a>后续步骤

本文介绍了 Azure 日志集成。 若要详细了解 Azure 日志集成和支持的日志类型，请参阅以下文章：

* [Azure 日志集成入门](security-azure-log-integration-get-started.md)。 本教程将指导你完成 Azure 日志集成的安装。 它还介绍了如何集成来自 Windows Azure 诊断 (WAD) 存储的日志、Azure 活动日志、Azure 安全中心警报和 Azure Active Directory 审核日志。
* [Azure 日志集成常见问题解答 (FAQ)](security-azure-log-integration-faq.md)。 此常见问题解答回答了关于 Azure 日志集成的常见问题。
* 详细了解如何[将 Azure 监视数据流式传输到事件中心以便外部工具使用](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md)。

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png
