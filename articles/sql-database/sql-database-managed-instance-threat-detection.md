---
title: 配置高级威胁防护-Azure SQL 数据库托管实例 |Microsoft Docs
description: 高级威胁防护检测异常的数据库活动, 指示对托管实例中的数据库的潜在安全威胁。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 08/05/2019
ms.openlocfilehash: eb6d8229e85e54a6a3fc8591dc4688a73773fd8b
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816585"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-database-managed-instance"></a>在 Azure SQL 数据库托管实例中配置高级威胁防护

针对[托管实例](sql-database-managed-instance-index.yml)的[高级威胁防护](sql-database-threat-detection-overview.md)可检测异常活动, 指示对访问或利用数据库的异常和潜在有害尝试。 高级威胁防护可以识别**潜在的 SQL 注入**、**从异常位置或数据中心进行访问**、**从不熟悉的主体或潜在有害的应用程序访问**和**暴力破解 SQL 凭据**-请参阅[高级威胁防护警报](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts)中的更多详细信息。

你可以通过[电子邮件通知](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event)或 [Azure 门户](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal)接收有关检测到的威胁的通知

[高级威胁防护](sql-database-threat-detection-overview.md)是[高级的数据安全](sql-database-advanced-data-security.md)(ADS) 产品/服务的一部分, 它是高级 SQL 安全功能的统一包。 可通过中心 SQL ADS 门户访问和管理高级威胁防护。

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>在 Azure 门户中设置高级威胁防护

1. 在 [https://portal.azure.com](https://portal.azure.com) 中启动 Azure 门户。
2. 导航到要保护的托管实例的配置页。 在 "**设置**" 页中, 选择 "**高级数据安全性**"。
3. 在 "高级数据安全配置" 页中
   - 启用高级数据安全性。
   - 配置在检测到异常数据库活动时需要接收安全警报的**电子邮件列表**。
   - 选择保存异常的威胁审核记录的 **Azure 存储帐户**。
   - 选择你想要配置的**高级威胁防护类型**。 详细了解[高级威胁防护警报](sql-database-threat-detection-overview.md)。
4. 单击 "**保存**" 以保存新的或更新的高级数据安全策略。

   ![高级威胁防护](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

   > [!NOTE]
   > 屏幕截图中的价格并不总是反映当前价格, 而是一个示例。

## <a name="next-steps"></a>后续步骤

- 详细了解[高级威胁防护](sql-database-threat-detection-overview.md)。
- 如需了解有关托管实例的信息，请参阅[什么是托管实例](sql-database-managed-instance.md)。
- 详细了解[单一数据库的高级威胁防护](sql-database-threat-detection.md)。
- 详细了解[托管实例审核](https://go.microsoft.com/fwlink/?linkid=869430)。
- 详细了解 [Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)。
