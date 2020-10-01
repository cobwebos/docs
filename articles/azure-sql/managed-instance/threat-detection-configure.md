---
title: 配置高级威胁防护
titleSuffix: Azure SQL Managed Instance
description: 高级威胁防护可检测异常的数据库活动，这些活动指示 Azure SQL 托管实例中存在对数据库的潜在安全威胁。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
ms.date: 09/21/2020
ms.openlocfilehash: d2ea14356bf85c795769f1d406f1571f36adaa38
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2020
ms.locfileid: "91617888"
---
# <a name="configure-advanced-threat-protection-in-azure-sql-managed-instance"></a>在 Azure SQL 托管实例中配置高级威胁防护
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[Azure SQL 托管实例](sql-managed-instance-paas-overview.md)的[高级威胁防护](../database/threat-detection-overview.md)可检测异常活动，这些活动指示存在访问或恶意利用数据库的异常且可能有害的企图。 高级威胁防护可以识别**潜在的 SQL 注入**、**来自异常位置或数据中心的访问**、**来自陌生主体或可能有害的应用程序的访问**以及**暴力破解 SQL 凭据** - 请在[高级威胁防护警报](../database/threat-detection-overview.md#alerts)中查看更多详细信息。

你可以通过[电子邮件通知](../database/threat-detection-overview.md#explore-detection-of-a-suspicious-event)或 [Azure 门户](../database/threat-detection-overview.md#explore-alerts-in-the-azure-portal)接收有关检测到的威胁的通知

[高级威胁防护](../database/threat-detection-overview.md) 是适用于 [Sql 的 Azure Defender](../database/azure-defender-for-sql.md)  产品/服务的一部分，它是一个用于高级 sql 安全功能的统一软件包。 高级威胁防护可通过用于 SQL 的中央 Azure Defender 门户进行访问和管理。

##  <a name="azure-portal"></a>Azure 门户

1. 登录 [Azure 门户](https://portal.azure.com)。 
2. 导航到要保护的 SQL 托管实例的实例配置页面。 在 " **安全性**" 下，选择 " **安全中心**"。
3. 在 "用于 SQL 的 Azure Defender" 配置页
   - 打开 **适用于 SQL** 的 Azure Defender。
   - 配置在检测到异常数据库活动时需要接收安全警报的**电子邮件列表**。
   - 选择保存异常的威胁审核记录的 **Azure 存储帐户**。
   - 选择要配置的**高级威胁防护类型**。 详细了解[高级威胁防护警报](../database/threat-detection-overview.md)。
4. 单击 " **保存** " 以保存新的或更新的 Azure DEFENDER for SQL 策略。

## <a name="next-steps"></a>后续步骤

- 详细了解[高级威胁防护](../database/threat-detection-overview.md)。
- 如需了解有关托管实例的信息，请参阅[什么是 Azure SQL 托管实例](sql-managed-instance-paas-overview.md)。
- 详细了解 [Azure SQL 数据库的高级威胁防护](../database/threat-detection-configure.md)。
- 详细了解 [SQL 托管实例审核](https://go.microsoft.com/fwlink/?linkid=869430)。
- 详细了解 [Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)。
