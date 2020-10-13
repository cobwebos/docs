---
title: Azure Database for PostgreSQL（单一服务器）中的安全性
description: Azure Database for PostgreSQL（单一服务器）中的安全性功能概述。
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: be042a0ec076538cf0f0d155667acea6f1ae19cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91710475"
---
# <a name="security-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL（单一服务器）中的安全性

可以通过多层安全性来保护 Azure Database for PostgreSQL 服务器上的数据。 本文概述了这些安全选项。

## <a name="information-protection-and-encryption"></a>信息保护和加密

### <a name="in-transit"></a>动态
Azure Database for PostgreSQL 使用传输层安全性来加密动态数据，通过这种方式来保护数据。 默认情况下，强制实施加密 (SSL/TLS)。

### <a name="at-rest"></a>静态
Azure Database for PostgreSQL 服务使用 FIPS 140-2 验证的加密模块对静态数据进行存储加密。 数据（包括备份）在磁盘上加密，包括运行查询时创建的临时文件。 该服务使用包含在 Azure 存储加密中的 AES 256 位密码，并且密钥由系统进行管理。 存储加密始终处于启用状态，无法禁用。


## <a name="network-security"></a>网络安全性
到 Azure Database for PostgreSQL 服务器的连接首先通过区域性网关进行路由。 网关 IP 可以公开访问，而服务器 IP 地址则受保护。 有关网关的详细信息，请参阅[连接体系结构文章](concepts-connectivity-architecture.md)。  

新创建的 Azure Database for PostgreSQL 服务器有一个防火墙，可以阻止所有外部连接。 它们可以到达网关，但不能连接到服务器。 

### <a name="ip-firewall-rules"></a>IP 防火墙规则
IP 防火墙规则基于每个请求的起始 IP 地址授予对服务器的访问权限。 有关详细信息，请参阅[防火墙规则概述](concepts-firewall-rules.md)。

### <a name="virtual-network-firewall-rules"></a>虚拟网络防火墙规则
虚拟网络服务终结点将虚拟网络连接扩展到 Azure 主干网。 使用虚拟网络规则，Azure Database for PostgreSQL 服务器就会允许从虚拟网络中的所选子网进行连接。 有关详细信息，请参阅[虚拟网络服务终结点概述](concepts-data-access-and-security-vnet.md)。

### <a name="private-ip"></a>专用 IP
专用链接允许通过专用终结点连接到 Azure 中的 Azure Database for PostgreSQL 单一服务器。 Azure 专用链接实质上是将 Azure 服务引入专用虚拟网络 (VNet) 中。 就像 VNet 中的任何其他资源一样，可以使用专用 IP 地址访问 PaaS 资源。 有关详细信息，请参阅 [专用链接概述](concepts-data-access-and-security-private-link.md)


## <a name="access-management"></a>访问管理

在创建 Azure Database for PostgreSQL 服务器时，我们会提供管理员角色的凭据。 可以通过此管理员角色创建其他 [PostgreSQL 角色](https://www.postgresql.org/docs/current/user-manag.html)。

你还可以使用 [Azure Active Directory (AAD) 身份验证](concepts-aad-authentication.md)连接到服务器。


## <a name="threat-protection"></a>威胁防护

你可以选择 " [高级威胁防护](concepts-data-access-and-security-threat-protection.md) "，它会检测异常活动，这些活动表示对访问或利用服务器的异常和潜在有害尝试。

[审核日志记录](concepts-audit.md) 可用于跟踪数据库中的活动。 

## <a name="migrating-from-oracle"></a>从 Oracle 迁移

Oracle 支持透明数据加密 (TDE) 加密表和表空间数据。 在 Azure for PostgreSQL 中，数据在不同层自动加密。 请参阅本页中的 "静止" 部分，并参阅各个安全主题，其中包括 [客户托管密钥](./concepts-data-encryption-postgresql.md) 和 [基础结构双加密](./concepts-infrastructure-double-encryption.md)。 你还可以考虑使用 Azure 中支持[的用于 PostgreSQL](./concepts-extensions.md)的[pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html)扩展。

## <a name="next-steps"></a>后续步骤
- 为 [IP](concepts-firewall-rules.md) 或[虚拟网络](concepts-data-access-and-security-vnet.md)启用防火墙规则
- 了解 Azure Database for PostgreSQL 中的[Azure Active Directory 身份验证](concepts-aad-authentication.md)
