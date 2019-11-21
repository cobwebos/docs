---
title: Azure 专用 DNS 常见问题解答
description: In this article, learn frequently asked questions about Azure Private DNS
services: dns
author: asudbring
ms.service: dns
ms.topic: article
ms.date: 10/05/2019
ms.author: allensu
ms.openlocfilehash: de63799400a10afc1930cd373df0c8dd86320f78
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74212012"
---
# <a name="azure-private-dns-faq"></a>Azure 专用 DNS 常见问题解答

The following are frequently asked questions about Azure private DNS.

## <a name="does-azure-dns-support-private-domains"></a>Azure DNS 是否支持专用域？

Private domains are supported using the Azure Private DNS zones feature. Private DNS zones are resolvable only from within specified virtual networks. 有关详细信息，请参阅[概述](private-dns-overview.md)。

有关 Azure 中其他内部 DNS 选项的信息，请参阅 [VM 和角色实例的名称解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)。

## <a name="will-azure-private-dns-zones-work-across-azure-regions"></a>Will Azure Private DNS zones work across Azure regions?

可以。 专用区域支持在跨 Azure 区域的虚拟网络之间进行 DNS 解析。 即使未显式建立虚拟网络对等互连，专用区域也能正常工作。 All the virtual networks must be linked to the private DNS zone.

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>专用区域是否需要在虚拟网络与 Internet 之间建立连接？

不。 专用区域配合虚拟网络工作。 You use them to manage domains for virtual machines or other resources within and across virtual networks. 无需建立 Internet 连接即可进行名称解析。

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>是否可将同一专用区域用于解析多个虚拟网络？

可以。 You can link a private DNS zone with thousands of virtual networks. For more information, see [Azure DNS Limits](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-dns-limits)

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-linked-to-a-private-zone"></a>Can a virtual network that belongs to a different subscription be linked to a private zone?

可以。 你必须对虚拟网络和专用 DNS 区域拥有写入操作权限。 可向多个 RBAC 角色授予“写入”权限。 For example, the Classic Network Contributor RBAC role has write permissions to virtual networks and Private DNS zones Contributor role has write permissions on the private DNS zones. 有关 RBAC 角色的详细信息，请参阅[基于角色的访问控制](../role-based-access-control/overview.md)。

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>Will the automatically registered virtual machine DNS records in a private zone be automatically deleted when you delete the virtual machine?

可以。 If you delete a virtual machine within a linked virtual network with autoregistration enabled, the registered records are automatically deleted.

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>Can an automatically registered virtual machine record in a private zone from a linked virtual network be deleted manually?

可以。 可以在区域中使用手动创建的 DNS 记录来覆盖此类自动注册的 DNS 记录。 以下问答部分解答了此主题。

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>What happens when I try to manually create a new DNS record into a private zone that has the same hostname as an automatically registered existing virtual machine in a linked virtual network?

You try to manually create a new DNS record into a private zone that has the same hostname as an existing, automatically registered virtual machine in a linked virtual network. 则新的 DNS 记录会覆盖自动注册的虚拟机记录。 如果再次尝试从区域中删除这条手动创建的 DNS 记录，则删除操作将会成功。 只要虚拟机仍然存在并且其上已附加专用 IP，就会再次发生自动注册。 DNS 记录将在区域中自动重新创建。

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>What happens when we unlink a linked virtual network from a private zone? 虚拟网络中自动注册的虚拟机记录是否也会从区域中删除？

可以。 To unlink a linked virtual network from a private zone, you update the DNS zone to remove the associated virtual network link. 在此过程中，自动注册的虚拟机记录将从区域中删除。

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>What happens when we delete a linked virtual network that's linked to a private zone? Do we have to manually update the private zone to unlink the virtual network as a linked virtual network from the zone?

不。 When you delete a linked virtual network without unlinking it from a private zone first, your deletion operation succeeds and the links to the DNS zone are automatically cleared.

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>Will DNS resolution by using the default FQDN (internal.cloudapp.net) still work even when a private zone (for example, private.contoso.com) is linked to a virtual network?

可以。 Private Zones don't replace the default Azure-provided internal.cloudapp.net zone. 不管依赖于 Azure 提供的 internal.cloudapp.net 还是自己的专用区域，都请使用要解析的区域的 FQDN。

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>链接的虚拟网络中虚拟机上的 DNS 后缀是否会更改为专用区域的 DNS 后缀？

不。 链接的虚拟网络中虚拟机上的 DNS 后缀将保留为 Azure 提供的默认后缀（“*.internal.cloudapp.net”）。 可以手动将虚拟机上的此 DNS 后缀更改为专用区域的 DNS 后缀。
For guidance on how to change this suffix refer to [Use dynamic DNS to register hostnames in your own DNS server](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-ddns#windows-clients)

## <a name="what-are-the-usage-limits-for-azure-dns-private-zones"></a>What are the usage limits for Azure DNS Private zones?

Refer to [Azure DNS limits](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-dns-limits) for details on the usage limits for Azure DNS private zones.

## <a name="why-dont-my-existing-private-dns-zones-show-up-in-new-portal-experience"></a>Why don’t my existing private DNS zones show up in new portal experience?

If your existing private DNS zone were created using preview API, you must migrate these zones to new resource model. Private DNS zones created using preview API will not show up in new portal experience. See below for instructions on how to migrate to new resource model.

## <a name="how-do-i-migrate-my-existing-private-dns-zones-to-the-new-model"></a>How do I migrate my existing private DNS zones to the new model?

We strongly recommend that you migrate to the new resource model as soon as possible. Legacy resource model will be supported, however, further features will not be developed on top of this model. In future, we intend to deprecate it in favor of new resource model. For guidance on how to migrate your existing private DNS zones to new resource model see[migration guide for Azure DNS private zones](private-dns-migration-guide.md).

## <a name="next-steps"></a>后续步骤

- [Learn more about Azure Private DNS](private-dns-overview.md)
