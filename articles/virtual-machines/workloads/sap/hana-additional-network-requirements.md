---
title: Azure 上的 SAP HANA（大型实例）的其他网络要求 | Microsoft Docs
description: Azure 上的 SAP HANA（大型实例）的其他网络要求。
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8ee89553846309bd96f3cad3648e337488cf07d3
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2018
ms.locfileid: "44167574"
---
# <a name="additional-network-requirements"></a>其他网络要求

有时候，HANA 大型实例部署可能有其他网络要求。 本文介绍了以下网络要求：
- [添加更多 IP 地址或子网](#adding-more-ip-addresses-or-subnets)
- [添加虚拟网络](#adding-vnets)
- [提高 ExpressRoute 线路带宽](#increasing-expressroute-circuit-bandwidth)
- [添加其他 ExpressRoute 线路](#adding-an-additional-expressroute-circuit)
- [删除子网](#deleting-a-subnet)
- [删除虚拟网络](#deleting-a-vnet)
- [删除 ExpressRoute 线路](#deleting-an-expressroute-circuit)


## <a name="adding-more-ip-addresses-or-subnets"></a>添加更多 IP 地址或子网

可以使用 Azure 门户、PowerShell 或 CLI 添加多个 IP 地址或子网。

在这种情况下，建议将新 IP 地址范围作为新范围添加到 VNet 地址空间，而不是生成新的聚合范围。 在任一情况下，都需要将此项更改提交给 Microsoft，以便在客户端中将该新 IP 地址范围连接到 HANA 大型实例单元。 可以发起 Azure 支持请求来添加新的 VNet 地址空间。 收到确认后，请执行后续步骤。

若要通过 Azure 门户预览创建其他子网，请参阅[使用 Azure 门户预览版创建虚拟网络](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)一文；若要使用 PowerShell，请参阅[使用 PowerShell 创建虚拟网络](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)。

## <a name="adding-vnets"></a>添加 VNet

首次连接一个或多个 Azure VNet 后，可能需要添加其他用于访问 Azure 上的 SAP HANA（大型实例）的 VNet。 首先，请提交 Azure 支持请求，在该请求中，请包含指明特定 Azure 部署的具体信息，以及 Azure VNet 地址空间的 IP 地址空间范围。 然后，Azure 上的 SAP HANA 服务管理部门将提供所需的信息用于连接其他 VNet 和 ExpressRoute。 对于每个 VNet，需要使用唯一授权密钥连接到 HANA 大型实例 ExpressRoute 线路。

添加新 Azure VNet 的步骤：

1. 完成载入过程的第一步，具体请参阅“创建 Azure VNet”部分。
2. 完成载入过程的第二步，具体请参阅“创建网关子网”部分。
3. 发出 Azure 支持请求，并在其中添加新 VNet 的相关信息，同时请求获取新授权密钥，以便将其他 VNet 连接到 HANA 大型实例 ExpressRoute 线路。
4. 收到已完成授权的通知后，立即使用 Microsoft 提供的授权信息完成载入过程的第三步，具体请参阅“链接 VNet”部分。

## <a name="increasing-expressroute-circuit-bandwidth"></a>提高 ExpressRoute 线路带宽

咨询 Azure 上的 SAP HANA 服务管理部门。 如果有通知指出需要提高 Azure 上的 SAP HANA（大型实例）ExpressRoute 线路的带宽，请创建 Azure 支持请求。 （可以请求将单条线路的带宽提高到最大 10 Gbps）。完成该操作后，会收到通知；不需要采取其他措施即可在 Azure 中提高速度。

## <a name="adding-an-additional-expressroute-circuit"></a>添加其他 ExpressRoute 线路

如果有通知指出需要更多的 ExpressRoute 线路，请咨询 Azure 上的 SAP HANA 服务管理部门，提出 Azure 支持请求来创建新的 ExpressRoute 线路（并获取用于连接该线路的授权信息）。 必须在提出请求之前定义要在 VNet 上使用的地址空间，以便 Azure 上的 SAP HANA 服务管理部门能够提供授权信息。

新建线路且 Azure 上的 SAP HANA 服务管理部门完成配置后，便会收到通知，其中包含继续执行下一步所需的信息。 请遵循上面提供的步骤创建新的 VNet 并将其连接到此附加线路。 如果 Azure VNet 已连接到同一 Azure 区域中的另一条 Azure 上的 SAP HANA（大型实例）ExpressRoute 线路，则无法连接到此附加线路。

## <a name="deleting-a-subnet"></a>删除子网

若要删除 VNet 子网，可以使用 Azure 门户、PowerShell 或 CLI。 如果 Azure VNet IP 地址范围/Azure VNet 地址空间在聚合范围内，则不需要与 Microsoft 协调。 不过，VNet 仍会传播包含已删除的子网的 BGP 路由地址空间。 如果已将 Azure VNet IP 地址范围/Azure VNet 地址空间定义为多个 IP 地址范围，并且其中的某个范围已分配到已删除的子网，则应在 VNet 地址空间中删除该范围，随后告知 Azure 上的 SAP HANA 服务管理部门，让他们从允许 Azure 上的 SAP HANA（大型实例）通信的范围中删除该范围。

若要删除子网，请参阅[删除子网](../../../virtual-network/virtual-network-manage-subnet.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-subnet)以获取有关创建子网的详细信息。

## <a name="deleting-a-vnet"></a>删除 VNet

若要删除虚拟网络，请参阅[删除虚拟网络](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-virtual-network)。 Azure 上的 SAP HANA 服务管理部门将删除 Azure 上的 SAP HANA（大型实例）ExpressRoute 线路中的现有授权，并删除用来与 HANA 大型实例通信的 Azure VNet IP 地址范围/Azure VNet 地址空间。

删除 VNet 后，请提出 Azure 支持请求并提供要删除的 IP 地址空间范围。

为了确保删除所有组件，请删除以下各项：

- ExpressRoute 连接、VNet 网关、VNet 网关公共 IP 和 VNet

## <a name="deleting-an-expressroute-circuit"></a>删除 ExpressRoute 线路

若要删除附加的 Azure 上的 SAP HANA（大型实例）ExpressRoute 线路，请向 Azure 上的 SAP HANA 服务管理部门提出应删除此线路的 Azure 支持请求。 在 Azure 订阅中，可以根据需要删除或保留 VNet。 但是，必须删除 HANA 大型实例 ExpressRoute 线路与链接的 VNet 网关之间的连接。

如果还想要删除某个 VNet，请遵循上一部分中有关删除 VNet 的指导。

**后续步骤**

- 参阅[如何安装和配置 Azure 上的 SAP HANA（大型实例）](hana-installation.md)。
