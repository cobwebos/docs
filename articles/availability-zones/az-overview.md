---
title: Azure 中的区域和可用性区域
description: 了解 Azure 中的区域和可用性区域，以满足你的技术和法规要求。
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 08/27/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc
ms.openlocfilehash: 9a9cdef27276aa589a4aadd853185b0e3fb4be61
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91773733"
---
# <a name="regions-and-availability-zones-in-azure"></a>Azure 中的区域和可用性区域

Microsoft Azure 服务可在全球范围内实现云操作的最佳水平。 你可以根据技术和监管注意事项选择最适合你的需求区域：服务功能、数据常驻、合规性要求和延迟。

## <a name="terminology"></a>术语

为了更好地了解 Azure 中的区域和可用性区域，它有助于了解关键术语或概念。

| 术语或概念 | 说明 |
| --- | --- |
| region | 在延迟定义的外围中部署的一组数据中心，并通过专用的区域低延迟网络进行连接。 |
| geography | 世界上至少包含一个 Azure 区域的区域。 地理位置定义了一个保持数据驻留和符合性界限的离散市场。 地域允许具有特定数据驻留和符合性要求的客户保持他们的数据和应用程序相邻近。 地域具有容错能力，可通过其与专用的高容量网络基础结构的连接来经受完成区域故障。 |
| 可用性区域 | 区域中的唯一物理位置。 每个区域由一个或多个数据中心组成，这些数据中心配置了独立电源、冷却和网络。 |
| 推荐的区域 | 提供最广泛的服务功能的区域，旨在支持可用性区域现在或将来使用。 根据 **建议**在 Azure 门户中指定这些项。 |
| 备用 (其他) 区域 | 一种区域，它在 "数据驻留" 边界内扩展 Azure 占用量，其中还存在建议的区域。 备用区域有助于优化延迟，并为灾难恢复需要提供另一个区域。 它们不是为了支持可用性区域 (，不过 Azure 会定期评估这些区域，以确定它们是否应该成为推荐的区域) 。 它们在 Azure 门户中指定为 " **其他**"。 |
| 基础服务 | 一种核心 Azure 服务，该服务在区域公开上市时在所有区域提供。 |
| 主流服务 | 一项 Azure 服务，该服务在区域/服务的12个月内的所有推荐区域中提供，在备用区域中公开上市或要求驱动。 |
| 专用服务 | 一项 Azure 服务，在自定义/专用硬件支持的各区域之间实现了要求驱动的可用性。 |
| 区域服务 | 突破部署的 Azure 服务，可让客户指定要将服务部署到的区域。 有关完整列表，请参阅 [可用产品（按区域](https://azure.microsoft.com/global-infrastructure/services/?products=all)）。 |
| 非区域服务 | 一个 Azure 服务，它与特定的 Azure 区域没有任何依赖关系。 非区域服务部署到两个或多个区域，如果出现区域性故障，另一个区域中的服务实例将继续为客户提供服务。 有关完整列表，请参阅 [可用产品（按区域](https://azure.microsoft.com/global-infrastructure/services/?products=all)）。 |

## <a name="regions"></a>区域

区域是在延迟定义的外围中部署的一组数据中心，通过专用的区域低延迟网络进行连接。 Azure 可让你灵活地部署需要的应用程序，包括跨多个区域，以提供跨区域复原能力。 有关详细信息，请参阅 [复原支柱概述](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview)。

## <a name="availability-zones"></a>可用性区域

可用性区域是一种高可用性产品，可保护应用程序和数据免受数据中心故障的影响。 可用性区域是 Azure 区域中独特的物理位置。 每个区域由一个或多个数据中心组成，这些数据中心配置了独立电源、冷却和网络。 为确保能够进行复原，所有已启用的地区中都必须至少有三个单独的区域。 区域中可用性区域的物理隔离可以在发生数据中心故障的情况下保护应用程序和数据。 区域冗余服务可跨可用性区域复制应用程序和数据，以防范单点故障。 Azure 凭借可用性区域提供一流的 99.99% VM 运行时间 SLA。 完整 [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) 说明了 Azure 作为整体的保证可用性。

Azure 区域中的可用性区域是容错域和更新域的组合。 例如，如果在 Azure 区域的三个区域中创建三个或更多 VM，则 VM 将有效分布在三个容错域和三个更新域中。 Azure 平台可识别更新域中的此分发，以确保不会计划同时更新不同区域中的 Vm。

通过将计算、存储、网络和数据资源共置在一个区域并将其复制到其他区域，在应用程序体系结构中内置高可用性。 支持可用性区域的 Azure 服务划分为两类：

- **区域性服务** –将资源固定到特定区域的位置 (例如，虚拟机、托管磁盘、标准 IP 地址) 或
- **区域冗余服务** –当 Azure 平台跨区域自动复制时 (例如，区域冗余存储、SQL Database) 。

若要在 Azure 上实现全面的业务连续性，可结合使用可用性区域和 Azure 区域对来构建应用程序体系结构。 可以使用 Azure 区域中的可用性区域以同步方式复制应用程序和数据以实现高可用性，并以异步方式跨 Azure 区域复制以实现灾难恢复保护。
 
![地区中发生故障的一个区域的概念视图](./media/az-overview/az-graphic-two.png)

> [!IMPORTANT]
>  () 上图中的数字1、2和3的可用性区域标识符在逻辑上分别映射到每个订阅的实际物理区域。 这意味着，给定订阅中的可用性区域1可能引用不同订阅中不同于可用性区域1的物理区域。 因此，建议不要在虚拟机放置的不同订阅之间依赖于可用性区域 Id。

## <a name="region-and-service-categories"></a>区域和服务类别

Azure 上跨区域提供 Azure 服务可用性的最佳方法是通过表达在建议区域和备用区域中提供的服务来对其进行说明。

- **推荐的区域** -一种提供范围最广的服务功能并设计为支持可用性区域现在或将来的服务的区域。 根据 **建议**在 Azure 门户中指定这些项。
- **备用 (其他) 区域** -一个区域，该区域在一个推荐的区域也存在的数据派驻边界内扩展 Azure 占用空间。 备用区域有助于优化延迟，并为灾难恢复需要提供另一个区域。 它们不是为了支持可用性区域 (，不过 Azure 会定期评估这些区域，以确定它们是否应该成为推荐的区域) 。 它们在 Azure 门户中指定为 " **其他**"。

### <a name="comparing-region-types"></a>比较区域类型

Azure 服务分为三个类别：基本、主流和专用服务。 将服务部署到任何给定区域的 Azure 一般策略主要由区域类型、服务类别和客户需求驱动：

- **基础** –当区域公开上市时，或在新的基础服务公开上市后的12个月内提供。
- **主流** –在区域/服务正式发布后12个月内的所有推荐区域提供;备用区域中的按需驱动 (许多已部署到) 的一大部分备用区域中。
- **专门** 面向行业的服务产品，通常由自定义/专用硬件集中或支持。 跨区域的需求驱动可用性 (许多都已部署到) 的建议区域的一小部分。

若要查看给定区域中部署了哪些服务，以及区域中服务的预览或公开服务的未来发展路线图，请参阅 [按区域提供的产品](https://azure.microsoft.com/global-infrastructure/services/?products=all)。

如果某个服务产品在特定区域中不可用，你可以通过联系 Microsoft 销售代表来分享你的兴趣。

| 区域类型 | 非区域 | 基础 | 主要支持 | 专用 | 可用性区域 | 数据驻留 |
| --- | --- | --- | --- | --- | --- | --- |
| 建议 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | 需求驱动 | :heavy_check_mark: | :heavy_check_mark: |
| 备用 | :heavy_check_mark: | :heavy_check_mark: | 需求驱动 | 需求驱动 | 空值 | :heavy_check_mark: |

### <a name="services-by-category"></a>按类别分类的服务

如前所述，Azure 将服务分为三个类别：基础、主流和专用化。 服务类别在正式发布时分配。 通常，服务会将其生命周期作为专用服务启动，并按需求和利用率增加，从而提升为主流或基础。 下表列出了 "基本"、"主流" 或 "专用" 服务的类别。 应注意以下有关表的信息：

- 某些服务不是区域。 有关非区域性服务的信息和列表，请参阅 [可用产品（按区域](https://azure.microsoft.com/global-infrastructure/services/)）。
- 不会列出较旧的代虚拟机。 有关详细信息，请参阅 [之前代虚拟机大小的](../virtual-machines/sizes-previous-gen.md)文档。

> [!div class="mx-tableFixed"]
> | 基础 | 主要支持 | 专用 |
> | --- | --- | --- |
> | 帐户存储 | API 管理 | 适用于 FHIR 的 Azure API |
> | 应用程序网关 | 应用程序配置 | Azure Analysis Services |
> | Azure 备份 | 应用服务 | Azure 区块链服务 |
> | Azure Cosmos DB | 自动化 | Azure 蓝图 |
> | Azure Data Lake Storage Gen2 | Azure Active Directory 域服务 | Azure Database for MariaDB |
> | Azure ExpressRoute | Azure Bastion | Azure 专用 HSM |
> | Azure SQL 数据库 | 用于 Redis 的 Azure 缓存 | Azure Dev Spaces |
> | 云服务 | Azure 认知搜索 | Azure 数字孪生 |
> | 云服务： Av2 系列 | Azure 数据资源管理器 | Azure 实验室服务 |
> | 云服务： Dv2 系列 | Azure Data Share | Azure NetApp 文件 |
> | 云服务： Dv3 系列 | Azure Database for MySQL | Azure 量程 |
> | 云服务： Ev3 系列 | Azure Database for PostgreSQL | Azure 春季云服务 |
> | 云服务：实例级别 Ip | Azure 数据库迁移服务 | Azure 时序见解 |
> | 云服务：保留 IP | Azure Databricks | Azure VMware Solution by CloudSimple |
> | 磁盘存储 | Azure DDoS 防护 | 云服务： G 系列 |
> | 事件中心 | Azure 开发测试实验室 | 云服务： H 系列 |
> | Key Vault | Azure 防火墙 | 认知服务：自定义视觉 |
> | 负载均衡器 | Azure 防火墙管理器 | 认知服务：演讲者识别 |
> | 服务总线 | Azure Functions | Data Box Heavy |
> | Service Fabric | Azure HPC 缓存 | 数据目录 |
> | 虚拟机规模集 | Azure IoT 中心 | 数据工厂：数据工厂 V1 |
> | 虚拟机 | Azure Kubernetes 服务 (AKS) | Data Lake Analytics |
> | 虚拟机： Av2 系列 | Azure 机器学习 | Azure 机器学习工作室（经典）|
> | 虚拟机： Bs.1770 系列 | Azure 专用链接 | Microsoft 基因组学 |
> | 虚拟机： DSv2 系列 | Azure Red Hat OpenShift | 远程渲染 |
> | 虚拟机： DSv3 系列 | Azure SignalR 服务 | 空间定位点 |
> | 虚拟机： Dv2 系列 | Azure Site Recovery | StorSimple |
> | 虚拟机： Dv3 系列 | Azure Stack Hub | 视频索引器 |
> | 虚拟机： ESv3 系列 | Azure 流分析 | 虚拟机： DASv4 系列 |
> | 虚拟机： Ev3 系列 | Azure Synapse Analytics | 虚拟机： DAv4 系列 |
> | 虚拟机： F 系列 | Batch | 虚拟机： DCsv2 系列 |
> | 虚拟机： FS 系列 | 云服务： M 系列 | 虚拟机： EASv4 系列 |
> | 虚拟机：实例级别 Ip | 认知服务 | 虚拟机： EAv4 系列 |
> | 虚拟机：保留 IP | 认知服务：计算机视觉 | 虚拟机： G 系列 |
> | 虚拟网络 | 认知服务：内容审查器 | 虚拟机： GS 系列 |
> | VPN 网关 | 认知服务：面部 | 虚拟机： HBv1 系列 |
> |  | 认知服务：窗体识别器 | 虚拟机： HBv2 系列 |
> |  | 认知服务：语言理解 | 虚拟机： HCv1 系列 |
> |  | 认知服务：QnA Maker | 虚拟机： H 系列 |
> |  | 认知服务：语音服务 | 虚拟机： LS 系列 |
> |  | 容器实例 | 虚拟机： LSv2 系列 |
> |  | 容器注册表 | 虚拟机： Mv2 系列 |
> |  | 数据工厂 | 虚拟机： NC 系列 |
> |  | 事件网格 | 虚拟机： NCv2 系列 |
> |  | HDInsight | 虚拟机： NCv3 系列 |
> |  | 逻辑应用 | 虚拟机： NDs 系列 |
> |  | 媒体服务 | 虚拟机： NDv2 系列 |
> |  | 网络观察程序 | 虚拟机： NV 系列 |
> |  | 通知中心 | 虚拟机： NVv3 系列 |
> |  | Power BI Embedded | 虚拟机： NVv4 系列 |
> |  | 高级 Blob 存储 | 虚拟机： Azure SAP HANA 大型实例 |
> |  | 高级文件存储 | Visual Studio 应用中心 |
> |  | 存储：存档存储 |  |
> |  | 超级磁盘存储 |  |
> |  | 虚拟机： Ddsv4 系列 |  |
> |  | 虚拟机： Ddv4 系列 |  |
> |  | 虚拟机： Dsv4 系列 |  |
> |  | 虚拟机： Dv4 系列 |  |
> |  | 虚拟机： Edsv4 系列 |  |
> |  | 虚拟机： Edv4 系列 |  |
> |  | 虚拟机： Esv4 系列 |  |
> |  | 虚拟机： Ev4 系列 |  |
> |  | 虚拟机： Fsv2 系列 |  |
> |  | 虚拟机： M 系列 |  |
> |  | 虚拟 WAN |  |

###  <a name="services-resiliency"></a>服务复原

所有 Azure 管理服务都设计为可从区域级故障中复原。 在故障范围内，区域中的一个或多个可用性区域故障与整个区域发生故障相比具有更小的故障半径。 Azure 可以从区域内或其他 Azure 区域内的管理服务的区域级故障中恢复。 Azure 会在区域中一次执行关键维护，以防止任何影响在区域内的可用性区域部署的客户资源的故障。

### <a name="pricing-for-vms-in-availability-zones"></a>可用性区域中 Vm 的定价

在可用性区域中部署虚拟机不会产生额外的费用。 当两个或更多个 VM 部署在一个 Azure 区域中的两个或更多个可用性区域时，可获得 99.99% VM 运行时间 SLA。 会产生可用性区域间 VM 到 VM 的数据传输费用。 有关详细信息，请查看[带宽定价](https://azure.microsoft.com/pricing/details/bandwidth/)页。

### <a name="get-started-with-availability-zones"></a>可用性区域入门

- [创建虚拟机](../virtual-machines/windows/create-portal-availability-zone.md)
- [使用 PowerShell 添加托管磁盘](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [创建区域冗余的虚拟机规模集](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [使用具有区域冗余前端的标准负载均衡器跨区域对 VM 进行负载均衡](../load-balancer/load-balancer-standard-public-zone-redundant-cli.md)
- [使用具有区域性前端的标准负载均衡器在区域内对 VM 进行负载均衡](../load-balancer/load-balancer-standard-public-zonal-cli.md)
- [区域冗余存储](../storage/common/storage-redundancy-zrs.md)
- [SQL 数据库](../azure-sql/database/high-availability-sla.md#zone-redundant-configuration)
- [事件中心异地灾难恢复](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [服务总线异地灾难恢复](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [创建区域冗余的虚拟网关](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [为 Azure Cosmos DB 添加区域冗余区域](../cosmos-db/high-availability.md#availability-zone-support)
- [入门用于 Redis 的 Azure 缓存可用性区域](https://aka.ms/redis/az/getstarted)
- [创建 Azure Active Directory 域服务实例](../active-directory-domain-services/tutorial-create-instance.md)
- [使用可用性区域 (AKS) 群集创建 Azure Kubernetes 服务](../aks/availability-zones.md)

## <a name="next-steps"></a>后续步骤

- [支持在 Azure 中可用性区域的区域](az-region.md)
- [快速入门模板](https://aka.ms/azqs)
