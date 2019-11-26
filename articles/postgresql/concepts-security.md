---
title: Security in Azure Database for PostgreSQL - Single Server
description: An overview of the security features in Azure Database for PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: a1bd9b8cbcbc785425c2d1870dc555ff91f695f7
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74485076"
---
# <a name="security-in-azure-database-for-postgresql---single-server"></a>Security in Azure Database for PostgreSQL - Single Server

There are multiple layers of security that are available to protect the data on your Azure Database for PostgreSQL server. This article outlines those security options.

## <a name="information-protection-and-encryption"></a>信息保护和加密

### <a name="in-transit"></a>In-transit
Azure Database for PostgreSQL secures your data by encrypting data in-transit with Transport Layer Security. Encryption (SSL/TLS) is enforced by default.

### <a name="at-rest"></a>At-rest
Azure Database for PostgreSQL 服务使用 FIPS 140-2 验证的加密模块对静态数据进行存储加密。 Data, including backups, are encrypted on disk, with the exception of temporary files created while running queries. 该服务使用包含在 Azure 存储加密中的 AES 256 位密码，并且密钥由系统进行管理。 存储加密始终处于启用状态，无法禁用。


## <a name="network-security"></a>网络安全
Connections to an Azure Database for PostgreSQL server are first routed through a regional gateway. The gateway has a publicly accessible IP, while the server IP addresses are protected. For more information about the gateway, visit the [connectivity architecture article](concepts-connectivity-architecture.md).  

A newly created Azure Database for PostgreSQL server has a firewall that blocks all external connections. Though they reach the gateway, they are not allowed to connect to the server. 

### <a name="ip-firewall-rules"></a>IP 防火墙规则
IP firewall rules grant access to servers based on the originating IP address of each request. See the [firewall rules overview](concepts-firewall-rules.md) for more information.

### <a name="virtual-network-firewall-rules"></a>虚拟网络防火墙规则
Virtual network service endpoints extend your virtual network connectivity over the Azure backbone. Using virtual network rules you can enable your Azure Database for PostgreSQL server to allow connections from selected subnets in a virtual network. For more information, see the [virtual network service endpoint overview](concepts-data-access-and-security-vnet.md).


## <a name="access-management"></a>访问管理

While creating the Azure Database for PostgreSQL server, you provide credentials for an administrator role. This administrator role can be used to create additional [PostgreSQL roles](https://www.postgresql.org/docs/current/user-manag.html).

You can also connect to the server using [Azure Active Directory (AAD) authentication](concepts-aad-authentication.md).


## <a name="threat-protection"></a>威胁防护

You can opt in to [Advanced Threat Protection](concepts-data-access-and-security-threat-protection.md) which detects anomalous activities indicating unusual and potentially harmful attempts to access or exploit servers.

[Audit logging](concepts-audit.md) is available to track activity in your databases. 


## <a name="next-steps"></a>后续步骤
- Enable firewall rules for [IPs](concepts-firewall-rules.md) or [virtual networks](concepts-data-access-and-security-vnet.md)
- Learn about [Azure Active Directory authentication](concepts-aad-authentication.md) in Azure Database for PostgreSQL
