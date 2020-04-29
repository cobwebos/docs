---
title: 连接体系结构 - Azure Database for MySQL
description: 描述 Azure Database for MySQL 服务器的连接体系结构。
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 6014e98d01755f29da74160fb1ef38ba29a74ba6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80547494"
---
# <a name="connectivity-architecture-in-azure-database-for-mysql"></a>Azure Database for MySQL 中的连接体系结构
本文介绍 Azure Database for MySQL 的连接体系结构，以及如何在 Azure 内部和外部将流量从客户端定向到 Azure Database for MySQL 实例。

## <a name="connectivity-architecture"></a>连接体系结构
可以通过网关连接到 Azure Database for MySQL，该网关负责将传入连接路由到服务器在群集中的物理位置。 下图演示了流量流。

![连接体系结构概述](./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png)

客户端在连接到数据库时，会获得一个用于连接到网关的连接字符串。 该网关有一个公共 IP 地址，用于侦听端口 3306。 在数据库群集中，流量转发到相应的 Azure Database for MySQL。 因此，为了通过某种方式（例如，通过公司网络）连接到服务器，必须打开客户端防火墙，使出站流量能够访问我们的网关。 下面是一个按区域分类的可供我们的网关使用的 IP 地址的完整列表。

## <a name="azure-database-for-mysql-gateway-ip-addresses"></a>Azure Database for MySQL 网关 IP 地址
下表列出了所有数据区域的 Azure Database for MySQL 网关的主要 IP 和次要 IP。 主 IP 地址是网关的当前 IP 地址，第二个 IP 地址是主 IP 地址故障时使用的故障转移 IP 地址。 如前所述，客户应该允许到这两个 IP 地址的出站流量。 第二个 IP 地址不侦听任何服务，除非 Azure Database for MySQL 激活该地址，使之接受连接。

| **区域名称** | **网关 IP 地址** |
|:----------------|:-------------|
| 澳大利亚中部| 20.36.105.0     |
| 澳大利亚 Central2     | 20.36.113.0   |
| 澳大利亚东部 | 13.75.149.87, 40.79.161.1     |
| 澳大利亚东南部 |191.239.192.109, 13.73.109.251   |
| 巴西南部 | 104.41.11.5, 191.233.201.8, 191.233.200.16  |
| 加拿大中部 |40.85.224.249  |
| 加拿大东部 | 40.86.226.166    |
| 美国中部 | 23.99.160.139, 13.67.215.62, 52.182.136.37, 52.182.136.38     |
| 中国东部 | 139.219.130.35    |
| 中国东部 2 | 40.73.82.1  |
| 中国北部 | 139.219.15.17    |
| 中国北部 2 | 40.73.50.0     |
| 东亚 | 191.234.2.139, 52.175.33.150, 13.75.33.20, 13.75.33.21     |
| 美国东部 | 40.121.158.30, 191.238.6.43  |
| 美国东部 2 |40.79.84.180, 191.239.224.107, 52.177.185.181, 40.70.144.38, 52.167.105.38  |
| 法国中部 | 40.79.137.0, 40.79.129.1  |
| 德国中部 | 51.4.144.100     |
| 德国东北部 | 51.5.144.179  |
| 印度中部 | 104.211.96.159     |
| 印度南部 | 104.211.224.146  |
| 印度西部 | 104.211.160.80    |
| 日本东部 | 13.78.61.196, 191.237.240.43  |
| 日本西部 | 104.214.148.156, 191.238.68.11, 40.74.96.6, 40.74.96.7    |
| 韩国中部 | 52.231.32.42   |
| 韩国南部 | 52.231.200.86    |
| 美国中北部 | 23.96.178.199, 23.98.55.75, 52.162.104.35, 52.162.104.36    |
| 北欧 | 40.113.93.91, 191.235.193.75, 52.138.224.6, 52.138.224.7    |
| 南非北部  | 102.133.152.0    |
| 南非西部 | 102.133.24.0   |
| 美国中南部 |13.66.62.124, 23.98.162.75, 104.214.16.39, 20.45.120.0   |
| 东南亚 | 104.43.15.0, 23.100.117.95, 40.78.233.2, 23.98.80.12     |
| 阿联酋中部 | 20.37.72.64  |
| 阿拉伯联合酋长国北部 | 65.52.248.0    |
| 英国南部 | 51.140.184.11   |
| 英国西部 | 51.141.8.11  |
| 美国中西部 | 13.78.145.25     |
| 西欧 | 40.68.37.158, 191.237.232.75, 13.69.105.208  |
| 美国西部 | 104.42.238.205, 23.99.34.75  |
| 美国西部 2 | 13.66.226.202  |
||||

## <a name="connection-redirection"></a>连接重定向

Azure Database for MySQL 支持附加的连接策略（**重定向**），有助于减少客户端应用程序和 MySQL 服务器之间的网络延迟。 利用此功能，在建立到 Azure Database for MySQL 服务器的初始 TCP 会话后，服务器会将发起 MySQL 服务器的节点的后端地址返回到客户端。 此后，所有后续数据包将直接流向服务器，绕过网关。 当数据包直接流向服务器时，延迟和吞吐量会提高性能。

具有引擎版本5.6、5.7 和8.0 的 Azure Database for MySQL 服务器支持此功能。

支持重定向在 Microsoft 开发的 PHP [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure)扩展中提供，可在[PECL](https://pecl.php.net/package/mysqlnd_azure)上找到。 有关如何在应用程序中使用重定向的详细信息，请参阅[配置重定向](./howto-redirection.md)一文。

> [!IMPORTANT]
> PHP [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure)扩展中对重定向的支持目前以预览版提供。

## <a name="next-steps"></a>后续步骤

* [使用 Azure 门户创建和管理 Azure Database for MySQL 防火墙规则](./howto-manage-firewall-using-portal.md)
* [使用 Azure CLI 创建和管理 Azure Database for MySQL 防火墙规则](./howto-manage-firewall-using-cli.md)
* [配置与 Azure Database for MySQL 的重定向](./howto-redirection.md)