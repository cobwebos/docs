---
title: Azure SQL 数据库连接体系结构
description: 本文档介绍了用于从 Azure 内部或 Azure 外部进行数据库连接的 Azure SQL 数据库连接体系结构。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: fasttrack-edit, sqldbrb=1
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 06/26/2020
ms.openlocfilehash: dc96cc704533c2b4fb5dc25b41a2440c66849b53
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87289080"
---
# <a name="azure-sql-database-and-azure-synapse-analytics-connectivity-architecture"></a>Azure SQL 数据库和 Azure Synapse Analytics 连接体系结构
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

本文介绍了将网络流量定向到 Azure SQL 数据库或 Azure Synapse Analytics 的服务器的各种组件的体系结构。 它还介绍了不同的连接策略，以及这些策略如何影响从 Azure 内部连接的客户端以及从 Azure 外部连接的客户端。

> [!IMPORTANT]
> 本文不适用于 **Azure SQL 托管实例**。 请参阅 [托管实例的连接体系结构](../managed-instance/connectivity-architecture-overview.md)。

## <a name="connectivity-architecture"></a>连接体系结构

下图提供连接体系结构的综合概述。

![体系结构概述](./media/connectivity-architecture/connectivity-overview.png)

以下步骤介绍如何建立与 Azure SQL 数据库的连接：

- 客户端连接到网关，后者使用公共 IP 地址并侦听端口 1433。
- 该网关根据有效的连接策略将流量重定向或代理到适当的数据库群集。
- 在数据库群集中，流量转发到相应的数据库。

## <a name="connection-policy"></a>连接策略

SQL 数据库和 Azure Synapse 中的服务器支持以下三个服务器连接策略设置选项：

- **重定向（建议）：** 客户端直接与托管数据库的节点建立连接，从而降低延迟并改进吞吐量。 若要通过连接来使用此模式，客户端需要：
  - 在范围为 11000 到 11999 的端口上允许从客户端到区域中所有 Azure SQL IP 地址的出站通信。 使用 SQL 服务标记，使其更易于管理。  
  - 在端口 1433 上允许从客户端到 Azure SQL 数据库网关 IP 地址的出站通信。

- **代理：** 在此模式下，所有连接都通过 Azure SQL 数据库网关来代理，导致延迟增大和吞吐量降低。 若要通过连接来使用此模式，客户端需满足以下条件：在端口 1433 上允许从客户端到 Azure SQL 数据库网关 IP 地址的出站通信。

- 默认值：除非显式将连接策略更改为 `Proxy` 或 `Redirect`，否则，在创建后，此连接策略将在所有服务器上生效。 对于所有源自 Azure 内部的客户端连接（例如，源自 Azure 虚拟机的连接），默认策略为 `Redirect`；对于所有源自外部的客户端连接（例如，源自本地工作站的连接），默认策略为 `Proxy`。

