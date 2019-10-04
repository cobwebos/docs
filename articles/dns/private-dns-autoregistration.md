---
title: Azure DNS 专用区域的自动注册功能
description: Azure DNS 专用区域的自动注册功能概述
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 9d1854b459e799d5cbb401de9ac717dd7d0fde1d
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2019
ms.locfileid: "71961237"
---
# <a name="what-is-the-autoregistration-feature-of-azure-dns-private-zones"></a>Azure DNS 专用区域的自动注册功能

Azure DNS 专用区域自动注册功能将对虚拟网络中部署的虚拟机的 DNS 记录管理困难。 当你将[虚拟网络](./private-dns-virtual-network-links.md)与专用 dns 区域链接并为所有虚拟机启用自动注册时，将在专用 DNS 区域中自动创建在虚拟网络中部署的虚拟机的 DNS 记录。 除了向前查找记录（A 记录）之外，还会为虚拟机自动创建反向查找记录（PTR 记录）。
如果将更多的虚拟机添加到虚拟网络，则也会在链接的专用 DNS 区域中自动创建这些虚拟机的 DNS 记录。

删除虚拟机时，会自动从专用 DNS 区域中删除该虚拟机的 DNS 记录。

可以通过在创建虚拟网络链接时选择 "启用自动注册" 选项来启用自动注册。

![启用自动注册](./media/privatedns-concepts/enable-autoregistration.png)

## <a name="restrictions"></a>限制

* 自动注册仅适用于虚拟机。 对于内部负载均衡器等所有其他资源，你可以在链接到虚拟网络的专用 DNS 区域中手动创建 DNS 记录。
* 仅为主虚拟机 NIC 自动创建 DNS 记录。 如果虚拟机有多个 NIC，则可以为其他网络接口手动创建 DNS 记录。
* 不支持 IPv6 的自动注册（AAAA 记录）。

## <a name="next-steps"></a>后续步骤

* 了解如何使用 [Azure PowerShell](./private-dns-getstarted-powershell.md) 或 [Azure CLI](./private-dns-getstarted-cli.md) 在 Azure DNS 中创建专用区域。

* 了解可以通过 Azure DNS 中的专用区域实现的一些常见[专用区域方案](./private-dns-scenarios.md)。

* 有关 Azure DNS 专用区域的一些常见问题和解答，包括对于某些类型的操作可以期待的特定行为，请参阅[专用 DNS 常见问题解答](./dns-faq-private.md)。
