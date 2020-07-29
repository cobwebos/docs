---
title: 配置高级威胁防护
description: 高级威胁防护检测异常的数据库活动，指出对 Azure SQL 数据库中的数据库存在潜在的安全威胁
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-dt-2019, sqldbrb=1
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 08/05/2019
ms.openlocfilehash: 2f4e9841a44252829fae283a12ba804219204022
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84321534"
---
# <a name="configure-advanced-threat-protection-for-azure-sql-database"></a>为 Azure SQL 数据库配置高级威胁防护
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

适用于 Azure SQL 数据库的[高级威胁防护](threat-detection-overview.md)可检测异常活动，这些活动表明访问或利用数据库的异常和潜在有害尝试。 高级威胁防护可以识别**潜在的 SQL 注入**、**来自异常位置或数据中心的访问**、**来自陌生主体或可能有害的应用程序的访问**以及**暴力破解 SQL 凭据** - 请在[高级威胁防护警报](threat-detection-overview.md#alerts)中查看更多详细信息。

你可以通过[电子邮件通知](threat-detection-overview.md#explore-detection-of-a-suspicious-event)或 [Azure 门户](threat-detection-overview.md#explore-alerts-in-the-azure-portal)接收有关检测到的威胁的通知

[高级威胁防护](threat-detection-overview.md)是[高级数据安全](advanced-data-security.md)产品/服务的一部分，它是一个用于高级 SQL 安全功能的统一包。 高级威胁防护可通过 SQL 高级数据安全门户进行访问和管理。

## <a name="set-up-advanced-threat-protection-in-the-azure-portal"></a>在 Azure 门户中设置高级威胁防护

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 导航到要保护的服务器的 "配置" 页。 在安全设置中，选择“高级数据安全”****。
3. 在“高级数据安全”**** 配置页：

   - 在服务器上启用高级数据安全。
   - 在“高级威胁防护设置”中的“发送警报到”文本框中，提供检测到异常数据库活动时接收安全警报的电子邮件列表********。
  
   ![设置高级威胁防护](./media/threat-detection/set_up_threat_detection.png)

## <a name="set-up-advanced-threat-protection-using-powershell"></a>使用 PowerShell 设置高级威胁防护

有关脚本示例，请参阅[使用 PowerShell 配置审核和高级威胁防护](scripts/auditing-threat-detection-powershell-configure.md)。

## <a name="next-steps"></a>后续步骤

- 详细了解[高级威胁防护](threat-detection-overview.md)。
- 详细了解[SQL 托管实例中的高级威胁防护](../managed-instance/threat-detection-configure.md)。  
- 详细了解[高级数据安全性](advanced-data-security.md)。
- 详细了解[审核](../../azure-sql/database/auditing-overview.md)
- 详细了解 [Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)
- 有关定价的详细信息，请参阅 [SQL 数据库定价页](https://azure.microsoft.com/pricing/details/sql-database/)  
