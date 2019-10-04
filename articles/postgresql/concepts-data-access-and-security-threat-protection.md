---
title: 高级威胁防护概念-Azure Database for PostgreSQL-单服务器
description: 了解如何使用高级威胁防护来检测异常的数据库活动，指示数据库存在潜在的安全威胁。
author: bolzmj
ms.author: mbolz
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 61c0e666458018d4e03d5560e417d28d0efadb27
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2019
ms.locfileid: "71950076"
---
# <a name="advanced-threat-protection-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL 中的高级威胁防护-单服务器

Azure Database for PostgreSQL 的高级威胁防护可检测异常活动，指出有人在访问或利用数据库时的异常行为和可能有害的尝试。

> [!NOTE]
> 高级威胁防护是公开预览版。

威胁防护是高级威胁防护 (ATP) 产品/服务的一部分，后者是一个针对高级安全功能的统一软件包。 可以通过[Azure 门户](https://portal.azure.com)或使用[REST API](/rest/api/postgresql/serversecurityalertpolicies)来访问和管理高级威胁防护。 此功能可用于常规用途和内存优化服务器。

> [!NOTE]
> 高级威胁防护功能在以下 Azure 政府和主权云区域中**不可用**：US Gov 德克萨斯州、US Gov 亚利桑那州、US Gov 爱荷华州、US Gov 弗吉尼亚、US DoD 东部、US DoD 中部、德国中部、德国北部、中国东部、中国东部 2。 请访问[各区域的产品可用性](https://azure.microsoft.com/global-infrastructure/services/)，以了解常规产品可用性。

## <a name="what-is-advanced-threat-protection"></a>什么是高级威胁防护？

Azure Database for PostgreSQL 高级威胁防护提供了一个新的安全层，它针对异常活动发出安全警报，让客户能够在潜在威胁出现时进行检测和应对。 出现可疑数据库活动、潜在漏洞以及异常数据库访问和查询模式时，用户将收到警报。 Azure Database for PostgreSQL 高级威胁防护将警报与 [Azure 安全中心](https://azure.microsoft.com/services/security-center/)集成，其中包含可疑活动的详细信息以及如何调查和缓解威胁的建议操作。 不必是安全专家，也不需要管理先进的安全监视系统，就能使用 Azure Database for PostgreSQL 高级威胁防护轻松解决数据库的潜在威胁。 

![高级威胁防护概念](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>高级威胁防护警报 
Azure Database for PostgreSQL 高级威胁防护可检测异常活动，指出有人在访问或利用数据库时的异常行为和可能有害的尝试，并可以触发以下警报：
- **来自异常位置的访问**：当 Azure Database for PostgreSQL 服务器的访问模式发生更改，有人从异常的地理位置登录到 Azure Database for PostgreSQL 服务器时，会触发此警报。 在某些情况下，警报会检测合法操作（发布新应用程序或开发人员维护）。 在其他情况下，警报会检测恶意操作（以前的员工、外部攻击者）。
- **来自异常 Azure 数据中心的访问**：当 Azure Database for PostgreSQL 服务器的访问模式发生更改，最近一段时间在此服务器上发现有人从异常 Azure 数据中心登录到服务器时，会触发此警报。 在某些情况下，警报会检测合法操作（在 Azure、Power BI 或 Azure Database for PostgreSQL 查询编辑器中发布新应用程序）。 在其他情况下，警报会检测通过 Azure 资源/服务执行的恶意操作（以前的员工、外部攻击者）。
- **来自陌生主体的访问**：当 Azure Database for PostgreSQL 服务器的访问模式发生更改，有人使用异常主体（Azure Database for PostgreSQL 用户）登录到服务器时，会触发此警报。 在某些情况下，警报会检测合法操作（发布新应用程序或开发人员维护）。 在其他情况下，警报会检测恶意操作（以前的员工、外部攻击者）。
- **来自可能有害的应用程序的访问**：当使用可能有害的应用程序访问数据库时，会触发此警报。 在某些情况下，警报会检测操作中的渗透测试。 在其他情况下，警报会检测使用常见攻击工具执行的攻击。
- **暴力破解 Azure Database for PostgreSQL 凭据**：当有人使用不同的凭据异常登录并失败很多次时，会触发此警报。 在某些情况下，警报会检测操作中的渗透测试。 在其他情况下，警报会检测暴力破解攻击。

## <a name="next-steps"></a>后续步骤

* 了解有关 [Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)的详细信息
* 有关定价的详细信息，请参阅 [Azure Database for PostgreSQL 定价页](https://azure.microsoft.com/pricing/details/postgresql/) 
* 使用 Azure 门户配置 [Azure Database for PostgreSQL 高级威胁防护](howto-database-threat-protection-portal.md)  
