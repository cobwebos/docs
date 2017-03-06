---
title: "Azure 中的网络接口 | Microsoft 文档"
description: "了解 Azure 网络接口及其与虚拟机配合使用的方式。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f58b503f-18bf-4377-aa63-22fc8a96e4be
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: 395cff80b3f97b6340e15f370c13f783e2f5dde3
ms.lasthandoff: 02/28/2017


---
# <a name="what-are-network-interfaces"></a>什么是网络接口？

网络接口 (NIC) 是虚拟机 (VM) 与基础软件网络之间互相连接的桥梁。 本文解释什么是网络接口，以及在 Azure Resource Manager 部署模型中如何使用它。

Microsoft 建议使用 Resource Manager 部署模型来部署新资源，但也可以在[经典](virtual-network-ip-addresses-overview-classic.md)部署模型中部署具有网络连接的 VM。 如果你熟悉经典模式，请注意，Resource Manager 部署模型中的 VM 网络具有重要的差别。 请阅读 [Virtual machine networking - Classic](virtual-network-ip-addresses-overview-classic.md#differences-between-resource-manager-and-classic-deployments)（虚拟机网络 - 经典）一文详细了解这些差别。

在 Azure 中：

1. 网络接口是一种可以创建、删除的资源，具有自身的可配置的设置。
2. 在创建时，网络接口必须连接到 Azure 虚拟网络 (VNet) 中的一个子网。 如果你不熟悉 VNet，请阅读 [Virtual network overview](virtual-networks-overview.md)（虚拟网络概述）一文了解详细信息。 NIC 必须连接到与 NIC 位于相同 Azure [位置](https://azure.microsoft.com/regions)和[订阅](../azure-glossary-cloud-terminology.md#subscription)中的 VNet。 创建 NIC 之后，可以更改它连接到的子网，但无法更改它连接到的 VNet。
3. 有一个分配的名称，创建 NIC 后无法更改此名称。 该名称在 Azure [资源组](../azure-resource-manager/resource-group-overview.md#resource-groups)中必须唯一，但是在订阅中、创建所在的 Azure 位置中或者连接到的 VNet 中不必要唯一。 通常会在 Azure 订阅中创建多个 NIC。 建议制定一种命名约定，以便更轻松地管理多个 NIC，而不像使用默认名称时那样麻烦。 有关建议，请参阅 [Recommended naming conventions for Azure resources](../guidance/guidance-naming-conventions.md)（Azure 资源的建议命名约定）一文。
4. 可以附加到 VM，但只能附加到与 NIC 位于相同位置的单个 VM。
5. 具有 MAC 地址。只要 NIC 与 VM 保持连接，MAC 地址就会在 NIC 上保留。 无论是使用 Azure 门户、Azure PowerShell 或 Azure 命令行接口将 VM 重新启动（从操作系统内部）还是停止（解除分配）再启动，MAC 地址都会保留。 如果将 NIC 从 VM 分离，然后将其附加到不同的 VM，则 NIC 会收到不同的 MAC 地址。 如果删除 NIC，MAC 地址将分配到其他 NIC。
6. 必须向 NIC 分配一个主要**专用** *IPv4* 静态或动态 IP 地址。
7. 可将一个或多个公用 IP 地址资源与之关联，有关详细信息，请参阅[每个 NIC 具有多个 IP 地址](virtual-network-multiple-ip-addresses-portal.md)这一文档。
8. 运行特定 Microsoft Windows Server 操作系统版本的特定 VM 大小支持具有单根 I/O 虚拟化 (SR-IOV) 技术的加速网络功能。 有关此预览版功能的详细信息，请阅读[适用于虚拟机的加速网络](virtual-network-accelerated-networking-powershell.md)一文。
9. 如果为 NIC 启用了 IP 转发，NIC 可以接收目标不是其分配的专用 IP 地址的流量。 例如，如果 VM 正在运行防火墙软件，则会路由目标不是其自身 IP 地址的数据包。 VM 仍然必须运行可以路由或转发流量的软件，但为此必须对 NIC 启用 IP 转发。
10. NIC 通常在与它附加到的 VM 相同的资源组中创建，或者在它连接到的相同 VNet 中创建，但这不是强制性的要求。

## <a name="vms-with-multiple-network-interfaces"></a>具有多个网络接口的 VM
可将多个 NIC 附加到一个 VM，但如果这样做，请注意以下事项：  

* VM 大小必须支持多个 NIC。 有关哪些 VM 大小支持多个 NIC 的详细信息，请阅读文章 [Windows Server VM 大小](../virtual-machines/virtual-machines-windows-sizes.md)或 [Linux VM 大小](../virtual-machines/virtual-machines-linux-sizes.md)。
* 必须至少使用两个 NIC 创建 VM。 如果只使用一个 NIC 创建 VM，则即使 VM 大小支持多个 NIC，创建 VM 后也无法向其附加更多 NIC。 如果至少使用两个 NIC 创建 VM，则创建后可以向其附加更多 NIC，前提是 VM 大小支持两个以上的 NIC。  
* 如果 VM 上至少附加了三个 NIC，可以从 VM 中分离辅助 NIC（无法分离主 NIC）。 如果 VM 上附加的 NIC 不超过两个，则无法分离 NIC。  
* VM 内部 NIC 的顺序将是随机的，在 Azure 基础结构更新过程中也可能会更改。 不过，IP 地址和相应的以太网 MAC 地址将会保持不变。 例如，假设操作系统将 Azure NIC1 识别为 Eth1。 Eth1 的 IP 地址为 10.1.0.100，MAC 地址为 00-0D-3A-B0-39-0D。 更新并重新启动 Azure 基础结构后，操作系统可将 Azure NIC1 识别为 Eth2，但 IP 和 MAC 地址与操作系统将 Azure NIC1 识别为 Eth1 时一样。 如果是客户执行的重新启动，NIC 顺序将在操作系统中保持不变。  
* 如果 VM 是某个[可用性集](../azure-glossary-cloud-terminology.md#availability-set)的成员，该可用性集中的所有 VM 必须有一个 NIC 或多个 NIC。 如果 VM 有多个 NIC，则只要每个 VM 至少有两个 NIC，每个 NIC 的编号就不需要相同。

## <a name="next-steps"></a>后续步骤
* 阅读[创建 VM](../virtual-machines/virtual-machines-windows-hero-tutorial.md) 一文，了解如何创建具有单个 NIC 的 VM。
* 阅读[部署具有多个 NIC 的 VM](virtual-network-deploy-multinic-arm-ps.md) 一文，了解如何创建具有多个 NIC 的 VM。
* 阅读 [Azure 虚拟机的多个 IP 地址](virtual-network-multiple-ip-addresses-powershell.md)一文，了解如何创建具有多个 IP 配置的 NIC。


