---
title: 威胁检测 - Azure SQL 数据库 | Microsoft 文档
description: 威胁检测会检测异常的数据库活动，指出数据库有潜在的安全威胁。
services: sql-database
author: rmatchoro
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 06/19/2017
ms.author: ronmat
ms.openlocfilehash: 4cb31eef4ba4b85ea28a58bb9b947b4e07cb0b58
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
# <a name="sql-database-threat-detection"></a>SQL 数据库威胁检测

SQL 威胁检测会检测异常活动，这些活动表示异常和可能有害的数据库访问或使用尝试。

## <a name="overview"></a>概述

SQL 威胁检测提供新的安全层，在发生异常活动时会提供安全警报，让客户检测潜在威胁并做出响应。  出现可疑数据库活动、潜在漏洞、SQL 注入攻击和异常数据库访问模式时，用户将收到警报。 SQL 威胁检测警报提供可疑活动的详细信息，以及如何调查和缓解威胁的推荐操作。 用户可以使用 [SQL 数据库审核](sql-database-auditing.md)来探查可疑事件，判断这些可疑事件是否是因为有人尝试访问、破坏或利用数据库中的数据而生成的。 不必是安全专家，也不需要管理先进的安全监视系统，就能使用威胁检测轻松解决数据库的潜在威胁。

例如，SQL 注入是 Internet 上常见的 Web 应用程序安全问题之一，用于攻击数据驱动的应用程序。 攻击者利用应用程序漏洞将恶意 SQL 语句注入应用程序入口字段，以破坏或修改数据库中的数据。

SQL 威胁检测功能将警报与 [Azure 安全中心](https://azure.microsoft.com/services/security-center/)集成，且每个受保护的 SQL 数据库服务器都将按与 Azure 安全中心标准层相同的价格（即 $15/节点/月）进行计费，其中每个受保护的 SQL 数据库服务器均计为 1 个节点。  

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>在 Azure 门户中为数据库设置威胁检测
1. 在 [https://portal.azure.com](https://portal.azure.com) 中启动 Azure 门户。
2. 导航到要监视的 SQL 数据库的“配置”页。 在“设置”页中，选择“审核和威胁检测”。 
    ![导航窗格][1]
3. 在“审核和威胁检测”配置页中，将审核设置为“打开”，随后会显示威胁检测设置。
  
    ![导航窗格][2]
4. 将威胁检测设置为“打开”。
5. 配置在检测到异常数据库活动时需要接收安全警报的电子邮件列表。
6. 在“审核和威胁检测”页中单击“保存”，以保存新的或更新的审核和威胁检测设置。
       
    ![导航窗格][3]

## <a name="set-up-threat-detection-using-powershell"></a>使用 PowerShell 设置威胁检测

有关脚本示例，请参阅[使用 PowerShell 配置审核和威胁检测](scripts/sql-database-auditing-and-threat-detection-powershell.md)。

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>检测到可疑事件时探查异常数据库活动
1. 检测到异常数据库活动时，将收到电子邮件通知。 <br/>
   电子邮件将提供可疑安全事件的相关信息，包括异常活动的性质、数据库名称、服务器名称、应用程序名称和事件时间。 此外，电子邮件还会提供可能原因和建议操作的相关信息，帮助调查和缓解数据库的潜在威胁。<br/>
     
    ![导航窗格][4]
2. 电子邮件警报包括指向 SQL 审核日志的直接链接。 单击此链接将启动 Azure 门户，并打开可疑事件发生前后的 SQL 审核记录。 单击审核记录可查看有关可疑数据库活动的详细信息，使查找已执行的 SQL 语句（访问者、执行内容、执行时间）以及确定事件是否合法或有恶意（例如易受 SQL 注入攻击的应用程序漏洞、有人破坏敏感数据等等）变得更简单。<br/>
   ![导航窗格][5]


## <a name="explore-threat-detection-alerts-for-your-database-in-the-azure-portal"></a>在 Azure 门户中为数据库检测威胁检测警报

SQL 数据库威胁检测功能将其警报与 [Azure 安全中心](https://azure.microsoft.com/services/security-center/)集成。 Azure 门户中“数据库”页内的实时 SQL 安全磁贴会跟踪活动威胁的状态。 

   ![导航窗格][6]
   
1. 单击 SQL 安全磁贴将启动“Azure 安全中心警报”页，并提供在数据库中检测到的活动 SQL 威胁的概述。 

  ![导航窗格][7]

2. 单击特定警报将提供其他详细信息以及用于调查此威胁和解决潜在威胁的操作。

  ![导航窗格][8]


## <a name="next-steps"></a>后续步骤

* 有关威胁检测的详细信息，请访问 [Azure 博客](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-general-availability-in-spring-2017/) 
* 了解有关 [Azure SQL 数据库审核](sql-database-auditing.md)的详细信息
* 了解有关 [Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)的详细信息
* 有关定价的更多详细信息，请参阅 [SQL 数据库定价页](https://azure.microsoft.com/pricing/details/sql-database/)  
* 有关 PowerShell 脚本示例，请参阅[使用 PowerShell 配置审核和威胁检测](scripts/sql-database-auditing-and-threat-detection-powershell.md)



<!--Image references-->
[1]: ./media/sql-database-threat-detection/1_td_click_on_settings.png
[2]: ./media/sql-database-threat-detection/2_td_turn_on_auditing.png
[3]: ./media/sql-database-threat-detection/3_td_turn_on_threat_detection.png
[4]: ./media/sql-database-threat-detection/4_td_email.png
[5]: ./media/sql-database-threat-detection/5_td_audit_record_details.png
[6]: ./media/sql-database-threat-detection/6_td_security_tile_view_alerts.png
[7]: ./media/sql-database-threat-detection/7_td_SQL_security_alerts_list.png
[8]: ./media/sql-database-threat-detection/8_td_SQL_security_alert_details.png


