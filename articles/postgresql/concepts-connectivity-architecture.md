---
title: Azure Database for PostgreSQL 中的连接体系结构
description: 描述 Azure Database for PostgreSQL 服务器的连接体系结构。
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 05/23/2019
ms.openlocfilehash: 5288c3025e523e767082f4ab7cfbb08805a74eaf
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500088"
---
# <a name="connectivity-architecture-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL 中的连接体系结构
本文介绍 Azure Database for PostgreSQL 的连接体系结构，以及如何在 Azure 内部和外部将流量从客户端定向到 Azure Database for PostgreSQL 数据库实例。

## <a name="connectivity-architecture"></a>连接体系结构
可以通过网关连接到 Azure Database for PostgreSQL，该网关负责将传入连接路由到服务器在群集中的物理位置。 下图演示了流量流。

![连接体系结构概述](./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png)

客户端在连接到数据库时，会获得一个用于连接到网关的连接字符串。 此网关有一个公共 IP 地址，用于侦听端口 5432。 在数据库群集中，流量将转发到相应的 Azure Database for PostgreSQL。 因此，为了通过某种方式（例如，通过公司网络）连接到服务器，必须打开客户端防火墙，使出站流量能够访问我们的网关。 下面是一个按区域分类的可供我们的网关使用的 IP 地址的完整列表。

## <a name="azure-database-for-postgresql-gateway-ip-addresses"></a>Azure Database for PostgreSQL 网关 IP 地址
下表列出了所有数据区域的 Azure Database for PostgreSQL 网关的主要 IP 和次要 IP。 主 IP 地址是网关的当前 IP 地址，第二个 IP 地址是主 IP 地址故障时使用的故障转移 IP 地址。 如前所述，客户应该允许到这两个 IP 地址的出站流量。 第二个 IP 地址不侦听任何服务，除非 Azure Database for PostgreSQL 激活该地址，使之接受连接。

| **区域名称** | **主 IP 地址** | **次要 IP 地址** |
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
| 东亚 | 191.234.2.139 | 52.175.33.150 |
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
| 东南亚 | 23.100.117.95 | 104.43.15.0 |
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
> *美国东部 2* 还有第三个 IP 地址，即 `52.167.104.0`。

## <a name="next-steps"></a>后续步骤

* [使用 Azure 门户创建和管理 Azure Database for PostgreSQL 防火墙规则](./howto-manage-firewall-using-portal.md)
* [使用 Azure CLI 创建和管理 Azure Database for PostgreSQL 防火墙规则](./howto-manage-firewall-using-cli.md)
