---
title: 配置威胁检测 - Azure SQL 数据库托管实例 | Microsoft Docs
description: 威胁检测会检测异常的数据库活动，指出存在对托管实例中数据库的潜在安全威胁。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 59a3b4a4e1b08a9a9985836a9f9be44d1eff9c71
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2019
ms.locfileid: "55472059"
---
# <a name="configure-threat-detection-preview-in-azure-sql-database-managed-instance"></a>在 Azure SQL 数据库托管实例中配置威胁检测（预览版）

[SQL 数据库托管实例](sql-database-managed-instance-index.yml)的 Azure SQL [威胁检测](sql-database-threat-detection-overview.md)可检测异常活动，这些活动指示对数据库的异常和可能有害的访问或利用企图。 威胁检测可以识别“潜在的 SQL 注入”、“来自异常位置或数据中心的访问”、“来自陌生主体或可能有害的应用程序的访问”以及“暴力攻击 SQL 凭据”- 请在[威胁检测警报](sql-database-threat-detection-overview.md#azure-sql-database-threat-detection-alerts)中查看详细信息。

你可以通过[电子邮件通知](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event)或 [Azure 门户](sql-database-threat-detection-overview.md#explore-threat-detection-alerts-for-your-database-in-the-azure-portal)接收有关检测到的威胁的通知

[威胁检测](sql-database-threat-detection-overview.md)是 [SQL 高级数据安全](sql-advanced-threat-protection.md) (ADS) 产品/服务（它是高级 SQL 安全功能的一个统一包）的一部分。 可通过中心 SQL ADS 门户访问和管理威胁检测。 威胁检测服务按每个托管实例 15 美元/月来计费，前 30 天免费。

## <a name="set-up-threat-detection-for-your-managed-instance-in-the-azure-portal"></a>在 Azure 门户中为托管实例设置威胁检测

1. 在 [https://portal.azure.com](https://portal.azure.com) 中启动 Azure 门户。
2. 导航到要保护的托管实例的配置页。 在“设置”页中，选择“威胁检测”。
3. 在威胁检测配置页中
   - 将威胁检测设置为“打开”。
   - 配置在检测到异常数据库活动时需要接收安全警报的**电子邮件列表**。
   - 选择保存异常的威胁审核记录的 **Azure 存储帐户**。
4. 单击“保存”以保存新的或更新的威胁检测策略。

   ![威胁检测](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

## <a name="next-steps"></a>后续步骤

- 详细了解[威胁检测](sql-database-threat-detection-overview.md)。
- 了解有关托管实例的信息，请参阅[什么是托管实例](sql-database-managed-instance.md)。
- 详细了解[单一数据库中的威胁检测](sql-database-threat-detection.md)。
- 详细了解[托管实例审核](https://go.microsoft.com/fwlink/?linkid=869430)。
- 了解有关 [Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)的详细信息。
