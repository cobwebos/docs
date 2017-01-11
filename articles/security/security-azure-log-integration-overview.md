---
title: "Microsoft Azure 日志集成简介 | Microsoft Docs"
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
ms.date: 01/07/2017
ms.author: TomSh
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 68c0e3c73b3192aedc18ec3588f1201b3aa36f35


---
# <a name="introduction-to-microsoft-azure-log-integration-preview"></a>Microsoft Azure 日志集成（预览版）简介
了解 Azure 日志集成及其主要功能和工作原理。

## <a name="overview"></a>概述
Azure 中托管的平台即服务 (PaaS) 和基础结构即服务 (IaaS) 在安全日志中生成大量数据。 这些日志包含可以提供网络、主机和用户活动中的情报和深入了解其中的策略违规、内部和外部威胁、法规合规性问题和异常情况的重要信息。

Azure 日志集成使你能够将原始日志从你的 Azure 资源集成到你的本地安全信息和事件管理 (SIEM) 系统。 Azure 日志集成从 Windows *(WAD)* 虚拟机、Azure 活动日志、Azure 安全中心警报和 Azure 资源提供程序日志收集 Azure 诊断。 此集成为本地或云中的所有资产提供统一的仪表板，以便你可以针对安全事件进行聚合、关联、分析和发出警报。

![Azure 日志集成][1]

## <a name="what-logs-can-i-integrate"></a>可以集成哪些日志？
Azure 针对每个 Azure 服务生成大量日志记录。 这些日志按以下两种主要类型进行分类：

* **控制/管理日志**，使 Azure 资源管理器的 CREATE、UPDATE 和 DELETE 操作可见。 Azure 审核日志是此类日志的示例。
* **数据平面日志**，使作为 Azure 资源的使用情况的一部分引发的事件可见。 此类日志的示例是虚拟机中的 Windows 事件系统、安全性和应用程序日志。

Azure 日志集成当前支持 Azure 审核日志、虚拟机日志和 Azure 安全中心警报的集成。

如果存在关于 Azure 日志集成的问题，请发送电子邮件至 [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

## <a name="next-steps"></a>后续步骤
在本文档中，已向你介绍 Azure 日志集成。 若要详细了解 Azure 日志集成和支持的日志类型，请参阅以下内容：

* [Microsoft Azure Log Integration for Azure logs (Preview)](https://www.microsoft.com/download/details.aspx?id=53324)（适用于 Azure 日志的 Microsoft Azure 日志集成（预览版））– 有关 Azure 日志集成的详细信息、系统要求和安装说明的下载中心。
* [Azure 日志集成入门](security-azure-log-integration-get-started.md) - 本教程将指导你完成 Azure 日志集成的安装，以及集成来自 Azure 存储、Azure 审核日志和安全中心警报的日志。
* [合作伙伴配置步骤](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) - 此博客文章介绍如何配置 Azure 日志集成，以使用 Splunk、HP ArcSight 和 IBM QRadar 合作伙伴解决方案。
* [Azure 日志集成常见问题解答 (FAQ)](security-azure-log-integration-faq.md) - 此常见问题解答回答了有关 Azure 日志集成的问题。
* [集成安全中心警报与 Azure 日志集成](../security-center/security-center-integrating-alerts-with-log-integration.md) - 本文档介绍如何将安全中心警报以及由 Azure 诊断和 Azure 审核日志收集的虚拟机安全事件与你的日志分析或 SIEM 解决方案同步。
* [New features for Azure diagnostics and Azure Audit Logs](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/)（Azure 诊断和 Azure 审核日志的新功能）– 此博客文章介绍 Azure 审核日志和其他功能，可帮助你深入了解 Azure 资源的操作。

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png



<!--HONumber=Dec16_HO2-->


