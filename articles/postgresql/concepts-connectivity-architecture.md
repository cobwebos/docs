---
title: Azure Database for PostgreSQL 中的连接体系结构
description: 介绍 Azure Database for PostgreSQL 服务器的连接体系结构。
author: kummanish
ms.author: manishku
ms.service: PostgreSQL
ms.topic: conceptual
ms.date: 05/23/2019
ms.openlocfilehash: 73c23c471cb12ca3a3a7df4380779b464b8d86d4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66735731"
---
# <a name="connectivity-architecture-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL 中的连接体系结构
本文介绍 Azure Database for PostgreSQL 连接体系结构以及如何从内部和外部 Azure 客户端流量定向到 Azure Database for PostgreSQL 数据库实例。

## <a name="connectivity-architecture"></a>连接体系结构
通过负责到我们的群集中的服务器的物理位置的路由传入连接的网关建立到 Azure Database for PostgreSQL 的连接。 下图说明了流量流。

![连接体系结构概述](./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png)

作为客户端连接到数据库，则出现的连接字符串用于连接到网关。 此网关具有公共 IP 地址的侦听端口 5432。 在数据库内 clusterz 流量转发到相应 Azure Database for PostgreSQL。 因此，若要从公司网络，例如连接到你的服务器，则需要打开客户端端防火墙以允许出站流量必须能够访问我们的网关。 您可以在下面找到我们每个区域的网关使用的 IP 地址的完整列表。

## <a name="azure-database-for-postgresql-gateway-ip-addresses"></a>Azure Database for PostgreSQL 网关 IP 地址
下表列出了 Azure Database for PostgreSQL 网关的所有数据区域的主要和次要 Ip。 主 IP 地址是网关的当前 IP 地址和第二个 IP 地址是主数据库的故障的故障转移 IP 地址。 如前文所述，客户应允许出站到这两个 IP 地址。 第二个 IP 地址不会侦听任何服务 Azure Database for PostgreSQL 为接受连接激活之前。

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
| 英国南部 | 51.140.184.11 | |
| 英国西部 | 51.141.8.11| |
| 西欧 | 191.237.232.75 | 40.68.37.158 |
| 美国西部 1 | 23.99.34.75 | 104.42.238.205 |
| 美国西部 2 | 13.66.226.202 | |
||||

> [!NOTE]
> *美国东部 2* 还有第三个 IP 地址，即 `52.167.104.0`。

## <a name="next-steps"></a>后续步骤

* [使用 Azure 门户创建和管理 Azure Database for PostgreSQL 防火墙规则](./howto-manage-firewall-using-portal.md)
* [使用 Azure CLI 创建和管理 Azure Database for PostgreSQL 防火墙规则](./howto-manage-firewall-using-cli.md)
