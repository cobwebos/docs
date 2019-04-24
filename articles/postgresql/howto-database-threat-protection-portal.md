---
title: 高级威胁防护 - Azure Database for PostgreSQL
description: 威胁防护会检测异常的数据库活动，指出对数据库的潜在安全威胁。
author: bolzmj
ms.author: mbolz
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/24/2019
ms.openlocfilehash: bbb48708d0e5a7cd07a3971a6966f40696107562
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60421249"
---
# <a name="advanced-threat-protection-for-azure-database-for-postgresql"></a>Azure Database for PostgreSQL 的高级威胁防护

Azure Database for PostgreSQL 的高级威胁防护可检测异常活动，指出有人在访问或利用数据库时的异常行为和可能有害的尝试。

高级威胁防护包含在高级数据安全产品/服务中，是一个针对高级安全功能的统一软件包。 高级威胁防护可以通过 [Azure 门户](https://portal.azure.com)进行访问和管理，当前为预览版。

> [!NOTE]
> 高级威胁防护功能在以下 Azure 政府和主权云区域中**不可用**：US Gov 德克萨斯州、US Gov 亚利桑那州、US Gov 爱荷华州、US Gov 弗吉尼亚、US DoD 东部、US DoD 中部、德国中部、德国北部、中国东部、中国东部 2。 请访问[各区域的产品可用性](https://azure.microsoft.com/global-infrastructure/services/)，以了解常规产品可用性。
>

> [!NOTE]
> Azure 中的所有区域均提供此功能，其中 Azure Database for PostgreSQL 部署用于常规用途和内存优化服务器。

## <a name="set-up-threat-detection"></a>设置威胁检测
1. 在 [https://portal.azure.com](https://portal.azure.com) 中启动 Azure 门户。
2. 导航到要保护的 Azure Database for PostgreSQL 服务器的配置页。 在安全设置中，选择“高级威胁防护(预览版)”。
3. 在“高级威胁防护(预览版)”配置页上：

   - 在服务器上启用高级威胁防护。
   - 在“高级威胁防护设置”中的“发送警报到”文本框中，提供检测到异常数据库活动时接收安全警报的电子邮件列表。
  
   ![设置威胁检测](./media/howto-database-threat-protection-portal/set-up-threat-protection.png)

## <a name="explore-anomalous-database-activities"></a>浏览异常数据库活动

检测到异常数据库活动时，将收到电子邮件通知。 电子邮件将提供可疑安全事件的相关信息，包括异常活动的性质、数据库名称、服务器名称、应用程序名称和事件时间。 此外，电子邮件还会提供可能原因和建议操作的相关信息，帮助调查和缓解数据库的潜在威胁。
    
1. 单击电子邮件中“查看最近的警报”链接，以启动 Azure 门户并显示“Azure 安全中心警报”页，可概要查看在 SQL 数据库上检测到的活动威胁。
    
    ![异常活动报告](./media/howto-database-threat-protection-portal/anomalous-activity-report.png)

    查看活动威胁：

    ![活动威胁](./media/howto-database-threat-protection-portal/active-threats.png)

2. 单击特定警报可获得其他详细信息以及用于调查此威胁和解决潜在威胁的操作。
    
    ![特定警报](./media/howto-database-threat-protection-portal/specific-alert.png)

## <a name="explore-threat-detection-alerts"></a>浏览威胁检测警报

高级威胁防护功能将其警报与 [Azure 安全中心](https://azure.microsoft.com/services/security-center/)集成。 

单击“威胁防护”下的“安全警报”以启动“Azure 安全中心警报”页，并概要查看在数据库中检测到的活动 SQL 威胁。

  ![威胁防护 asc](./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png)

## <a name="next-steps"></a>后续步骤

* 了解有关 [Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)的详细信息
* 有关定价的详细信息，请参阅 [Azure Database for PostgreSQL 定价页](https://azure.microsoft.com/pricing/details/postgresql/)  
