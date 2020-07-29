---
title: 配置高级威胁防护
titleSuffix: Azure SQL Managed Instance
description: 高级威胁防护检测异常的数据库活动，指出对 Azure SQL 托管实例中的数据库存在潜在的安全威胁。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 08/05/2019
ms.openlocfilehash: ceb6285448df2a5d87dfa87ab249c99bf22c9928
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84686318"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-managed-instance"></a>配置 Azure SQL 中的高级威胁防护托管实例
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

适用于[AZURE SQL 托管实例](sql-managed-instance-paas-overview.md)的[高级威胁防护](../database/threat-detection-overview.md)可检测异常活动，这些活动表明访问或利用数据库的异常和潜在有害尝试。 高级威胁防护可以识别**潜在的 SQL 注入**、**来自异常位置或数据中心的访问**、**来自陌生主体或可能有害的应用程序的访问**以及**暴力破解 SQL 凭据** - 请在[高级威胁防护警报](../database/threat-detection-overview.md#alerts)中查看更多详细信息。

你可以通过[电子邮件通知](../database/threat-detection-overview.md#explore-detection-of-a-suspicious-event)或 [Azure 门户](../database/threat-detection-overview.md#explore-alerts-in-the-azure-portal)接收有关检测到的威胁的通知

[高级威胁防护](../database/threat-detection-overview.md)是[高级数据安全](../database/advanced-data-security.md)产品/服务的一部分，它是一个用于高级 SQL 安全功能的统一包。 可通过中心 SQL ADS 门户访问和管理高级威胁防护。

##  <a name="azure-portal"></a>Azure 门户

1. 登录到[Azure 门户](https://portal.azure.com)。 
2. 导航到要保护的 SQL 托管实例实例的 "配置" 页。 在“设置”页中，选择“高级数据安全”********。
3. 在“高级数据安全”配置页中，执行以下操作：
   - **启用**高级数据安全。
   - 配置**电子邮件列表**，以便在检测到异常数据库活动时接收安全警报。
   - 选择保存异常的威胁审核记录的 **Azure 存储帐户**。
   - 选择要配置的**高级威胁防护类型**。 详细了解[高级威胁防护警报](../database/threat-detection-overview.md)。
4. 单击“保存”，保存新的或更新的高级数据安全策略****。

   ![高级威胁防护](./media/threat-detection-configure/threat-detection.png)


## <a name="next-steps"></a>后续步骤

- 详细了解[高级威胁防护](../database/threat-detection-overview.md)。
- 了解托管实例，请参阅[什么是 AZURE SQL 托管实例](sql-managed-instance-paas-overview.md)。
- 详细了解[AZURE SQL 数据库的高级威胁防护](../database/threat-detection-configure.md)。
- 了解有关[SQL 托管实例审核](https://go.microsoft.com/fwlink/?linkid=869430)的详细信息。
- 了解有关[Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)的详细信息。
