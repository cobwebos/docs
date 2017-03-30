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
ms.date: 03/03/2017
ms.author: TomSh
translationtype: Human Translation
ms.sourcegitcommit: 2c9877f84873c825f96b62b492f49d1733e6c64e
ms.openlocfilehash: 434961f7d25195eee3310461a3a97e03ffdd8917
ms.lasthandoff: 03/15/2017


---
# <a name="introduction-to-microsoft-azure-log-integration"></a>Microsoft Azure 日志集成简介
了解 Azure 日志集成及其主要功能和工作原理。

## <a name="overview"></a>概述
Azure 中托管的平台即服务 (PaaS) 和基础结构即服务 (IaaS) 在安全日志中生成大量数据。 这些日志包含可以提供网络、主机和用户活动中的情报和深入了解其中的策略违规、内部和外部威胁、法规合规性问题和异常情况的重要信息。

Azure 日志集成是一种免费的解决方案，可用于将原始日志从 Azure 资源集成到本地安全信息和事件管理 (SIEM) 系统。 Azure 日志集成从 Windows *(WAD)* 虚拟机、Azure 活动日志、Azure 安全中心警报和 Azure 资源提供程序日志收集 Azure 诊断。 此集成为本地或云中的所有资产提供统一的仪表板，以便你可以针对安全事件进行聚合、关联、分析和发出警报。

![Azure 日志集成][1]

## <a name="what-logs-can-i-integrate"></a>可以集成哪些日志？
Azure 针对每个 Azure 服务生成大量日志记录。 这些日志按以下主要类型进行分类：

* **控制/管理日志**，使 Azure Resource Manager 的 CREATE、UPDATE 和 DELETE 操作可见。 Azure 活动日志是此类日志的示例。
* **数据平面日志**，使作为 Azure 资源的使用情况的一部分引发的事件可见。 此类日志的示例是虚拟机中的 Windows 事件系统、安全性、应用程序日志以及通过 Azure Monitor 配置的诊断日志
* **已处理事件**，显示系统已代表你处理的分析事件/警报。此类的示例是 Azure 安全中心警报，Azure 安全中心已处理和分析了订阅，并提供非常精炼的安全警报

Azure 日志集成目前支持集成 Azure 活动日志、Azure 订阅中 Windows 虚拟机上的 Windows 事件日志、Azure 安全中心警报、Azure 诊断日志和 Azure Active Directory 审核日志。

下表解释了日志类别和 SIEM 集成详细信息

| 日志类型  |支持 JSON (Splunk, ELK) 的 Log Analytics| ArcSight  | QRadar  |   
|---|---|---|---|
|  AAD 审核日志 |  是 | 需要创建 FlexConnector JSON 分析文件。 有关详细信息，请参阅 ArcSight 文档  |  必须创建日志源扩展。 有关详细信息，请参阅 QRadar 文档。 |  
| 活动日志  | 是  |  下载中心提供 FlexConnector JSON 分析文件和 Azure 日志集成下载 |  [QRadar DSM](https://www.ibm.com/support/knowledgecenter/SSKMKU/com.ibm.dsm.doc/c_dsm_guide_microsoft_azure_overview.html)（通过 Syslog 发送） |  
| ASC 警报  | 是  |  需要创建 FlexConnector JSON 分析文件。 有关详细信息，请参阅 ArcSight 文档。 | [QRadar DSM](https://www.ibm.com/support/knowledgecenter/SSKMKU/com.ibm.dsm.doc/c_dsm_guide_microsoft_azure_overview.html)（通过 Syslog 发送）   |   
| 诊断日志（资源日志） | 是 | 需要最终用户创建 FlexConnector JSON 分析文件。 相关操作请参见 ArcSight 文档。 | 必须创建日志源扩展。 有关详细信息，请参阅 QRadar 文档 |
| VM 日志 | 通过 Forwarded 事件，则为是；通过 JSON，则为否 | 通过 Forwarded 事件，则为是 | 通过 Forwarded 事件，则为是 |

Azure 日志集成是一种免费的解决方案 - 无需为 Azure 日志集成二进制文件付费。 但是，对于日志文件信息所需的 Azure 存储，会产生相关费用。

如果存在关于 Azure 日志集成的问题，请发送电子邮件至 [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

## <a name="next-steps"></a>后续步骤
在本文档中，已向你介绍 Azure 日志集成。 若要详细了解 Azure 日志集成和支持的日志类型，请参阅以下内容：

* [适用于 Azure 日志的 Microsoft Azure 日志集成](https://www.microsoft.com/download/details.aspx?id=53324) - 下载中心，其中包含有关 Azure 日志集成的详细信息、系统要求和安装说明。
* [Azure 日志集成入门](security-azure-log-integration-get-started.md) - 本教程将指导用户完成 Azure 日志集成的安装，以及集成来自 Azure WAD 存储的日志、Azure 活动日志、Azure 安全中心警报和 Azure Active Directory 审核日志。
* [集成诊断日志](https://blogs.msdn.microsoft.com/azuresecurity/2016/09/25/integrate-azure-logs-streamed-to-event-hubs-to-siem/) - 这篇博客文章提供使用 Azure 日志集成来集成诊断日志的步骤
* [合作伙伴配置步骤](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) - 此博客文章介绍如何配置 Azure 日志集成，以使用 Splunk、HP ArcSight 和 IBM QRadar 合作伙伴解决方案。
* [通过 syslog 将活动和 ASC 警报发送到 QRadar](https://blogs.msdn.microsoft.com/azuresecurity/2016/09/24/integrate-azure-logs-to-qradar/) - 这篇博客文章提供了通过 syslog 将活动和 Azure 安全中心警报发送到 QRadar 的步骤
* [Azure 日志集成常见问题解答 (FAQ)](security-azure-log-integration-faq.md) - 此常见问题解答回答了有关 Azure 日志集成的问题。
* [集成安全中心警报与 Azure 日志集成](../security-center/security-center-integrating-alerts-with-log-integration.md) - 本文档介绍如何将安全中心警报以及由 Azure 诊断和 Azure 审核日志收集的虚拟机安全事件与你的日志分析或 SIEM 解决方案同步。

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png

