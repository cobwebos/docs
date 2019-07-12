---
title: Azure SQL 数据库的安全属性
description: 用于评估 Azure SQL 数据库的安全属性的清单
services: sql-database
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mbaldwin
ms.openlocfilehash: 71e87a3295a9cd73dca2f97b3fa04a5d5ff76f84
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798683"
---
# <a name="security-attributes-for-azure-sql-database"></a>Azure SQL 数据库的安全属性

本文介绍内置于 Azure SQL 数据库的常见安全属性。

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

SQL 数据库包括[单一数据库](sql-database-single-index.yml)并[托管的实例](sql-database-managed-instance.md)。 以下各项适用于这两个产品/服务，除非另行说明。

## <a name="preventative"></a>预防

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 静态加密：<ul><li>服务器端加密</li><li>使用客户托管密钥的服务器端加密</li><li>其他加密功能，例如客户端或始终加密</ul>| 是 | 名为"加密--使用中，"文章中所述[Always Encrypted](sql-database-always-encrypted.md)。 使用服务器端加密[透明数据加密](transparent-data-encryption-azure-sql.md)。|
| 传输中加密：<ul><li>Azure ExpressRoute 加密</li><li>虚拟网络中的加密</li><li>虚拟网络之间的加密</ul>| 是 | 使用 HTTPS。 |
| 加密密钥处理，如 CMK 或 BYOK| 是 | 提供服务托管和客户托管密钥的处理。 提供通过后者[Azure 密钥保管库](../key-vault/index.yml)。 |
| Azure 数据服务提供的列级加密| 是 | 通过 [Always Encrypted](sql-database-always-encrypted.md) 进行。 |
| 加密的 API 调用| 是 | 使用 HTTPS/SSL。 |

## <a name="network-segmentation"></a>网络分段

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 服务终结点支持| 是 | 仅适用于[单一数据库](sql-database-single-index.yml)。 |
| Azure 虚拟网络注入支持| 是 | 适用于[托管的实例](sql-database-managed-instance.md)仅。 |
| 网络隔离和防火墙支持| 是 | 在数据库级别和服务器级别的防火墙。 网络隔离的是[托管的实例](sql-database-managed-instance.md)仅。 |
| 强制隧道支持| 是 | [托管的实例](sql-database-managed-instance.md)通过[ExpressRoute](../expressroute/index.yml) VPN。 |

## <a name="detection"></a>检测

| 安全属性 | Yes/No | 说明|
|---|---|--|
| Azure 监视支持，例如 Log Analytics 或 Application Insights| 是 | SecureSphere，来自 Imperva，SIEM 解决方案也支持通过[Azure 事件中心](../event-hubs/index.yml)通过集成[SQL 审核](sql-database-auditing.md)。 |

## <a name="identity-and-access-management"></a>标识和访问管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 身份验证| 是 | Azure Active Directory (Azure AD) |
| Authorization| 是 | 无 |

## <a name="audit-trail"></a>审核线索

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 控制平面和管理平面的日志记录和审核| 是 | 对于某些事件仅是 |
| 数据平面日志记录和审核 | 是 | 通过[SQL 审核](sql-database-auditing.md) |

## <a name="configuration-management"></a>配置管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 配置管理支持，如版本控制的配置| 否  | 无 |

## <a name="additional-security-attributes-for-sql-database"></a>SQL 数据库的其他安全属性

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 预防：漏洞评估 | 是 | 请参阅 [SQL 漏洞评估服务可帮助你识别数据库漏洞](sql-vulnerability-assessment.md)。 |
| 预防：数据发现和分类  | 是 | 请参阅 [Azure SQL 数据库和 SQL 数据仓库数据发现和分类](sql-database-data-discovery-and-classification.md)。 |
| 检测：威胁检测 | 是 | 请参阅 [Azure SQL 数据库的高级威胁防护](sql-database-threat-detection-overview.md)。 |
