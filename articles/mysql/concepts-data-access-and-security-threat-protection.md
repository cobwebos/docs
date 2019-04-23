---
title: 高级威胁防护 - Azure Database for MySQL | Microsoft Docs
description: 高级威胁防护会检测异常的数据库活动，指出对数据库的潜在安全威胁。
author: bolzmj
ms.author: mbolz
ms.service: mysql
ms.topic: conceptual
ms.date: 04/05/2019
ms.openlocfilehash: 10fa2a409437c8cc48bcd1a674cc3832f086dcf2
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59795947"
---
# <a name="azure-database-for-mysql-advanced-threat-protection"></a>Azure Database for MySQL 高级威胁防护

Azure Database for MySQL 的高级威胁防护可检测异常活动，指出有人在访问或利用数据库时的异常行为和可能有害的尝试。

> [!NOTE]
> 高级的威胁防护处于公共预览状态。

高级威胁防护包含在高级数据安全产品/服务中，是一个针对高级安全功能的统一软件包。 可以访问和管理通过高级的威胁防护[Azure 门户](https://portal.azure.com)或使用[REST API](/rest/api/mysql/serversecurityalertpolicies)。 功能仅适用于常规用途和内存优化的服务器。

> [!NOTE]
> 高级威胁防护功能在以下 Azure 政府和主权云区域中**不可用**：US Gov 德克萨斯州、US Gov 亚利桑那州、US Gov 爱荷华州、US Gov 弗吉尼亚、US DoD 东部、US DoD 中部、德国中部、德国北部、中国东部、中国东部 2。 请访问[各区域的产品可用性](https://azure.microsoft.com/global-infrastructure/services/)，以了解常规产品可用性。


## <a name="what-is-advanced-threat-protection"></a>什么是高级威胁防护？

Azure Database for MySQL 高级威胁防护提供了一个新的安全层，它针对异常活动发出安全警报，让客户能够在潜在威胁出现时进行检测和应对。 出现可疑数据库活动、潜在漏洞以及异常数据库访问和查询模式时，用户将收到警报。 Azure Database for MySQL 高级威胁防护将警报与 [Azure 安全中心](https://azure.microsoft.com/services/security-center/)集成，其中包含可疑活动的详细信息以及如何调查和缓解威胁的建议操作。 不必是安全专家，也不需要管理先进的安全监视系统，就能使用 Azure Database for MySQL 高级威胁防护轻松解决数据库的潜在威胁。 

![高级威胁防护概念](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>高级威胁防护警报 
Azure Database for MySQL 高级威胁防护可检测异常活动，指出有人在访问或利用数据库时的异常行为和可能有害的尝试，并可以触发以下警报：
- **来自异常位置的访问**：当 Azure Database for MySQL 服务器的访问模式发生更改、有人从异常地理位置登录 Azure Database for MySQL 服务器时，会触发此警报。 在某些情况下，警报会检测合法操作（发布新应用程序或开发人员维护）。 在其他情况下，警报会检测恶意操作（以前的员工、外部攻击者）。
- **来自异常 Azure 数据中心的访问**：当 Azure Database for MySQL 服务器的访问模式发生更改、最近一段时间在此服务器上发现有人从异常 Azure 数据中心登录服务器时，会触发此警报。 在某些情况下，警报会检测合法操作（在 Azure、Power BI 或 Azure Database for MySQL 查询编辑器中发布新应用程序）。 在其他情况下，警报会检测通过 Azure 资源/服务执行的恶意操作（以前的员工、外部攻击者）。
- **来自陌生主体的访问**：当 Azure Database for MySQL 服务器的访问模式发生更改、有人使用异常主体（Azure Database for MySQL 用户）登录服务器时，会触发此警报。 在某些情况下，警报会检测合法操作（发布新应用程序或开发人员维护）。 在其他情况下，警报会检测恶意操作（以前的员工、外部攻击者）。
- **来自可能有害的应用程序的访问**：当使用可能有害的应用程序访问数据库时，会触发此警报。 在某些情况下，警报会检测操作中的渗透测试。 在其他情况下，警报会检测使用常见攻击工具执行的攻击。
- **暴力破解 Azure Database for MySQL 凭据**：当有人使用不同的凭据异常登录并失败很多次时，会触发此警报。 在某些情况下，警报会检测操作中的渗透测试。 在其他情况下，警报会检测暴力破解攻击。

## <a name="next-steps"></a>后续步骤

* 了解有关 [Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)的详细信息
* 有关定价的详细信息，请参阅 [Azure Database for MySQL 定价页](https://azure.microsoft.com/pricing/details/mysql/) 
* 使用 Azure 门户配置 [Azure Database for MySQL 高级威胁防护](howto-database-threat-protection-portal.md)  
