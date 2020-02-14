---
title: 安全控件
description: 用于评估 Azure SQL 数据库的安全控制清单
services: sql-database
author: msmbaldwin
manager: rkalrin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: ce7f3eafa57cbd993be98f4a2da3d89cb312f9b7
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190697"
---
# <a name="security-controls-for-azure-sql-database"></a>适用于 Azure SQL 数据库的安全控制

本文介绍 Azure SQL 数据库中内置的安全控制。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

SQL 数据库同时包含[单一数据库](sql-database-single-index.yml)和[托管实例](sql-database-managed-instance.md)。 以下条目适用于这两个产品/服务，但在其他情况下除外。

## <a name="network"></a>网络

| 安全控制 | Yes/No | 注意 |
|---|---|--|
| 服务终结点支持| 是 | 仅适用于[单个数据库](sql-database-single-index.yml)。 |
| Azure 虚拟网络注入支持| 是 | 仅适用于[托管实例](sql-database-managed-instance.md)。 |
| 网络隔离和防火墙支持| 是 | 数据库级别和服务器级别的防火墙。 网络隔离仅适用于[托管实例](sql-database-managed-instance.md)。 |
| 强制隧道支持| 是 | [托管实例](sql-database-managed-instance.md)通过[ExpressRoute](../expressroute/index.yml) VPN。 |

## <a name="monitoring--logging"></a>监视 & 日志记录

| 安全控制 | Yes/No | 注意|
|---|---|--|
| Azure 监视支持，如 Log Analytics 或 Application Insights| 是 | SecureSphere 是 Imperva 的 SIEM 解决方案，也通过[SQL 审核](sql-database-auditing.md)通过[Azure 事件中心](../event-hubs/index.yml)集成来支持。 |
| 控制平面和管理平面日志记录和审核| 是 | 对于某些事件是唯一的 |
| 数据平面日志记录和审核 | 是 | 通过[SQL 审核](sql-database-auditing.md) |

## <a name="identity"></a>身份

| 安全控制 | Yes/No | 注意|
|---|---|--|
| 身份验证| 是 | Azure Active Directory (Azure AD) |
| 授权| 是 | 无 |

## <a name="data-protection"></a>数据保护

| 安全控制 | Yes/No | 注意 |
|---|---|--|
| 服务器端加密： Microsoft 托管密钥 | 是 | 称为 "正在使用的加密"，如[Always Encrypted](sql-database-always-encrypted.md)中所述。 服务器端加密使用[透明数据加密](transparent-data-encryption-azure-sql.md)。|
| 传输中的加密：<ul><li>Azure ExpressRoute 加密</li><li>虚拟网络中的加密</li><li>虚拟网络之间的加密</ul>| 是 | 使用 HTTPS。 |
| 加密密钥处理，如 CMK 或 BYOK| 是 | 提供服务管理的和客户管理的密钥处理。 后者通过[Azure Key Vault](../key-vault/index.yml)提供。 |
| Azure 数据服务提供的列级加密| 是 | 通过[Always Encrypted](sql-database-always-encrypted.md)。 |
| 加密的 API 调用| 是 | 使用 HTTPS/TLS。 |

## <a name="configuration-management"></a>配置管理

| 安全控制 | Yes/No | 注意|
|---|---|--|
| 配置管理支持，例如配置的版本控制| 是  | 无 |

## <a name="additional-security-controls-for-sql-database"></a>SQL 数据库的其他安全控件

| 安全控制 | Yes/No | 注意|
|---|---|--|
| 预防：漏洞评估 | 是 | 请参阅[SQL 漏洞评估服务帮助你确定数据库漏洞](sql-vulnerability-assessment.md)。 |
| 预防：数据发现和分类  | 是 | 请参阅[AZURE Sql 数据库和 SQL 数据仓库数据发现 & 分类](sql-database-data-discovery-and-classification.md)。 |
| 检测：威胁检测 | 是 | 请参阅[AZURE SQL 数据库的高级威胁防护](sql-database-threat-detection-overview.md)。 |

## <a name="next-steps"></a>后续步骤

- 详细了解[Azure 服务中的内置安全控件](../security/fundamentals/security-controls.md)。
