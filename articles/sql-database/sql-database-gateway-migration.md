---
title: 网关流量迁移通知
description: 文章向用户提供有关 Azure SQL 数据库网关 IP 地址迁移的通知
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 07/01/2019
ms.openlocfilehash: fe35dc4c22f3852934cde0d6f33084b56266d514
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73807693"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Azure SQL 数据库流量迁移到较新的网关

随着 Azure 基础结构的改进，Microsoft 将定期刷新硬件，以确保我们提供最佳的客户体验。 在接下来的几个月里，我们计划添加基于较新的硬件代构建的网关，将流量迁移到它们，并最终停用某些区域中基于旧硬件构建的网关。  

客户将通过电子邮件和 Azure 门户中收到通知，提前通知每个区域中可用的网关的任何更改。 最新的信息将在[Azure SQL 数据库网关 IP 地址](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses)表中维护。

## <a name="impact-of-this-change"></a>此更改的影响

第一轮流量迁移到较新的网关定于**2019 年 10 月 14**日在以下区域进行：
- 巴西南部
- 美国西部
- 西欧
- 美国东部
- 美国中部
- 东南亚
- 美国中南部
- 北欧
- 美国中北部
- 日本西部
- 日本东部
- 美国东部 2
- 东亚

流量迁移将更改 DNS 为 SQL 数据库解析的公共 IP 地址。
如果您有：
- 对本地防火墙中任何特定网关的 IP 地址进行硬编码
- 使用 Microsoft.SQL 作为服务终结点但无法与网关 IP 地址通信的任何子网

如果您有 ：
- 重定向为连接策略
- 从 Azure 内部和使用服务标记连接到 SQL 数据库
- 使用支持的 SQL Server 支持的 JDBC 驱动程序版本建立的连接不会造成任何影响。 有关支持的 JDBC 版本，请参阅[下载 SQL Server 的 Microsoft JDBC 驱动程序](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)。

## <a name="what-to-do-you-do-if-youre-affected"></a>如果您受到影响，该怎么办

我们建议您允许 TCP 端口 1433 上区域中的所有[Azure 数据库网关 IP 地址](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses)的出站流量到 IP 地址，端口范围为 11000-11999。 此建议适用于从本地连接的客户端，以及通过服务终结点连接的客户端。 有关端口范围的详细信息，请参阅[连接策略](sql-database-connectivity-architecture.md#connection-policy)。

使用版本 4.0 以下的 Microsoft JDBC 驱动程序的应用程序建立的连接可能会失败证书验证。 Microsoft JDBC 的较低版本依赖于证书的主题字段中的通用名称 （CN）。 缓解是确保主机NameIn证书属性设置为 *.database.windows.net。 有关如何设置主机NameIn证书属性的详细信息，请参阅[使用 SSL 加密进行连接](/sql/connect/jdbc/connecting-with-ssl-encryption)。

如果上述缓解措施不起作用，请使用以下 URL 提交 SQL 数据库支持请求：https://aka.ms/getazuresupport

## <a name="next-steps"></a>后续步骤

- 了解有关 Azure [SQL 连接体系结构的更多内容](sql-database-connectivity-architecture.md)