我们强烈建议使用 `Redirect` 连接策略而不要使用 `Proxy` 连接策略，以最大程度地降低延迟和提高吞吐量。 但是，你需要满足上述允许网络流量的附加要求。 如果客户端为 Azure 虚拟机，则可将网络安全组 (NSG) 与[服务标记](../../virtual-network/security-overview.md#service-tags)配合使用来实现它。 如果客户端从本地工作站进行连接，则可能需要联系网络管理员，让其允许网络流量通过公司防火墙。

## <a name="connectivity-from-within-azure"></a>从 Azure 内连接

如果从 Azure 内部连接，则连接默认具有 `Redirect` 连接策略。 `Redirect` 策略是指建立到 Azure SQL 数据库的 TCP 会话连接后，会将 Azure SQL 数据库网关的目标虚拟 IP 更改为群集的目标虚拟 IP，从而将客户端会话重定向到适当的数据库群集。 此后，所有后续数据包绕过 Azure SQL 数据库网关，直接传输到群集。 下图演示了此流量流。

![体系结构概述](./media/connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>从 Azure 外连接

如果从 Azure 外部连接，则连接默认具有 `Proxy` 连接策略。 `Proxy` 策略是指通过 Azure SQL 数据库网关建立 TCP 会话，并且所有后续数据包通过网关传输。 下图演示了此流量流。

![体系结构概述](./media/connectivity-architecture/connectivity-onprem.png)

> [!IMPORTANT]
> 另请打开 TCP 端口 1434 和 14000-14999，以便[使用 DAC 进行连接](https://docs.microsoft.com/sql/database-engine/configure-windows/diagnostic-connection-for-database-administrators?view=sql-server-2017#connecting-with-dac)

## <a name="gateway-ip-addresses"></a>网关 IP 地址

下表按区域列出了网关的 IP 地址。 若要连接到 SQL 数据库或 Azure Synapse，需要允许前往/来自该区域的所有网关的网络流量。

以下文章介绍了如何将流量迁移到特定区域中的新网关： [AZURE SQL 数据库流量迁移到更新的网关](gateway-migration.md)

| 区域名称          | 网关 IP 地址 |
| --- | --- |
| 澳大利亚中部    | 20.36.105.0 |
| 澳大利亚 Central2   | 20.36.113.0 |
| 澳大利亚东部       | 13.75.149.87, 40.79.161.1, 13.70.112.9 |
| 澳大利亚东南部 | 191.239.192.109, 13.73.109.251 |
| Brazil South         | 104.41.11.5, 191.233.200.14 |
| 加拿大中部       | 40.85.224.249, 52.246.152.0, 20.38.144.1 |
| 加拿大东部          | 40.86.226.166      |
| 美国中部           | 13.67.215.62, 52.182.137.15, 23.99.160.139, 104.208.16.96, 104.208.21.1 |
| 中国东部           | 139.219.130.35     |
| 中国东部 2         | 40.73.82.1         |
| 中国北部          | 139.219.15.17      |
| 中国北部 2        | 40.73.50.0         |
| 东亚            | 191.234.2.139, 52.175.33.150, 13.75.32.4 |
| East US              | 40.121.158.30, 40.79.153.12, 191.238.6.43, 40.78.225.32 |
| 美国东部 2            | 40.79.84.180, 52.177.185.181, 52.167.104.0, 191.239.224.107, 104.208.150.3 |
| 法国中部       | 40.79.137.0, 40.79.129.1 |
| 德国中部      | 51.4.144.100       |
| 德国东北部   | 51.5.144.179       |
| 印度中部        | 104.211.96.159     |
| 印度南部          | 104.211.224.146    |
| 印度西部           | 104.211.160.80     |
| Japan East           | 13.78.61.196, 40.79.184.8, 13.78.106.224, 191.237.240.43, 40.79.192.5 |
| 日本西部           | 104.214.148.156, 40.74.100.192, 191.238.68.11, 40.74.97.10 |
| 韩国中部        | 52.231.32.42       |
| 韩国南部          | 52.231.200.86      |
| 美国中北部     | 23.96.178.199, 23.98.55.75, 52.162.104.33 |
| 北欧         | 40.113.93.91, 191.235.193.75, 52.138.224.1, 13.74.104.113 |
| 挪威东部          | 51.120.96.0        |
| 挪威西部          | 51.120.216.0       |
| 南非北部   | 102.133.152.0      |
| 南非西部    | 102.133.24.0       |
| 美国中南部     | 13.66.62.124, 23.98.162.75, 104.214.16.32, 20.45.121.1, 20.49.88.1   |
| 东南亚      | 104.43.15.0, 23.100.117.95, 40.78.232.3   |
| 瑞士北部    | 51.107.56.0, 51.107.57.0 |
| 瑞士西部     | 51.107.152.0, 51.107.153.0 |
| 阿联酋中部          | 20.37.72.64        |
| 阿拉伯联合酋长国北部            | 65.52.248.0        |
| 英国南部             | 51.140.184.11      |
| 英国西部              | 51.141.8.11        |
| 美国中西部      | 13.78.145.25       |
| 西欧          | 40.68.37.158, 191.237.232.75, 104.40.168.105, 52.236.184.163  |
| 美国西部              | 104.42.238.205, 23.99.34.75, 13.86.216.196   |
| 美国西部 2            | 13.66.226.202, 40.78.240.8, 40.78.248.10  |
|                      |                    |

## <a name="next-steps"></a>后续步骤

- 有关如何更改服务器的 Azure SQL 数据库连接策略的信息，请参阅 [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy)。
- 若要了解使用 ADO.NET 4.5 或更高版本的客户端的 Azure SQL 数据库连接行为，请参阅[用于 ADO.NET 4.5 的非 1433 端口](adonet-v12-develop-direct-route-ports.md)。
- 若要了解常规应用程序开发的概述信息，请参阅[SQL 数据库应用程序开发概述](develop-overview.md)。
