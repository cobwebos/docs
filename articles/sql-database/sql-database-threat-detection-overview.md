---
title: 高级威胁防护 - Azure SQL 数据库 | Microsoft Docs
description: 高级的威胁防护检测到异常数据库活动，指出在 Azure SQL 数据库中的潜在安全威胁。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: monhaber
ms.author: ronmat
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 03/31/2019
ms.openlocfilehash: 710a94c919f4262c3f572f28d03c79b77e658287
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793894"
---
# <a name="advanced-threat-protection-for-azure-sql-database"></a>Azure SQL 数据库的高级威胁防护

高级威胁防护[Azure SQL 数据库](sql-database-technical-overview.md)并[SQL 数据仓库](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)可检测异常活动表示异常和潜在有害尝试访问或利用数据库。

高级的威胁防护属于[高级数据安全](sql-database-advanced-data-security.md)(ADS) 产品/服务，它是统一的高级 SQL 安全功能包。 可以访问和通过中央 SQL 广告门户管理高级的威胁防护。

> [!NOTE]
> 本主题适用于 Azure SQL 服务器，同时也适用于在 Azure SQL 服务器中创建的 SQL 数据库和 SQL 数据仓库数据库。 为简单起见，在提到 SQL 数据库和 SQL 数据仓库时，本文统称 SQL 数据库。

## <a name="what-is-advanced-threat-protection"></a>什么是高级威胁防护

 高级的威胁防护提供了新的安全层，从而使客户能够检测和应对潜在威胁，在发生异常活动时提供安全警报。 出现可疑数据库活动、潜在漏洞、SQL 注入攻击和异常数据库访问和查询模式时，用户将收到警报。 高级的威胁防护集成，警报与[Azure 安全中心](https://azure.microsoft.com/services/security-center/)，其中包括可疑活动的详细信息和建议如何帮助调查和缓解威胁的操作。 高级的威胁防护轻松解决潜在威胁到数据库而无需是安全专家或管理高级的安全监视系统。

为了提供完整的调查体验，建议启用 [SQL 数据库审核](sql-database-auditing.md)，它会将数据库事件写入到 Azure 存储帐户中的审核日志。  

## <a name="advanced-threat-protection-alerts"></a>高级威胁防护警报

Azure SQL 数据库的高级的威胁防护检测到异常活动表示异常和潜在有害尝试访问或利用数据库并可触发以下警报：

- **存在易受 SQL 注入攻击的漏洞**：当某个应用程序在数据库中生成错误的 SQL 语句时，会触发此警报。 此警报会指示可能存在易受 SQL 注入攻击的漏洞。 生成错误语句的可能原因有两个：

  - 应用程序代码中的缺陷导致构造出错误的 SQL 语句
  - 应用程序代码或存储过程在构造错误的 SQL 语句时无法清理用户输入，使该语句被 SQL 注入攻击利用
- **潜在 SQL 注入**：当 SQL 攻击有效利用已识别到的应用程序漏洞时，会触发此警报。 这意味着，攻击者正在尝试使用有漏洞的应用程序代码或存储过程注入恶意 SQL 语句。
- **来自异常位置的访问**：当 SQL Server 的访问模式发生更改，有人从异常的地理位置登录到 SQL Server 时，会触发此警报。 在某些情况下，警报会检测合法操作（发布新应用程序或开发人员维护）。 在其他情况下，警报会检测恶意操作（以前的员工、外部攻击者）。
- **来自异常 Azure 数据中心的访问**：当 SQL Server 的访问模式发生更改，有人从最近在此服务器上出现过的 Azure 数据中心登录到 SQL Server 时，会触发此警报。 在某些情况下，警报会检测合法操作（在 Azure、Power BI 或 Azure SQL 查询编辑器中发布新应用程序）。 在其他情况下，警报会检测通过 Azure 资源/服务执行的恶意操作（以前的员工、外部攻击者）。
- **来自陌生主体的访问**：当 SQL Server 的访问模式发生更改，有人使用异常的主体（SQL 用户）登录到 SQL Server 时，会触发此警报。 在某些情况下，警报会检测合法操作（发布新应用程序或开发人员维护）。 在其他情况下，警报会检测恶意操作（以前的员工、外部攻击者）。
- **来自可能有害的应用程序的访问**：当使用可能有害的应用程序访问数据库时，会触发此警报。 在某些情况下，警报会检测操作中的渗透测试。 在其他情况下，警报会检测使用常见攻击工具执行的攻击。
- **暴力破解 SQL 凭据**：当有人使用不同的凭据异常登录并失败很多次时，会触发此警报。 在某些情况下，警报会检测操作中的渗透测试。 在其他情况下，警报会检测暴力破解攻击。

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>检测到可疑事件时探查异常数据库活动

检测到异常数据库活动时，将收到电子邮件通知。 电子邮件将提供可疑安全事件的相关信息，包括异常活动的性质、数据库名称、服务器名称、应用程序名称和事件时间。 此外，电子邮件还会提供可能原因和建议操作的相关信息，帮助调查和缓解数据库的潜在威胁。

![异常活动报告](./media/sql-database-threat-detection/anomalous_activity_report.png)

1. 单击电子邮件中“查看最近的 SQL 警报”链接以启动 Azure 门户并显示“Azure 安全中心警报”页，该页面提供在 SQL 数据库上检测到的活动威胁的概述。

   ![活动威胁](./media/sql-database-threat-detection/active_threats.png)

2. 单击特定警报可获得其他详细信息以及用于调查此威胁和解决潜在威胁的操作。

   例如，SQL 注入是 Internet 上最常见的 Web 应用程序安全问题之一，用于攻击数据驱动的应用程序。 攻击者利用应用程序漏洞将恶意 SQL 语句注入应用程序入口字段，以破坏或修改数据库中的数据。 对于 SQL 注入警报，警报的详细信息包括被利用的有漏洞的 SQL 语句。

   ![特定警报](./media/sql-database-threat-detection/specific_alert.png)

## <a name="explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal"></a>浏览 Azure 门户中的数据库的高级威胁防护警报

高级的威胁防护集成其警报与[Azure 安全中心](https://azure.microsoft.com/services/security-center/)。 动态 SQL 高级威胁防护磁贴中的数据库和 SQL 广告边栏选项卡在 Azure 门户中的跟踪的活动威胁的状态。

单击**高级威胁防护警报**以启动 Azure 安全中心警报页，并获取检测到数据库或数据仓库上的活动 SQL 威胁的概述。

   ![高级的威胁防护警报](./media/sql-database-threat-detection/threat_detection_alert.png)

   ![高级威胁保护警报 2](./media/sql-database-threat-detection/threat_detection_alert_atp.png)

## <a name="next-steps"></a>后续步骤

- 详细了解如何[单个和共用数据库中的高级威胁防护](sql-database-threat-detection.md)。
- 详细了解如何[托管实例中的高级威胁防护](sql-database-managed-instance-threat-detection.md)。
- 详细了解如何[高级数据安全](sql-database-advanced-data-security.md)。
- 详细了解 [Azure SQL 数据库审核](sql-database-auditing.md)
- 详细了解 [Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)
- 有关定价的详细信息，请参阅 [SQL 数据库定价页](https://azure.microsoft.com/pricing/details/sql-database/)  
