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
ms.date: 08/08/2017
ms.author: TomSh
ms.custom: azlog
ms.translationtype: HT
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: 5e672bd6b9356ce16663e843e4a4e7365cb159c3
ms.contentlocale: zh-cn
ms.lasthandoff: 08/09/2017

---
# <a name="introduction-to-microsoft-azure-log-integration"></a>Microsoft Azure 日志集成简介
了解 Azure 日志集成及其主要功能和工作原理。

## <a name="overview"></a>概述

Azure 日志集成是一种免费的解决方案，可用于将原始日志从 Azure 资源集成到本地安全信息和事件管理 (SIEM) 系统。

Azure 日志集成从 Windows 事件查看器频道，以及来自 Azure 资源的 [Azure 活动日志](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)、[Azure 安全中心警报](../security-center/security-center-intro.md)和 [Azure 诊断日志](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)收集 Windows 事件。 此集成帮助 SIEM 解决方案为本地或云中的所有资产提供统一的仪表板，以便可以针对安全事件进行聚合、关联、分析和发出警报。

>[!NOTE]
此时，唯一受支持的云为 Azure 商业云和 Azure 政府云。 暂不支持其他云。

![Azure 日志集成][1]

## <a name="what-logs-can-i-integrate"></a>可以集成哪些日志？
Azure 针对每个 Azure 服务生成大量日志记录。 这些日志代表三种日志类型：

* **控制/管理日志**，提供 [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) 创建、更新和删除操作的相关信息。 Azure 活动日志是此类日志的示例。
* **数据平面日志**提供作为 Azure 资源使用的一部分而被引发的事件的相关信息。 此日志类型的一个示例是位于 Windows 虚拟机上 Windows 事件查看器的**系统**、**安全**和**应用程序**频道。 另一个示例是通过 Azure Monitor 配置的诊断日志记录
* **已处理事件**提供代表你处理的已分析事件和警报相关信息。 此日志类型的一个示例是 Azure 安全中心警报，由 Azure 安全中心处理和分析订阅，提供当前安全状态的相关警报。

Azure 日志集成目前支持集成 Azure 活动日志、Azure 订阅中 Windows 虚拟机上的 Windows 事件日志、Azure 安全中心警报、Azure 诊断日志和 Azure Active Directory 审核日志。

>[!NOTE]
虽然 Azure 日志集成解决方案免费，但日志文件信息存储会导致产生 Azure 存储费用。

下表解释了日志类别和 SIEM 集成详细信息

| 日志类型  |支持 JSON (Splunk, ELK) 的 Log Analytics| ArcSight  | QRadar  |   
|---|---|---|---|
|  AAD 审核日志 |  是 | 需要创建 FlexConnector JSON 分析文件。 有关详细信息，请参阅 ArcSight 文档  |  必须创建日志源扩展。 有关详细信息，请参阅 QRadar 文档。 |  
| 活动日志  | 是  |  下载中心提供 FlexConnector JSON 分析文件和 Azure 日志集成下载 |  [QRadar DSM](https://www.ibm.com/support/knowledgecenter/SSKMKU/com.ibm.dsm.doc/c_dsm_guide_microsoft_azure_overview.html)（通过 Syslog 发送） |  
| ASC 警报  | 是  |  需要创建 FlexConnector JSON 分析文件。 有关详细信息，请参阅 ArcSight 文档。 | [QRadar DSM](https://www.ibm.com/support/knowledgecenter/SSKMKU/com.ibm.dsm.doc/c_dsm_guide_microsoft_azure_overview.html)（通过 Syslog 发送）   |   
| 诊断日志（资源日志） | 是 | 需要最终用户创建 FlexConnector JSON 分析文件。 相关操作请参见 ArcSight 文档。 | 必须创建日志源扩展。 有关详细信息，请参阅 QRadar 文档 |
| VM 日志 | 通过 Forwarded 事件，则为是；通过 JSON，则为否 | 通过 Forwarded 事件，则为是 | 通过 Forwarded 事件，则为是 |

有关支持的日志类型的附加信息，请访问[常见问题解答](security-azure-log-integration-faq.md)


通过 [Azure 日志集成 MSDN 论坛](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration)可以获得社区帮助。 在此论坛中，AzLog 社区可以对如何充分利用 Azure 日志集成相互提问和回答，并提供提示和技巧。 此外，Azure 日志集成团队也会关注此论坛，并尽可能地提供帮助。

也可以打开[支持请求](../azure-supportability/how-to-create-azure-support-request.md)。 若要执行此操作，请选择“日志集成”作为需要请求支持的服务。

## <a name="next-steps"></a>后续步骤
本文档介绍了 Azure 日志集成。 若要详细了解 Azure 日志集成和支持的日志类型，请参阅以下内容：

* [Microsoft Azure 日志集成](https://www.microsoft.com/download/details.aspx?id=53324) - 下载中心，其中包含有关 Azure 日志集成的详细信息、系统要求和安装说明。
* [Azure 日志集成入门](security-azure-log-integration-get-started.md) - 本教程会指导用户完成 Azure 日志集成的安装，以及集成来自 Azure WAD 存储的日志、Azure 活动日志、Azure 安全中心警报和 Azure Active Directory 审核日志。
* [合作伙伴配置步骤](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) - 此博客文章介绍如何配置 Azure 日志集成，以使用 Splunk、HP ArcSight 和 IBM QRadar 合作伙伴解决方案。 此博客代表了我们关于配置合作伙伴解决方案的当前状态。 在任何情况下，请先参阅合作伙伴解决方案文档。
* [通过 syslog 将活动和 ASC 警报发送到 QRadar](https://blogs.msdn.microsoft.com/azuresecurity/2016/09/24/integrate-azure-logs-to-qradar/) - 这篇博客文章提供了通过 syslog 将活动和 Azure 安全中心警报发送到 QRadar 的步骤
* [Azure 日志集成常见问题解答 (FAQ)](security-azure-log-integration-faq.md) - 此常见问题解答回答了有关 Azure 日志集成的问题。
* [集成安全中心警报和 Azure 日志集成](../security-center/security-center-integrating-alerts-with-log-integration.md) - 此文档介绍如何同步 Azure 安全中心警报与 Azure 日志集成。

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png

