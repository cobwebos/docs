---
title: 安全控件
description: 用于评估 Azure SQL 数据库的安全控制的清单
services: sql-database
author: msmbaldwin
manager: rkarlin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: eec032ad56d00778627fc147761f61c03ba8bafd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89442082"
---
# <a name="security-controls-for-azure-sql-database-and-sql-managed-instance"></a>Azure SQL 数据库和 SQL 托管实例的安全控件
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

本文介绍 Azure SQL 数据库和 Azure SQL 托管实例中内置的安全控件。

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]



## <a name="network"></a>网络

| 安全控制 | Yes/No | 注释 |
|---|---|--|
| 服务终结点支持| 是 | 仅适用于 [SQL 数据库](../index.yml)。 |
| Azure 虚拟网络注入支持| 是 | 仅适用于 [SQL 托管实例](../managed-instance/sql-managed-instance-paas-overview.md)。 |
| 网络隔离和防火墙支持| 是 | 数据库级别和服务器级别的防火墙。 网络隔离仅适用于 [SQL 托管实例](../managed-instance/sql-managed-instance-paas-overview.md)。 |
| 强制隧道支持| 是 | 采用 [ExpressRoute](../expressroute/../index.yml) VPN 的 [SQL 托管实例](../managed-instance/sql-managed-instance-paas-overview.md)。 |

## <a name="monitoring--logging"></a>监视和日志记录

| 安全控制 | Yes/No | 注释|
|---|---|--|
| Azure 监视支持，如 Log Analytics 或 Application Insights| 是 | SecureSphere 是 Imperva 的 SIEM 解决方案，也通过 [Azure 事件中心](../event-hubs/../index.yml)集成（通过 [SQL 审核](../../azure-sql/database/auditing-overview.md)）获得支持。 |
| 控制平面和管理平面日志记录和审核| 是 | 仅对某些事件为“是” |
| 数据平面日志记录和审核 | 是 | 通过 [SQL 审核](../../azure-sql/database/auditing-overview.md) |

## <a name="identity"></a>标识

| 安全控制 | Yes/No | 注释|
|---|---|--|
| 身份验证| 是 | Azure Active Directory (Azure AD) |
| 授权| 是 | 无 |

## <a name="data-protection"></a>数据保护

| 安全控制 | Yes/No | 注释 |
|---|---|--|
| 服务器端静态加密：Microsoft 管理的密钥 | 是 | 称为“使用中加密”，详见 [Always Encrypted](always-encrypted-certificate-store-configure.md) 一文中的说明。 服务端加密使用[透明数据加密](transparent-data-encryption-tde-overview.md)。|
| 传输中加密：<ul><li>Azure ExpressRoute 加密</li><li>虚拟网络中的加密</li><li>虚拟网络之间的加密</ul>| 是 | 使用 HTTPS。 |
| 加密密钥处理，如 CMK 或 BYOK| 是 | 提供服务托管和客户托管密钥处理。 后者通过 [Azure Key Vault](../key-vault/../index.yml) 提供。 |
| Azure 数据服务提供的列级加密| 是 | 通过 [Always Encrypted](always-encrypted-certificate-store-configure.md) 进行。 |
| 加密的 API 调用| 是 | 使用 HTTPS/TLS。 |

## <a name="configuration-management"></a>配置管理

| 安全控制 | Yes/No | 注释|
|---|---|--|
| 配置管理支持（例如，配置的版本控制）| 否  | 无 |

## <a name="additional-security-controls-for-sql-database"></a>SQL 数据库的其他安全控制

| 安全控制 | Yes/No | 注释|
|---|---|--|
| 预防：漏洞评估 | 是 | 请参阅 [SQL 漏洞评估服务可帮助你识别数据库漏洞](sql-vulnerability-assessment.md)。 |
| 预防：数据发现和分类  | 是 | 请参阅 [AZURE SQL 数据库和 Azure Synapse Analytics 数据发现 & 分类](data-discovery-and-classification-overview.md)。 |
| 检测：威胁检测 | 是 | 请参阅 [Azure SQL 数据库的高级威胁防护](threat-detection-overview.md)。 |

## <a name="next-steps"></a>后续步骤

- 详细了解[跨 Azure 服务的内置安全控制](../../security/fundamentals/security-controls.md)。
