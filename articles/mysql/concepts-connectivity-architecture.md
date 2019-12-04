---
title: 连接体系结构-Azure Database for MySQL
description: 描述 Azure Database for MySQL 服务器的连接体系结构。
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 22c77bee95533606156ec6cc337af1d743018005
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74765318"
---
# <a name="connectivity-architecture-in-azure-database-for-mysql"></a>Azure Database for MySQL 中的连接体系结构
本文介绍了 Azure Database for MySQL 连接体系结构，以及如何从 Azure 内部和外部的客户端将流量定向到 Azure Database for MySQL 实例。

## <a name="connectivity-architecture"></a>连接体系结构
通过负责将传入连接路由到群集中服务器的物理位置的网关建立与 Azure Database for MySQL 的连接。 下图说明了流量。

![连接体系结构概述](./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png)

当客户端连接到数据库时，它们会获得连接到网关的连接字符串。 此网关具有侦听端口3306的公共 IP 地址。 在数据库群集中，会将流量转发到相应的 Azure Database for MySQL。 因此，若要连接到您的服务器（例如公司网络），必须打开客户端防火墙以允许出站流量访问我们的网关。 你可以在下面找到每个区域的网关使用的 IP 地址的完整列表。

## <a name="azure-database-for-mysql-gateway-ip-addresses"></a>Azure Database for MySQL 网关 IP 地址
下表列出了适用于所有数据区域的 Azure Database for MySQL 网关的主 Ip 和辅助 Ip。 主 IP 地址是网关的当前 IP 地址，第二个 IP 地址是故障转移 IP 地址（在主站点发生故障的情况下）。 如前所述，客户应该允许出站发送到两个 IP 地址。 第二个 IP 地址在激活之前不会在任何服务上进行侦听，Azure Database for MySQL 接受连接。

| **区域名称** | **主 IP 地址** | **辅助 IP 地址** |
|:----------------|:-------------|:------------------------|
| 澳大利亚东部 | 13.75.149.87 | 40.79.161.1 |
| 澳大利亚东南部 | 191.239.192.109 | 13.73.109.251 |
| 巴西南部 | 104.41.11.5 | |
| 加拿大中部 | 40.85.224.249 | |
| 加拿大东部 | 40.86.226.166 | |
| 美国中部 | 23.99.160.139 | 13.67.215.62 |
| 中国东部 1 | 139.219.130.35 | |
| 中国东部 2 | 40.73.82.1 | |
| 中国北部 1 | 139.219.15.17 | |
| 中国北部 2 | 40.73.50.0 | |
| 亚洲东部 | 191.234.2.139 | 52.175.33.150 |
| 美国东部 1 | 191.238.6.43 | 40.121.158.30 |
| 美国东部 2 | 191.239.224.107 | 40.79.84.180 * |
| 法国中部 | 40.79.137.0 | 40.79.129.1 |
| 德国中部 | 51.4.144.100 | |
| 印度中部 | 104.211.96.159 | |
| 印度南部 | 104.211.224.146 | |
| 印度西部 | 104.211.160.80 | |
| 日本东部 | 191.237.240.43 | 13.78.61.196 |
| 日本西部 | 191.238.68.11 | 104.214.148.156 |
| 韩国中部 | 52.231.32.42 | |
| 韩国南部 | 52.231.200.86 |  |
| 美国中北部 | 23.98.55.75 | 23.96.178.199 |
| 北欧 | 191.235.193.75 | 40.113.93.91 |
| 美国中南部 | 23.98.162.75 | 13.66.62.124 |
| 亚洲东南部 | 23.100.117.95 | 104.43.15.0 |
| 南非北部 | 102.133.152.0 | |
| 南非西部 | 102.133.24.0 | |
| 阿拉伯联合酋长国北部 | 65.52.248.0 | |
| 英国南部 | 51.140.184.11 | |
| 英国西部 | 51.141.8.11| |
| 欧洲西部 | 191.237.232.75 | 40.68.37.158 |
| 美国西部 1 | 23.99.34.75 | 104.42.238.205 |
| 美国西部 2 | 13.66.226.202 | |
||||

> [!NOTE]
> *美国东部 2*也有 `52.167.104.0`的第三个 IP 地址。

## <a name="next-steps"></a>后续步骤

* [使用 Azure 门户创建和管理 Azure Database for MySQL 防火墙规则](./howto-manage-firewall-using-portal.md)
* [使用 Azure CLI 创建和管理 Azure Database for MySQL 防火墙规则](./howto-manage-firewall-using-cli.md)