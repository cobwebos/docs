---
title: 威胁检测 - Azure SQL 数据库 | Microsoft Docs
description: 威胁检测会检测异常的数据库活动，这些活动指示单一数据库或弹性池中存在对数据库的潜在安全威胁。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: 60839fa06d8436d3d2202d1d97735c42c5768e9e
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793748"
---
# <a name="azure-sql-database-threat-detection-for-single-or-pooled-databases"></a>针对单一数据库或入池数据库的 Azure SQL 数据库威胁检测

针对单一数据库或入池数据库的[威胁检测](sql-database-threat-detection-overview.md)可以检测异常活动，这些活动指示对数据库的异常和可能有害的访问或利用企图。 威胁检测可以识别**潜在的 SQL 注入**、**来自异常位置或数据中心的访问**、**来自陌生主体或可能有害的应用程序的访问**以及**暴力攻击 SQL 凭据** - 请在[威胁检测警报](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts)中查看更多详细信息。

你可以通过[电子邮件通知](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event)或 [Azure 门户](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)接收有关检测到的威胁的通知

[威胁检测](sql-database-threat-detection-overview.md)是[高级数据安全](sql-database-advanced-data-security.md) (ADS) 产品/服务（它是高级 SQL 安全功能的一个统一包）的一部分。 可通过中心 SQL ADS 门户访问和管理威胁检测。 高级数据安全包按每个逻辑服务器 15 美元/月来计费，前 30 天免费。

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>在 Azure 门户中为数据库设置威胁检测

1. 在 [https://portal.azure.com](https://portal.azure.com) 中启动 Azure 门户。
2. 导航到要保护的 Azure SQL 数据库服务器的配置页。 在安全设置中，选择“高级数据安全”。
3. 在“高级数据安全”配置页：

   - 在服务器上启用高级数据安全。
   - 在“威胁检测设置”中的“发送警报到”文本框中，提供检测到异常数据库活动时收到安全警报的电子邮件列表。
  
   ![设置威胁检测](./media/sql-database-threat-detection/set_up_threat_detection.png)

## <a name="set-up-threat-detection-using-powershell"></a>使用 PowerShell 设置威胁检测

有关脚本示例，请参阅[使用 PowerShell 配置审核和威胁检测](scripts/sql-database-auditing-and-threat-detection-powershell.md)。

## <a name="next-steps"></a>后续步骤

- 详细了解[威胁检测](sql-database-threat-detection-overview.md)。
- 详细了解[托管实例中的威胁检测](sql-database-managed-instance-threat-detection.md)。  
- 详细了解[高级数据安全](sql-database-advanced-data-security.md)。
- 详细了解[审核](sql-database-auditing.md)
- 详细了解 [Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)
- 有关定价的详细信息，请参阅 [SQL 数据库定价页](https://azure.microsoft.com/pricing/details/sql-database/)  
