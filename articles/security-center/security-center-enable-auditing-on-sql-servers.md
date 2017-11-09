---
title: "在 Azure 安全中心中对 SQL Server 启用审核和威胁检测 | Microsoft Docs"
description: "本文档演示如何实现 Azure 安全中心建议“对 SQL Server 启用审核和威胁检测”。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 042fca4d-7dab-4172-8614-e8c21ccb4960
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: terrylan
ms.openlocfilehash: 660b537aef8d175a478ff93d60b8391d55fc92ad
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="enable-auditing-and-threat-detection-on-sql-servers-in-azure-security-center"></a>在 Azure 安全中心中对 SQL Server 启用审核和威胁检测
Azure 安全中心将建议对 Azure SQL 服务器上的所有数据库启用审核和威胁检测（如果尚未启用）。 审核和威胁检测可帮助你一直保持遵从法规、了解数据库活动，以及深入了解可能指示业务利害关系或疑似安全违规的偏差和异常。

启用审核后，则可以配置威胁检测设置和电子邮件，以接收安全警报。 威胁检测会检测异常的数据库活动，指出数据库有潜在的安全威胁。 这使你能够检测出潜在威胁，并在威胁发生时做出响应。

此建议仅适用于 Azure SQL 服务；不包括 Azure 基础结构服务 (Azure IaaS) 中的虚拟机上运行的 SQL。

> [!NOTE]
> 本文档将使用示例部署介绍该服务。  这并非一份循序渐进的指南。
>
>

## <a name="implement-the-recommendation"></a>实现该建议
1. 在“建议”边栏选项卡中，选择“对 SQL Server 启用审核和威胁检测”。  这将打开“对 SQL Server 启用审核和威胁检测”边栏选项卡。

   ![对 SQL 服务器启用审核][1]
2. 选择一个 SQL Server 以启用审核和威胁检测。 这将打开“审核和威胁检测”边栏选项卡。

3. 在“审核和威胁检测”边栏选项卡上，选择“审核”下方的“ON”。

   ![打开审核设置][2]
4. 请按照[在 Azure 门户中审核 SQL 数据库](../sql-database/sql-database-auditing-portal.md)中的步骤配置要存储审核日志的存储。 数据收集的订阅存储帐户是默认的存储帐户。
5. 请按照 [SQL 数据库威胁检测入门](../sql-database/sql-database-threat-detection.md)一文中的步骤来启用和配置威胁检测，并配置收到有关检测到异常活动的安全警报的电子邮件列表。

## <a name="see-also"></a>另请参阅
本文档演示如何实现安全中心建议“对 SQL Server 启用审核和威胁检测”。 若要了解有关保护 SQL 数据库的详细信息，请参阅以下内容：

* [保护 SQL 数据库](../sql-database/sql-database-security-overview.md)

若要了解有关安全中心的详细信息，请参阅以下文章：

* [在 Azure 安全中心中设置安全策略](security-center-policies.md)了解如何配置 Azure 订阅和资源组的安全策略。
* [在 Azure 安全中心中管理安全建议](security-center-recommendations.md) -- 了解建议如何帮助保护 Azure 资源。
* [Azure 安全中心的安全性运行状况监视](security-center-monitoring.md) -- 了解如何监视 Azure 资源的运行状况。
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md) -- 了解如何管理和响应安全警报。
* [通过 Azure 安全中心监视合作伙伴解决方案](security-center-partner-solutions.md) -- 了解如何监视合作伙伴解决方案的运行状态。
* [Azure 安全中心常见问题解答](security-center-faq.md)查找有关使用服务的常见问题。
* [Azure 安全博客](http://blogs.msdn.com/b/azuresecurity/)获取最新的 Azure 安全新闻和信息。

<!--Image references-->
[1]: ./media/security-center-enable-auditing-on-sql-server/enable-auditing-on-sql-servers.png
[2]: ./media/security-center-enable-auditing-on-sql-server/auditing-settings-blade.png
