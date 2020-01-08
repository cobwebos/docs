---
title: 什么是 Azure DNS 专用区域的虚拟网络链接 subresource
description: 虚拟网络链接子资源 Azure DNS 专用区域概述
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 9181ef93dfedbc28b297bef48a0bc37ba6d69798
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646755"
---
# <a name="what-is-a-virtual-network-link"></a>什么是虚拟网络链接？

在 Azure 中创建专用 DNS 区域后，不能立即从任何虚拟网络访问它。 必须先将其链接到虚拟网络，然后该网络中托管的 VM 才能访问专用 DNS 区域。
若要将专用 DNS 区域与虚拟网络链接，必须在专用 DNS 区域下创建虚拟网络链接。 每个专用 DNS 区域都有一个虚拟网络链接子资源的集合。 其中每个资源都表示一个到虚拟网络的连接。

可以将虚拟网络链接到专用 DNS 区域，作为注册虚拟网络，或将其作为解析虚拟网络。

## <a name="registration-virtual-network"></a>注册虚拟网络

在专用 DNS 区域和虚拟网络之间[创建链接](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network)时，可以选择为虚拟机启用 DNS 记录[自动注册](./private-dns-autoregistration.md)。 如果选择此选项，则虚拟网络将成为专用 DNS 区域的注册虚拟网络。 系统会自动为在网络中部署的虚拟机创建 DNS 记录。 将为已在虚拟网络中部署的虚拟机创建 DNS 记录。 从虚拟网络的角度来看，专用 DNS 区域成为该虚拟网络的注册区域。
一个专用 DNS 区域可以有多个注册虚拟网络，但每个虚拟网络只能有一个与其关联的注册区域。

## <a name="resolution-virtual-network"></a>解析虚拟网络

如果在专用 DNS 区域下创建虚拟网络链接，并选择不启用 DNS 记录自动注册，则虚拟网络将被视为仅限解析虚拟网络。 不会在链接的专用 DNS 区域中自动创建此类网络中部署的虚拟机的 DNS 记录。 但是，在此类网络中部署的虚拟机可以成功地从专用 DNS 区域查询 DNS 记录。 可以手动创建这些记录，也可以使用专用 DNS 区域链接为注册网络的其他虚拟网络来填充这些记录。
一个专用 DNS 区域可以有多个解析虚拟网络，一个虚拟网络可以有多个与之关联的解析区域。

## <a name="limits"></a>限制

若要了解多少注册和解析网络，可以链接到专用 DNS 区域，请参阅[Azure DNS 限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits)

## <a name="other-considerations"></a>其他注意事项

* 不支持使用经典部署模型部署的虚拟网络。

* 只能在专用 DNS 区域和虚拟网络之间创建一个链接。

* 专用 DNS 区域下的每个虚拟网络链接都必须在专用 DNS 区域的上下文中具有唯一的名称。 你可以在不同的专用 DNS 区域中具有相同名称的链接。

* 创建虚拟网络链接后，请检查虚拟网络链接资源的 "链接状态" 字段。 根据虚拟网络的大小，此操作可能需要几分钟才能完成，链接状态将更改为 "*已完成*"。

* 删除虚拟网络时，会自动删除在不同专用 DNS 区域中与之关联的所有虚拟网络链接和自动注册的 DNS 记录。

## <a name="next-steps"></a>后续步骤

* 了解如何使用[Azure 门户](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network)将虚拟网络链接到专用 DNS 区域

* 了解如何使用 [Azure PowerShell](./private-dns-getstarted-powershell.md) 或 [Azure CLI](./private-dns-getstarted-cli.md) 在 Azure DNS 中创建专用区域。

* 了解可以通过 Azure DNS 中的专用区域实现的一些常见[专用区域方案](./private-dns-scenarios.md)。

* 有关 Azure DNS 专用区域的一些常见问题和解答，包括对于某些类型的操作可以期待的特定行为，请参阅[专用 DNS 常见问题解答](./dns-faq-private.md)。
