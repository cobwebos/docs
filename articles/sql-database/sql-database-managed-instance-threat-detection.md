---
title: 威胁检测 - Azure SQL 数据库托管实例 | Microsoft Docs
description: 威胁检测会检测异常的数据库活动，指出数据库有潜在的安全威胁。
services: sql-database
author: rmatchoro
manager: craigg
ms.service: sql-database
ms.custom: security, managed instance
ms.topic: conceptual
ms.date: 03/07/2018
ms.author: ronmat
ms.reviewer: carlrab
ms.openlocfilehash: 14ef907717045e2e0cf297694d92468a65e57e4d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34650071"
---
# <a name="azure-sql-database-managed-instance-threat-detection"></a>Azure SQL 数据库托管实例威胁检测

SQL 威胁检测可检测异常活动，这些活动指示对 Azure SQL 数据库托管实例（预览版）的异常和可能有害的访问或利用企图。

## <a name="overview"></a>概述

威胁检测会检测异常的数据库活动，指出存在对托管实例的潜在安全威胁。 对于托管实例，威胁检测目前为预览版。

威胁检测提供了新的安全层，在发生异常数据库活动时会提供安全警报，让客户检测潜在威胁并做出响应。 不必是安全专家，也不需要管理先进的安全监视系统，就能使用威胁检测轻松解决对托管实例的潜在威胁。 为了提供完整的调查体验，建议启用 Azure 托管实例审核，它会将数据库事件写入到 Azure 存储帐户中的审核日志。 

SQL 威胁检测将警报与 [Azure 安全中心](https://azure.microsoft.com/services/security-center/)相集成，为每个受保护的托管实例计费时，将使用与 Azure 安全中心标准层相同的价格，即 $15/每节点/每月，其中每个受支持的托管实例都计为一个节点。  

## <a name="set-up-threat-detection-for-your-managed-instance-in-the-azure-portal"></a>在 Azure 门户中为托管实例设置威胁检测
1. 在 [https://portal.azure.com](https://portal.azure.com) 中启动 Azure 门户。
2. 导航到要保护的托管实例的配置页。 在“设置”页中，选择“威胁检测”。 
3. 在威胁检测配置页中 
   - 将威胁检测设置为“打开”。
   - 配置在检测到异常数据库活动时需要接收安全警报的**电子邮件列表**。
   - 选择保存异常的威胁审核记录的 **Azure 存储帐户**。 
4.  单击“保存”以保存新的或更新的威胁检测策略。

   ![威胁检测](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

## <a name="explore-anomalous-managed-instance-activities-upon-detection-of-a-suspicious-event"></a>检测到可疑事件时探查异常托管实例活动

1. 检测到异常数据库活动时，将收到电子邮件通知。 

   该电子邮件提供可疑安全事件的相关信息，包括异常活动的性质、数据库名称、服务器名称和事件时间。 此外，它还会提供可能原因和建议操作的相关信息，以帮助调查和缓解对托管实例的潜在威胁。

   ![威胁检测电子邮件](./media/sql-database-managed-instance-threat-detection/threat-detection-email.png)

2. 单击电子邮件中“查看最近的 SQL 警报”链接以启动 Azure 门户并显示 Azure 安全中心警报页面，该页面提供在托管实例的数据库上检测到的活动 SQL 威胁的概述。

   ![活动威胁](./media/sql-database-managed-instance-threat-detection/active-threats.png)

3. 单击特定警报可获得其他详细信息以及用于调查此威胁和解决潜在威胁的操作。

   例如，SQL 注入是 Internet 上常见的 Web 应用程序安全问题之一。 SQL 注入被用来攻击由数据驱动的应用程序。 攻击者利用应用程序漏洞将恶意 SQL 语句注入应用程序入口字段，以破坏或修改数据库中的数据。 对于 SQL 注入警报，警报的详细信息包括被利用的有漏洞的 SQL 语句。

   ![SQL 注入](./media/sql-database-managed-instance-threat-detection/sql-injection.png)

## <a name="managed-instance-threat-detection-alerts"></a>托管实例威胁检测警报 

托管实例威胁检测可检测异常活动（指示异常和可能有害的数据库访问或利用企图），并可触发以下警报：
- **SQL 注入漏洞**：当某个应用程序在数据库中生成错误的 SQL 语句时，会触发此警报。 此警报指示可能存在易受 SQL 注入攻击的漏洞。 生成错误语句的可能原因有两个：
 - 应用程序代码中的缺陷导致构造出错误的 SQL 语句
 - 应用程序代码或存储过程在构造错误的 SQL 语句时无法清理用户输入，使该语句被 SQL 注入攻击利用
- **潜在的 SQL 注入**：当 SQL 攻击有效利用已识别到的应用程序漏洞时，会触发此警报。 这意味着，攻击者正在尝试使用有漏洞的应用程序代码或存储过程注入恶意 SQL 语句。
- **从异常位置访问**：当托管实例的访问模式发生更改，有人从异常的地理位置登录到托管实例时，会触发此警报。 在某些情况下，警报会检测合法操作（发布新应用程序或开发人员的维护操作）。 在其他情况下，警报会检测恶意操作（来自以前的员工、外部攻击者，等等）。
- **从异常的 Azure 数据中心访问**：当托管实例的访问模式发生更改，有人从近期未访问此托管实例的 Azure 数据中心登录到此托管实例时，会触发此警报。 在某些情况下，警报会检测合法操作（在 Azure、Power BI 或 Azure SQL 查询编辑器中发布新应用程序，等等）。 在其他情况下，警报会检测通过 Azure 资源/服务执行的恶意操作（以前的员工、外部攻击者）。
- **从不熟悉的主体访问**：当托管实例的访问模式发生更改，有人使用异常的主体（SQL 用户）登录到托管实例时，会触发此警报。 在某些情况下，警报会检测合法操作（新应用程序开发人员的维护操作）。 在其他情况下，警报会检测恶意操作（以前的员工、外部攻击者）。
- **从可能有害的应用程序访问**：当使用可能有害的应用程序访问数据库时，会触发此警报。 在某些情况下，警报会检测操作中的渗透测试。 在其他情况下，警报会检测使用常见攻击工具执行的攻击。
- **暴力破解 SQL 凭据**：当有人使用不同的凭据异常登录并失败很多次时，会触发此警报。 在某些情况下，警报会检测操作中的渗透测试。 在其他情况下，警报会检测暴力破解攻击。

## <a name="next-steps"></a>后续步骤

- 了解有关托管实例的信息，请参阅[什么是托管实例](sql-database-managed-instance.md)
- 详细了解[托管实例审核](https://go.microsoft.com/fwlink/?linkid=869430) 
- 了解有关 [Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)的详细信息
