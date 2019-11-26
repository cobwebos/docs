---
title: Azure Database for MySQL 中的安全性-单一服务器
description: Azure Database for MySQL-单服务器中的安全功能概述。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 11/25/2019
ms.openlocfilehash: 06f895d2e8cd7936141c9f35c5ed0c81fb7e3e15
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74485050"
---
# <a name="security-in-azure-database-for-mysql---single-server"></a>Azure Database for MySQL 中的安全性-单一服务器

有多个安全层可用于保护 Azure Database for MySQL 服务器上的数据。 本文概述了这些安全选项。

## <a name="information-protection-and-encryption"></a>信息保护和加密

### <a name="in-transit"></a>传输中
Azure Database for MySQL 通过使用传输层安全性对传输中的数据进行加密来保护数据。 默认情况下，强制实施加密（SSL/TLS）。

### <a name="at-rest"></a>静止
Azure Database for MySQL 服务使用 FIPS 140-2 验证的加密模块对静态数据进行存储加密。 数据（包括备份）在磁盘上加密，在运行查询时创建的临时文件除外。 该服务使用包含在 Azure 存储加密中的 AES 256 位密码，并且密钥由系统进行管理。 存储加密始终处于启用状态，无法禁用。


## <a name="network-security"></a>网络安全
与 Azure Database for MySQL 服务器的连接先通过区域网关进行路由。 网关具有可公开访问的 IP，而服务器 IP 地址受到保护。 有关网关的详细信息，请访问[连接体系结构一文](concepts-connectivity-architecture.md)。  

新创建的 Azure Database for MySQL 服务器具有阻止所有外部连接的防火墙。 尽管它们到达网关，但不允许它们连接到服务器。 

### <a name="ip-firewall-rules"></a>IP 防火墙规则
IP 防火墙规则根据每个请求的起始 IP 地址授予对服务器的访问权限。 有关详细信息，请参阅[防火墙规则概述](concepts-firewall-rules.md)。

### <a name="virtual-network-firewall-rules"></a>虚拟网络防火墙规则
虚拟网络服务终结点通过 Azure 主干扩展你的虚拟网络连接。 使用虚拟网络规则，你可以使 Azure Database for MySQL 服务器允许来自虚拟网络中所选子网的连接。 有关详细信息，请参阅[虚拟网络服务终结点概述](concepts-data-access-and-security-vnet.md)。


## <a name="access-management"></a>访问管理

创建 Azure Database for MySQL 服务器时，需要为管理员用户提供凭据。 此管理员可用于创建其他 MySQL 用户。


## <a name="threat-protection"></a>威胁防护

你可以选择 "[高级威胁防护](concepts-data-access-and-security-threat-protection.md)"，它会检测异常活动，这些活动表示对访问或利用服务器的异常和潜在有害尝试。

[审核日志记录](concepts-audit-logs.md)可用于跟踪数据库中的活动。 


## <a name="next-steps"></a>后续步骤
- 为[IPs](concepts-firewall-rules.md)或[虚拟网络](concepts-data-access-and-security-vnet.md)启用防火墙规则