---
title: 高级威胁防护 - MariaDB 的 Azure 数据库
description: 高级威胁防护会检测异常的数据库活动，指出对数据库的潜在安全威胁。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 8485907eba75f80c8f0ed4fd0cc7368c6147b9fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79532172"
---
# <a name="azure-database-for-mariadb-advanced-threat-protection"></a>用于 MariaDB 高级威胁保护的 Azure 数据库

MariaDB Azure 数据库的高级威胁保护可检测异常活动，指示访问或利用数据库的异常和潜在有害尝试。

> [!IMPORTANT]
> 高级威胁防护处于公共预览版。

高级威胁防护包含在高级数据安全产品/服务中，是一个针对高级安全功能的统一软件包。 高级威胁防护可以通过[Azure 门户](https://portal.azure.com)进行访问和管理。 此功能可用于通用和内存优化服务器。

> [!NOTE]
> 高级威胁防护功能在以下 Azure 政府和主权云区域中不**** 可用：US Gov 德克萨斯州、US Gov 亚利桑那州、US Gov 爱荷华州、US Gov 弗吉尼亚州、US DoD 东部、US DoD 中部、德国中部、德国北部、中国东部、中国东部 2。 请访问[各区域的产品可用性](https://azure.microsoft.com/global-infrastructure/services/)，以了解常规产品可用性。


## <a name="what-is-advanced-threat-protection"></a>什么是高级威胁防护？

MariaDB Azure 数据库的高级威胁保护提供了新的安全层，使客户能够通过为异常活动提供安全警报来检测和响应潜在威胁。 出现可疑数据库活动、潜在漏洞以及异常数据库访问和查询模式时，用户将收到警报。 MariaDB 的 Azure 数据库高级威胁保护将警报与[Azure 安全中心](https://azure.microsoft.com/services/security-center/)集成，其中包括可疑活动的详细信息，并建议就如何调查和缓解威胁采取行动。 MariaDB Azure 数据库的高级威胁保护使解决数据库的潜在威胁变得简单，而无需成为安全专家或管理高级安全监控系统。 

![高级威胁防护概念](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>高级威胁防护警报 
MariaDB Azure 数据库的高级威胁保护可检测异常活动，指示访问或利用数据库的异常和潜在有害尝试，并可能引发以下警报：
- **从异常位置访问**：当 MariaDB 服务器的 Azure 数据库访问模式发生更改时触发此警报，其中有人从异常地理位置登录到 MariaDB 服务器的 Azure 数据库。 在某些情况下，警报会检测合法操作（发布新应用程序或开发人员维护）。 在其他情况下，警报会检测恶意操作（以前的员工、外部攻击者）。
- **从异常 Azure 数据中心访问**：当 MariaDB 服务器对 Azure 数据库的访问模式发生更改时触发此警报，其中有人从最近一段时间内在此服务器上看到的异常 Azure 数据中心登录到服务器。 在某些情况下，警报会检测合法操作（Azure 中的新应用程序 Power BI）。 在其他情况下，警报会检测通过 Azure 资源/服务执行的恶意操作（以前的员工、外部攻击者）。
- **来自不熟悉主体的访问**：当 MariaDB 服务器的 Azure 数据库访问模式发生更改时触发此警报，其中有人使用异常主体（MariaDB 用户的 Azure 数据库）登录到服务器。 在某些情况下，警报会检测合法操作（发布新应用程序或开发人员维护）。 在其他情况下，警报会检测恶意操作（以前的员工、外部攻击者）。
- **从可能有害的应用程序访问**：当使用可能有害的应用程序访问数据库时，会触发此警报。 在某些情况下，警报会检测操作中的渗透测试。 在其他情况下，警报会检测使用常见攻击工具执行的攻击。
- **MariaDB 凭据的强制 Azure 数据库**：当具有不同凭据的异常大量失败登录时触发此警报。 在某些情况下，警报会检测操作中的渗透测试。 在其他情况下，警报会检测暴力破解攻击。

## <a name="next-steps"></a>后续步骤

* 了解有关[Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)
* 有关定价的详细信息，请参阅[MariaDB 定价的 Azure 数据库页面](https://azure.microsoft.com/pricing/details/mariadb/) 
* 使用 Azure 门户[为 MariaDB 高级威胁保护](howto-database-threat-protection-portal.md)配置 Azure 数据库  
