---
title: 支持在 Azure 中可用性区域的区域
description: 若要在 Azure 中创建具有高可用性和弹性的应用程序，可用性区域提供了可用于运行资源的物理独立位置。
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 09/18/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: b2143e4a9e4c24291ca2840f51e1b63c12017b04
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91264230"
---
# <a name="regions-that-support-availability-zones-in-azure"></a>支持在 Azure 中可用性区域的区域

可用性区域是一个高可用性产品/服务，在数据中心发生故障时可以保护应用程序和数据。 有关可用性区域的详细信息，请参阅 [Azure 中的区域和可用性区域](az-overview.md)。

本部分列出了支持可用性区域的 Azure 服务和区域。

每个区域中可用的服务以及适用于可用性的即将推出的服务可在 [按区域提供的产品](https://azure.microsoft.com/global-infrastructure/services/)中找到。

## <a name="americas"></a>美洲

| 服务 | 美国中部 | 美国东部 | 美国东部 2 | 美国西部 2 | 加拿大中部
| --- | :---: | :---: | :---: | :---: | :---: |
| **计算** |  |  |  |  |
| Linux 虚拟机             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Windows 虚拟机           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| 虚拟机规模集         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure App Service 环境 ILB | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Kubernetes 服务           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **存储** |  |  |  |  |
| 托管磁盘                      | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| 区域冗余存储             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **联网** |  |  |  |  |
| 标准 IP 地址                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| 标准负载均衡器             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| VPN 网关                        | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| ExpressRoute 网关               | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| 应用程序网关 (V2)            | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure 防火墙                     | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **数据库** |  |  |  |  |
| Azure 数据资源管理器                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| SQL 数据库                       | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| 用于 Redis 的 Azure 缓存              | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Cosmos DB                    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **分析** |  |  |  |  |
| 事件中心                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **集成** |  |  |  |  |
| 服务总线（仅限高级层）    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| 事件网格                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **标识** |  |  |  |  |
| Azure AD 域服务           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |   |

## <a name="europe"></a>欧洲

| 服务 | 法国中部 | 北欧 | 英国南部 | 西欧 |
| --- | :---: | :---: | :---: | :---: |
| **计算** |  |  |  |  |
| Linux 虚拟机             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Windows 虚拟机           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| 虚拟机规模集         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure App Service 环境 ILB | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Kubernetes 服务           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **存储** |  |  |  |  |
| 托管磁盘                      | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| 区域冗余存储             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **联网** |  |  |  |  |
| 标准 IP 地址                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| 标准负载均衡器             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| VPN 网关                        | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| ExpressRoute 网关               | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| 应用程序网关 (V2)            | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure 防火墙                     | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **数据库** |  |  |  |  |
| Azure 数据资源管理器                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| SQL 数据库                       | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| 用于 Redis 的 Azure 缓存              | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Cosmos DB                    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **分析** |  |  |  |  |
| 事件中心                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **集成**  |  |  |  |  |
| 服务总线（仅限高级层）    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| 事件网格                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **标识** |  |  |  |  |
| Azure AD 域服务           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="asia-pacific"></a>亚太区

| 服务 | Japan East | Southeast Asia | 澳大利亚东部 |
| --- | :---: | :---: | :---: |
| **计算** |  |  |  |
| Linux 虚拟机             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Windows 虚拟机           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| 虚拟机规模集         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure App Service 环境 ILB | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Kubernetes 服务           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **存储** |  |  |  |
| 托管磁盘                      | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| 区域冗余存储             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **联网** |  |  |  |
| 标准 IP 地址                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| 标准负载均衡器             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| VPN 网关                        | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| ExpressRoute 网关               | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| 应用程序网关 (V2)            | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure 防火墙                     | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **数据库** |  |  |  |
| Azure 数据资源管理器                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| SQL 数据库                       | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| 用于 Redis 的 Azure 缓存              | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Cosmos DB                    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **分析** |  |  |  |
| 事件中心                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **集成** |  |  |  |
| 服务总线（仅限高级层）    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| 事件网格                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **标识** |  |  |  |
| Azure AD 域服务           | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="other"></a>其他

Azure 还提供以下区域中的可用性区域支持：

- US Gov 弗吉尼亚州
- 南非北部
- 美国中南部

若要详细了解这三个区域的可用性区域支持，请与你的 Microsoft 销售或客户代表联系，或提出技术支持请求。

## <a name="next-steps"></a>后续步骤

- [Azure 中的区域和可用性区域](az-overview.md)
