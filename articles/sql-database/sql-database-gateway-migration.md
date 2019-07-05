---
title: 网关迁移请注意，Azure SQL 数据库从第 2 代 Gen3 |Microsoft Docs
description: 文章提供了有关 Azure SQL 数据库网关 IP 地址迁移的用户的通知
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
manager: craigg
ms.date: 07/01/2019
ms.openlocfilehash: 5894579c62c524394c7fea044b96885f7c8e8204
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538375"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Azure SQL 数据库流量迁移到更高版本网关

随着提高 Azure 基础结构，Microsoft 会定期刷新硬件，以确保我们为客户提供最佳客户体验。 在未来几个月，我们计划添加较新的硬件代上生成网关并解除授权基于较旧的硬件，在某些区域中的网关。  

通过电子邮件和对每个区域中的可用网关的任何更改之前在 Azure 门户中，客户将收到通知。 最新信息将会保留在[Azure SQL 数据库网关 IP 地址](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses)表。

## <a name="impact-of-this-change"></a>此更改造成的影响

网关解除授权的第一轮为 2019 年 9 月 1 日计划在以下区域中：

- 美国西部
- 西欧
- East US
- 美国中部
- 东南亚
- 美国中南部
- 北欧
- 美国中北部
- 日本西部
- 日本东部
- 美国东部 2
- 东亚

停止使用 IP 地址将停止接受流量和任何新的连接尝试将路由到其中一个区域中网关。

其中不会看到此更改造成的影响：

- 客户使用重定向，正如其连接策略不会看到任何影响。
- 连接到 SQL 数据库从内部 Azure 并使用服务标记不会受影响。
- 使用适用于 SQL Server 的受支持的版本的 JDBC 驱动程序进行的连接将不会影响。 支持 JDBC 版本，请参阅[下载 Microsoft JDBC Driver for SQL Server](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)。

## <a name="what-to-do-you-do-if-youre-affected"></a>如果正在受影响要执行的操作所执行的操作

我们建议为所有允许的 IP 地址的出站流量[Azure SQL 数据库网关 IP 地址](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses)在区域中上 TCP 端口 1433，和端口范围 11000-11999 防火墙设备。 端口范围的详细信息，请参阅[连接策略](sql-database-connectivity-architecture.md#connection-policy)。

从低于版本 4.0 中使用 Microsoft JDBC 驱动程序的应用程序进行的连接可能会失败证书验证。 较低版本的 Microsoft JDBC 依赖于在公用名 (CN) 的证书的主题字段中。 缓解措施是为了确保 hostNameInCertificate 属性设置为 *。 database.windows.net。 有关如何设置 hostNameInCertificate 属性的详细信息，请参阅[使用 SSL 加密进行连接](/sql/connect/jdbc/connecting-with-ssl-encryption)。

如果上面的缓解措施不起作用，使用以下 URL 的 SQL 数据库中的文件的支持请求： https://aka.ms/getazuresupport

## <a name="next-steps"></a>后续步骤

- 查找更多有关[Azure SQL 连接体系结构](sql-database-connectivity-architecture.md)