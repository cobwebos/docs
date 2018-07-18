---
title: VPN 网关从经典部署模型迁移到 Resource Manager 部署模型 | Microsoft 文档
description: 此页简要介绍了 VPN 网关从经典部署模型到 Resource Manager 部署模型的迁移过程。
documentationcenter: na
services: vpn-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: caa8eb19-825a-4031-8b49-18fbf3ebc04e
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/02/2017
ms.author: amsriva
ms.openlocfilehash: 1164fc24355657af22b6befaad74685ebbc2b5cb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
ms.locfileid: "23127095"
---
# <a name="vpn-gateway-classic-to-resource-manager-migration"></a>VPN 网关从经典部署模型迁移到 Resource Manager 部署模型
VPN 网关现可从经典部署模型迁移到 Resource Manager 部署模型。 可以阅读有关 Azure Resource Manager [功能和优点](../azure-resource-manager/resource-group-overview.md)的更多内容。 在本文中，我们将详细介绍如何从经典部署模型迁移到更新的基于 Resource Manager 的部署模型。 

作为 VNet 迁移的一部分，VPN 网关将从经典部署模型迁移到 Resource Manager 部署模型。 一次仅限一个 VNet 完成此迁移。 就迁移工具或迁移前提条件而言，没有什么额外要求。 迁移步骤与现有 VNet 迁移步骤相同，并且已在 [IaaS 资源迁移页](../virtual-machines/windows/migration-classic-resource-manager-ps.md)进行了编档。 在迁移期间没有任何数据路径停机时间，因此，现有的工作负荷将继续运行，并且在迁移期间不会丢失本地连接。 在迁移过程中，与 VPN 网关关联的公用 IP 地址不会更改。 这就说明完成迁移后无需重新配置本地路由器。  

Resource Manager 中的模型与经典模型不同，它由虚拟网络网关、本地网络网关和连接资源组成。 这些表示 VPN 网关本身，即分别表示本地地址空间和两者之间连接的本地站点。 完成迁移后，网关在经典模型下不再可用，并且必须使用 Resource Manager 模型对虚拟网络网关、本地网络网关和连接对象执行所有管理操作。

## <a name="supported-scenarios"></a>支持的方案
从经典到 Resource Manager 的迁移涵盖了最常用的 VPN 连接方案。 支持的方案包括：

* 点到站点连接
* 站点到站点连接，其中 VPN 网关连接到本地位置
* 使用 VPN 网关的两个 Vnet 之间的 VNet 到 VNet 连接
* 多个 Vnet 连接到同一本地位置
* 多站点连接
* 已启用强制隧道的 VNet

不支持的方案包括︰  

* 当前不支持使用 ExpressRoute 网关和 VPN 网关的 VNet。
* VM 扩展连接到本地服务器的传输方案。 下面详细介绍了传输 VPN 连接限制。

> [!NOTE]
> Resource Manager 模型中的 CIDR 验证比经典模型中的验证更为严格。 迁移之前，请确保在开始迁移之前给定的经典地址范围符合有效的 CIDR 格式。 可以使用任何常用的 CIDR 验证程序验证 CIDR。 一旦迁移了 CIDR 范围无效的 VNet 或本地站点，会导致失败。
> 
> 

## <a name="vnet-to-vnet-connectivity-migration"></a>VNet 到 VNet 连接迁移
经典模型中的 VNet 到 VNet 连接是通过创建已连接 VNet 的本地站点表示来实现的。 客户需要创建表示需连接在一起的两个 Vnet 的本地站点。 然后，使用 IPsec 隧道将这些连接到相应的 Vnet，建立两个 Vnet 之间的连接。 由于必须在相应的本地站点表示中维护一个 VNet 中的任何地址范围更改，所以经典模型会有可管理性方面的问题。 在 Resource Manager 模型中，已不再需要此解决方法。 可以在连接资源中使用“Vnet 到 Vnet”连接类型直接实现两个 Vnet 之间的连接。 

![VNet 到 VNet 迁移的屏幕截图。](./media/vpn-gateway-migration/migration1.png)

在 VNet 迁移期间，我们检测到连接到当前 VNet 的 VPN 网关的实体是另一个 VNet，但可以确保这两个 Vnet 迁移完成后，将无法再看到表示另一个 VNet 的两个本地站点。 由两个 VPN 网关、两个本地站点及其之间的两个连接组成的经典模型，将转换为使用两个 VPN 网关和两个 Vnet 到 Vnet 类型的连接的 Resource Manager 模型。

## <a name="transit-vpn-connectivity"></a>传输 VPN 连接
可以在拓扑中配置 VPN 网关，以便通过连接到另一个直接连接到本地的 VNet 来实现 VNet 的本地连接。 这就是传输 VNet 连接，其中第一个 VNet 中的实例通过在直接连接到本地的已连接 VNet 中传输到 VPN 网关来连接到本地资源。 为了在经典部署模型中实现此配置，将需要创建拥有聚合前缀的本地站点，其中前缀表示两个已连接的 VNet 和本地地址空间。 然后，此表述性本地站点连接到 VNet，以实现传输连接。 此经典模型还面临着许多类似的可管理性方面的挑战，因为还必须在表示 VNet 和本地聚合的本地站点上维护本地地址范围的任何更改。 在支持 Resource Manager 的网关中引入 BGP 支持简化了可管理性，因为已连接网关可以从本地学习路由，并且无需手动修改前缀。

![传输路由方案的屏幕截图。](./media/vpn-gateway-migration/migration2.png)

由于我们无需本地站点即可转换 VNet 到 VNet 的连接，所以此传输方案将丢失间接连接到本地的 VNet 的本地连接。 完成迁移后，可以按照以下两种方式缓解连接丢失： 

* 对连接在一起和连接到本地的 VPN 网关启用 BGP。 由于路由是在 VNet 网关之间学习和播发的，所以启用 BGP 可还原连接，而且无需更改任何其他配置。 请注意，BGP 选项仅适用于标准和更高版本的 SKU。
* 在受影响的 VNet 和表示本地位置的本地网络网关之间建立显式连接。 这还需要更改本地路由器配置来创建和配置 IPsec 隧道。

## <a name="next-steps"></a>后续步骤
在了解过 VPN 网关迁移支持后，请转到[在支持的平台上将 IaaS 资源从经典模型迁移到 Resource Manager 模型](../virtual-machines/windows/migration-classic-resource-manager-ps.md)开始学习。

