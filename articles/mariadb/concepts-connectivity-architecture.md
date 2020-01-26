---
title: 连接体系结构-Azure Database for MariaDB
description: 描述 Azure Database for MariaDB 服务器的连接体系结构。
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 58f6e49280925b550deff21ecf04528d91d924d7
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760124"
---
# <a name="connectivity-architecture-in-azure-database-for-mariadb"></a>Azure Database for MariaDB 中的连接体系结构
本文介绍了 Azure Database for MariaDB 连接体系结构，以及如何从 Azure 内部和外部的客户端将流量定向到 Azure Database for MariaDB 实例。

## <a name="connectivity-architecture"></a>连接体系结构

通过负责将传入连接路由到群集中服务器的物理位置的网关建立与 Azure Database for MariaDB 的连接。 下图说明了流量。

![连接体系结构概述](./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png)

当客户端连接到数据库时，它们会获得连接到网关的连接字符串。 此网关具有侦听端口3306的公共 IP 地址。 在数据库群集中，会将流量转发到相应的 Azure Database for MariaDB。 因此，若要连接到您的服务器（例如公司网络），必须打开客户端防火墙以允许出站流量访问我们的网关。 你可以在下面找到每个区域的网关使用的 IP 地址的完整列表。

## <a name="azure-database-for-mariadb-gateway-ip-addresses"></a>Azure Database for MariaDB 网关 IP 地址

下表列出了适用于所有数据区域的 Azure Database for MariaDB 网关的主 Ip 和辅助 Ip。 主 IP 地址是网关的当前 IP 地址，第二个 IP 地址是故障转移 IP 地址（在主站点发生故障的情况下）。 如前所述，客户应该允许出站发送到两个 IP 地址。 第二个 IP 地址在激活之前不会在任何服务上进行侦听，Azure Database for MariaDB 接受连接。

| **区域名称** | **网关 IP 地址** |
|:----------------|:-------------|
| 澳大利亚中部| 20.36.105.0     |
| 澳大利亚 Central2     | 20.36.113.0   |
| 澳大利亚东部 | 13.75.149.87, 40.79.161.1     |
| 澳大利亚东南部 |191.239.192.109, 13.73.109.251   |
| 巴西南部 | 104.41.11.5, 191.233.201.8, 191.233.200.16  |
| 加拿大中部 |40.85.224.249  |
| 加拿大东部 | 40.86.226.166    |
| 美国中部 | 23.99.160.139, 13.67.215.62   |
| 中国东部 2 | 40.73.82.1  |
| 中国北部 2 | 40.73.50.0     |
| 亚洲东部 | 191.234.2.139, 52.175.33.150, 13.75.33.20, 13.75.33.21     |
| 美国东部 | 40.121.158.30, 191.238.6.43  |
| 美国东部 2 |40.79.84.180, 191.239.224.107, 52.177.185.181   |
| 法国中部 | 40.79.137.0, 40.79.129.1  |
| 德国中部 | 51.4.144.100     |
| 德国东北部 | 51.5.144.179  |
| 印度中部 | 104.211.96.159     |
| 印度南部 | 104.211.224.146  |
| 印度西部 | 104.211.160.80    |
| 日本东部 | 13.78.61.196, 191.237.240.43  |
| 日本西部 | 104.214.148.156, 191.238.68.11    |
| 韩国中部 | 52.231.32.42   |
| 韩国南部 | 52.231.200.86    |
| 美国中北部 | 23.96.178.199, 23.98.55.75, 52.162.104.35, 52.162.104.36    |
| 欧洲北部 | 40.113.93.91, 191.235.193.75    |
| 南非北部  | 102.133.152.0    |
| 南非西部 | 102.133.24.0   |
| 美国中南部 |13.66.62.124, 23.98.162.75   |
| 亚洲东南部 | 104.43.15.0, 23.100.117.95, 40.78.233.2, 23.98.80.12     |
| 阿拉伯联合酋长国中部 | 20.37.72.64  |
| 阿拉伯联合酋长国北部 | 65.52.248.0    |
| 英国南部 | 51.140.184.11   |
| 英国西部 | 51.141.8.11  |
| 美国中西部 | 13.78.145.25     |
| 欧洲西部 | 40.68.37.158, 191.237.232.75     |
| 美国西部 | 104.42.238.205, 23.99.34.75  |
| 美国西部 2 | 13.66.226.202  |
||||

## <a name="next-steps"></a>后续步骤

* [使用 Azure 门户创建和管理 Azure Database for MariaDB 防火墙规则](./howto-manage-firewall-portal.md)
* [使用 Azure CLI 创建和管理 Azure Database for MariaDB 防火墙规则](./howto-manage-firewall-cli.md)
