---
title: 威胁检测 - Azure SQL 数据库 | Microsoft 文档
description: 威胁检测会检测异常的数据库活动，指出数据库有潜在的安全威胁。
services: sql-database
author: rmatchoro
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 05/17/2018
ms.author: ronmat
ms.reviewer: carlrab
ms.openlocfilehash: db10bbdd39920b05d9fd8c3907f22c3ee5d08b02
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
ms.locfileid: "34364142"
---
# <a name="azure-sql-database-threat-detection"></a>Azure SQL 数据库威胁检测

Azure SQL 数据库威胁检测可检测异常活动，这些活动表示异常和可能有害的数据库访问或使用尝试。

威胁检测包含在 [SQL 高级威胁防护](sql-advanced-threat-protection.md) (ATP) 产品中，该产品是高级 SQL 安全功能统一软件包。 可通过中心 SQL ATP 门户访问和管理威胁检测。

## <a name="what-is-threat-detection"></a>什么是威胁检测？

SQL 威胁检测提供新的安全层，在发生异常活动时会提供安全警报，让客户检测潜在威胁并做出响应。 出现可疑数据库活动、潜在漏洞、SQL 注入攻击和异常数据库访问和查询模式时，用户将收到警报。 SQL 威胁检测将警报与 [Azure 安全中心](https://azure.microsoft.com/services/security-center/)集成，其中包含可疑活动的详细信息以及如何调查和缓解威胁的建议操作。 不必是安全专家，也不需要管理先进的安全监视系统，就能使用 SQL 威胁检测轻松解决数据库的潜在威胁。 

为了提供完整的调查体验，建议启用 [SQL 数据库审核](sql-database-auditing.md)，它会将数据库事件写入到 Azure 存储帐户中的审核日志。  

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>在 Azure 门户中为数据库设置威胁检测
1. 在 [https://portal.azure.com](https://portal.azure.com) 中启动 Azure 门户。
2. 导航到要保护的 Azure SQL 数据库服务器的配置页。 在安全设置中，选择“高级威胁防护”。
3. 在“高级威胁防护”配置页上：

   - 在服务器上启用高级威胁防护。
   - 在“威胁检测设置”中的“发送警报到”文本框中，提供检测到异常数据库活动时收到安全警报的电子邮件列表。
  
   ![设置威胁检测](./media/sql-database-threat-detection/set_up_threat_detection.png)

## <a name="set-up-threat-detection-using-powershell"></a>使用 PowerShell 设置威胁检测

有关脚本示例，请参阅[使用 PowerShell 配置审核和威胁检测](scripts/sql-database-auditing-and-threat-detection-powershell.md)。

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>检测到可疑事件时探查异常数据库活动

检测到异常数据库活动时，将收到电子邮件通知。 电子邮件将提供可疑安全事件的相关信息，包括异常活动的性质、数据库名称、服务器名称、应用程序名称和事件时间。 此外，电子邮件还会提供可能原因和建议操作的相关信息，帮助调查和缓解数据库的潜在威胁。

![异常活动报告](./media/sql-database-threat-detection/anomalous_activity_report.png)
     
1. 单击电子邮件中“查看最近的 SQL 警报”链接以启动 Azure 门户并显示“Azure 安全中心警报”页，该页面提供在 SQL 数据库上检测到的活动威胁的概述。

   ![活动威胁](./media/sql-database-threat-detection/active_threats.png)

2. 单击特定警报可获得其他详细信息以及用于调查此威胁和解决潜在威胁的操作。

   例如，SQL 注入是 Internet 上最常见的 Web 应用程序安全问题之一，用于攻击数据驱动的应用程序。 攻击者利用应用程序漏洞将恶意 SQL 语句注入应用程序入口字段，以破坏或修改数据库中的数据。 对于 SQL 注入警报，警报的详细信息包括被利用的有漏洞的 SQL 语句。

   ![特定警报](./media/sql-database-threat-detection/specific_alert.png)

## <a name="explore-threat-detection-alerts-for-your-database-in-the-azure-portal"></a>在 Azure 门户中为数据库检测威胁检测警报

SQL 数据库威胁检测功能将其警报与 [Azure 安全中心](https://azure.microsoft.com/services/security-center/)集成。 Azure 门户中“数据库和 SQL ATP”边栏选项卡内的“实时 SQL 威胁检测”磁贴会跟踪活动威胁的状态。

单击“威胁检测警报”以启动“Azure 安全中心警报”页，并获取在数据库中检测到的活动 SQL 威胁的概述。

   ![威胁检测警报](./media/sql-database-threat-detection/threat_detection_alert.png)
   
   ![威胁检测警报 2](./media/sql-database-threat-detection/threat_detection_alert_atp.png)

## <a name="azure-sql-database-threat-detection-alerts"></a>Azure SQL 数据库威胁检测警报 
Azure SQL 数据库威胁检测可检测异常活动（指示异常和可能有害的数据库访问或使用企图），并可触发以下警报：
- **SQL 注入漏洞**：当某个应用程序在数据库中生成错误的 SQL 语句时，会触发此警报。 此警报指示可能存在易受 SQL 注入攻击的漏洞。 生成错误语句的可能原因有两个：
   - 应用程序代码中的缺陷导致构造出错误的 SQL 语句
   - 应用程序代码或存储过程在构造错误的 SQL 语句时无法清理用户输入，使该语句被 SQL 注入攻击利用
- **潜在的 SQL 注入**：当 SQL 攻击有效利用已识别到的应用程序漏洞时，会触发此警报。 这意味着，攻击者正在尝试使用有漏洞的应用程序代码或存储过程注入恶意 SQL 语句。
- **从异常位置访问**：当 SQL Server 的访问模式发生更改，有人从异常的地理位置登录到 SQL Server 时，会触发此警报。 在某些情况下，警报会检测合法操作（发布新应用程序或开发人员维护）。 在其他情况下，警报会检测恶意操作（以前的员工、外部攻击者）。
- **从异常的 Azure 数据中心访问**：当 SQL Server 的访问模式发生更改，有人从最近在此服务器上出现过的 Azure 数据中心登录到 SQL Server 时，会触发此警报。 在某些情况下，警报会检测合法操作（在 Azure、Power BI 或 Azure SQL 查询编辑器中发布新应用程序）。 在其他情况下，警报会检测通过 Azure 资源/服务执行的恶意操作（以前的员工、外部攻击者）。
- **从不熟悉的主体访问**：当 SQL Server 的访问模式发生更改，有人使用异常的主体（SQL 用户）登录到 SQL Server 时，会触发此警报。 在某些情况下，警报会检测合法操作（发布新应用程序或开发人员维护）。 在其他情况下，警报会检测恶意操作（以前的员工、外部攻击者）。
- **从可能有害的应用程序访问**：当使用可能有害的应用程序访问数据库时，会触发此警报。 在某些情况下，警报会检测操作中的渗透测试。 在其他情况下，警报会检测使用常见攻击工具执行的攻击。
- **暴力破解 SQL 凭据**：当有人使用不同的凭据异常登录并失败很多次时，会触发此警报。 在某些情况下，警报会检测操作中的渗透测试。 在其他情况下，警报会检测暴力破解攻击。

## <a name="next-steps"></a>后续步骤

* 详细了解 [SQL 高级威胁防护](sql-advanced-threat-protection.md)。 
* 了解有关 [Azure SQL 数据库审核](sql-database-auditing.md)的详细信息
* 了解有关 [Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)的详细信息
* 有关定价的更多详细信息，请参阅 [SQL 数据库定价页](https://azure.microsoft.com/pricing/details/sql-database/)  
