---
title: 高级威胁防护-Azure Database for MariaDB
description: 高级威胁防护会检测异常的数据库活动，指出对数据库的潜在安全威胁。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 8485907eba75f80c8f0ed4fd0cc7368c6147b9fd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "79532172"
---
# <a name="azure-database-for-mariadb-advanced-threat-protection"></a>Azure Database for MariaDB 高级威胁防护

适用于 Azure Database for MariaDB 的高级威胁防护可检测异常活动，这些活动表明访问或利用数据库的异常和潜在有害尝试。

> [!IMPORTANT]
> 高级威胁防护是公开预览版。

高级威胁防护包含在高级数据安全产品/服务中，是一个针对高级安全功能的统一软件包。 高级威胁防护可通过 [Azure 门户](https://portal.azure.com)进行访问和管理。 此功能可用于常规用途和内存优化服务器。

> [!NOTE]
> 高级威胁防护功能在以下 Azure 政府和主权云区域中不**** 可用：US Gov 德克萨斯州、US Gov 亚利桑那州、US Gov 爱荷华州、US Gov 弗吉尼亚州、US DoD 东部、US DoD 中部、德国中部、德国北部、中国东部、中国东部 2。 请访问[各区域的产品可用性](https://azure.microsoft.com/global-infrastructure/services/)，以了解常规产品可用性。


## <a name="what-is-advanced-threat-protection"></a>什么是高级威胁防护？

适用于 Azure Database for MariaDB 的高级威胁防护提供了一个新的安全层，它使客户能够通过提供异常活动的安全警报来检测并响应潜在威胁。 出现可疑数据库活动、潜在漏洞以及异常数据库访问和查询模式时，用户将收到警报。 高级威胁防护 Azure Database for MariaDB 将警报与 [Azure 安全中心](https://azure.microsoft.com/services/security-center/)集成，其中包括可疑活动的详细信息以及如何调查和缓解威胁的建议操作。 针对 Azure Database for MariaDB 的高级威胁防护使得解决数据库的潜在威胁变得简单，无需成为安全专家，也不需要管理高级的安全监视系统。 

![高级威胁防护概念](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>高级威胁防护警报 
适用于 Azure Database for MariaDB 的高级威胁防护可检测异常活动，这些活动表明访问或利用数据库的异常和潜在有害尝试，并且它可能会触发以下警报：
- **从异常位置访问**：当 Azure Database for MariaDB 服务器的访问模式发生变化时，用户从异常地理位置登录到 Azure Database for MariaDB 服务器时，将触发此警报。 在某些情况下，警报会检测合法操作（发布新应用程序或开发人员维护）。 在其他情况下，警报会检测恶意操作（以前的员工、外部攻击者）。
- **从异常的 azure 数据中心访问**：当 Azure Database for MariaDB 服务器的访问模式发生更改，而该服务器在最近一段时间内在此服务器上发现的不寻常的 azure 数据中心登录到服务器时，将触发此警报。 在某些情况下，警报会检测 (新应用程序的合法操作，Power BI) 。 在其他情况下，警报会检测通过 Azure 资源/服务执行的恶意操作（以前的员工、外部攻击者）。
- **从不熟悉的主体访问**：当 Azure Database for MariaDB 服务器的访问模式发生更改，而用户使用异常的主体 (Azure Database for MariaDB 用户) 登录到服务器时，将触发此警报。 在某些情况下，警报会检测合法操作（发布新应用程序或开发人员维护）。 在其他情况下，警报会检测恶意操作（以前的员工、外部攻击者）。
- **来自可能有害的应用程序的访问**：当使用可能有害的应用程序访问数据库时，会触发此警报。 在某些情况下，警报会检测操作中的渗透测试。 在其他情况下，警报会检测使用常见攻击工具执行的攻击。
- **强力 Azure Database for MariaDB 凭据**：当使用不同凭据的失败登录失败时，将触发此警报。 在某些情况下，警报会检测操作中的渗透测试。 在其他情况下，警报会检测暴力破解攻击。

## <a name="next-steps"></a>后续步骤

* 了解有关 [Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)的详细信息
* 有关定价的详细信息，请参阅 [Azure Database for MariaDB 定价页](https://azure.microsoft.com/pricing/details/mariadb/) 
* 使用 Azure 门户配置[Azure Database for MariaDB 高级威胁防护](howto-database-threat-protection-portal.md)  
