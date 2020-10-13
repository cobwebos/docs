---
title: 支持在 Azure 中可用性区域的区域
description: 若要在 Azure 中创建具有高可用性和弹性的应用程序，可用性区域提供了可用于运行资源的物理独立位置。
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 10/07/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: 888365e04566f18e5a73454fea4c96c321f5ce52
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91858381"
---
# <a name="regions-that-support-availability-zones-in-azure"></a>支持在 Azure 中可用性区域的区域

可用性区域是一个高可用性产品/服务，在数据中心发生故障时可以保护应用程序和数据。 有关可用性区域的详细信息，请参阅 [Azure 中的区域和可用性区域](az-overview.md)。

本部分列出了支持可用性区域的 Azure 服务和区域。

每个区域中可用的服务以及适用于可用性的即将推出的服务可在 [按区域提供的产品](https://azure.microsoft.com/global-infrastructure/services/)中找到。


## <a name="americas"></a>美洲

| **产品** | 美国中部 | **美国东部** | **美国东部 2** | **美国西部 2** | **加拿大中部** |
|--|--|--|--|--|--|
| **计算** |  |  |  |  |  |
| [应用服务环境 (ILB) ](../app-service/environment/zone-redundancy.md#how-to-deploy-an-app-service-environment-in-an-availability-zone) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Kubernetes 服务 (AKS)](https://docs.microsoft.com/azure/aks/availability-zones) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cross-availability-zones#:~:text=An%20Availability%20Zone%20is%20a%20unique%20physical%20location,zones.%20This%20will%20ensure%20high-availability%20of%20your%20applications.) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [虚拟机规模集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones#:~:text=To%20protect%20your%20virtual%20machine%20scale%20sets%20from,their%20own%20independent%20power%20source%2C%20network%2C%20and%20cooling.) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [虚拟机](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **存储** |  |  |  |  |  |
| [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [高级文件存储](https://docs.microsoft.com/azure/storage/files/storage-files-planning) |  | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Blob 存储](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [托管磁盘](https://azure.microsoft.com/en-gb/updates/azure-managed-snapshots-images-ga/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **网络** |  |  |  |  |  |
| [应用程序网关 V2](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Express Route](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure 防火墙](https://docs.microsoft.com/azure/firewall/deploy-availability-zone-powershell#:~:text=For%20more%20information%20about%20Azure%20Firewall%20Availability%20Zones%2C,This%20creates%20a%20zone-redundant%20IP%20address%20by%20default.) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [标准 IP 地址](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [负载均衡器](../load-balancer/load-balancer-standard-availability-zones.md#concepts) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [虚拟网络](https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [虚拟 WAN](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-faq) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [VPN 网关](https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **数据库** |  |  |  |  |  |
| [用于 Redis 的 Azure 缓存](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-overview) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/high-availability#availability-zone-support) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure 数据资源管理器](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure SQL 数据库](https://docs.microsoft.com/azure/azure-sql/database/high-availability-sla) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **分析** |  |  |  |  |  |
| [事件中心](https://docs.microsoft.com/azure/event-hubs/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **集成** |  |  |  |  |  |
| [事件网格](https://docs.microsoft.com/azure/event-grid/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [服务总线](https://docs.microsoft.com/azure/service-fabric/service-fabric-cross-availability-zones#:~:text=An%20Availability%20Zone%20is%20a%20unique%20physical%20location,zones.%20This%20will%20ensure%20high-availability%20of%20your%20applications.) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **安全性** |  |  |  |  |  |
| [Azure Active Directory 域服务](https://docs.microsoft.com/azure/active-directory-domain-services/overview) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="europe"></a>欧洲

| **产品** | **法国中部** | **北欧** | **英国南部** | “西欧” |
|--|--|--|--|--|
| **计算** |  |  |  |  |
| [应用服务环境 (ILB) ](../app-service/environment/zone-redundancy.md#how-to-deploy-an-app-service-environment-in-an-availability-zone) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Kubernetes 服务 (AKS)](https://docs.microsoft.com/azure/aks/availability-zones) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cross-availability-zones#:~:text=An%20Availability%20Zone%20is%20a%20unique%20physical%20location,zones.%20This%20will%20ensure%20high-availability%20of%20your%20applications.) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [虚拟机规模集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones#:~:text=To%20protect%20your%20virtual%20machine%20scale%20sets%20from,their%20own%20independent%20power%20source%2C%20network%2C%20and%20cooling.) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [虚拟机](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **存储** |  |  |  |  |
| [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [高级文件存储](https://docs.microsoft.com/azure/storage/files/storage-files-planning) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Blob 存储](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [托管磁盘](https://azure.microsoft.com/en-gb/updates/azure-managed-snapshots-images-ga/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **网络** |  |  |  |  |
| [应用程序网关 V2](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Express Route](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure 防火墙](https://docs.microsoft.com/azure/firewall/deploy-availability-zone-powershell#:~:text=For%20more%20information%20about%20Azure%20Firewall%20Availability%20Zones%2C,This%20creates%20a%20zone-redundant%20IP%20address%20by%20default.) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [标准 IP 地址](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [负载均衡器](../load-balancer/load-balancer-standard-availability-zones.md#concepts) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [虚拟网络](https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [虚拟 WAN](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-faq) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [VPN 网关](https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **数据库** |  |  |  |  |
| [用于 Redis 的 Azure 缓存](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-overview) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/high-availability#availability-zone-support) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure 数据资源管理器](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure SQL 数据库](https://docs.microsoft.com/azure/azure-sql/database/high-availability-sla) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **分析** |  |  |  |  |
| [事件中心](https://docs.microsoft.com/azure/event-hubs/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **集成** |  |  |  |  |
| [事件网格](https://docs.microsoft.com/azure/event-grid/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [服务总线](https://docs.microsoft.com/azure/service-fabric/service-fabric-cross-availability-zones#:~:text=An%20Availability%20Zone%20is%20a%20unique%20physical%20location,zones.%20This%20will%20ensure%20high-availability%20of%20your%20applications.) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **安全性** |  |  |  |  |
| [Azure Active Directory 域服务](https://docs.microsoft.com/azure/active-directory-domain-services/overview) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="asia-pacific"></a>亚太区



| **产品** | **Japan East** | **Southeast Asia** | **澳大利亚东部** |
|--|--|--|--|
| **计算** |  |  |  |
| [应用服务环境 (ILB) ](../app-service/environment/zone-redundancy.md#how-to-deploy-an-app-service-environment-in-an-availability-zone) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Kubernetes 服务 (AKS)](https://docs.microsoft.com/azure/aks/availability-zones) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cross-availability-zones#:~:text=An%20Availability%20Zone%20is%20a%20unique%20physical%20location,zones.%20This%20will%20ensure%20high-availability%20of%20your%20applications.) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [虚拟机规模集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones#:~:text=To%20protect%20your%20virtual%20machine%20scale%20sets%20from,their%20own%20independent%20power%20source%2C%20network%2C%20and%20cooling.) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [虚拟机](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **存储** |  |  |  |
| [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [高级文件存储](https://docs.microsoft.com/azure/storage/files/storage-files-planning) |  | :heavy_check_mark: | :heavy_check_mark: |
| [Blob 存储](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [托管磁盘](https://azure.microsoft.com/en-gb/updates/azure-managed-snapshots-images-ga/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **网络** |  |  |  |
| [应用程序网关 V2](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Express Route](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure 防火墙](https://docs.microsoft.com/azure/firewall/deploy-availability-zone-powershell#:~:text=For%20more%20information%20about%20Azure%20Firewall%20Availability%20Zones%2C,This%20creates%20a%20zone-redundant%20IP%20address%20by%20default.) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [标准 IP 地址](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [负载均衡器](../load-balancer/load-balancer-standard-availability-zones.md#concepts) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [虚拟网络](https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [虚拟 WAN](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-faq) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [VPN 网关](https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **数据库** |  |  |  |
| [用于 Redis 的 Azure 缓存](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-overview) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/high-availability#availability-zone-support) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure 数据资源管理器](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure SQL 数据库](https://docs.microsoft.com/azure/azure-sql/database/high-availability-sla) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **分析** |  |  |  |
| [事件中心](https://docs.microsoft.com/azure/event-hubs/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **集成** |  |  |  |
| [事件网格](https://docs.microsoft.com/azure/event-grid/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [服务总线](https://docs.microsoft.com/azure/service-fabric/service-fabric-cross-availability-zones#:~:text=An%20Availability%20Zone%20is%20a%20unique%20physical%20location,zones.%20This%20will%20ensure%20high-availability%20of%20your%20applications.) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **安全性** |  |  |  |
| [Azure Active Directory 域服务](https://docs.microsoft.com/azure/active-directory-domain-services/overview) | :heavy_check_mark: | :heavy_check_mark: |  |





## <a name="other"></a>其他

Azure 还提供以下区域中的可用性区域支持：• US Gov 弗吉尼亚州•南部非洲北部•美国中南部

若要详细了解这三个区域的可用性区域支持，请联系你的 Microsoft 销售或客户代表。


## <a name="next-steps"></a>后续步骤

- [Azure 中的区域和可用性区域](az-overview.md)
