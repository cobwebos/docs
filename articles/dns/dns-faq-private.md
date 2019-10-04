---
title: Azure 专用 DNS 常见问题解答
description: Azure 专用 DNS 常见问题
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 10/05/2019
ms.author: victorh
ms.openlocfilehash: cb0cc5e99cc07728d475a9f9e54c7eb6a8c7554e
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2019
ms.locfileid: "71959930"
---
# <a name="azure-private-dns-faq"></a>Azure 专用 DNS 常见问题解答

下面是有关 Azure 专用 DNS 的常见问题。

## <a name="does-azure-dns-support-private-domains"></a>Azure DNS 是否支持专用域？

使用 Azure 专用 DNS 区域功能支持专用域。 专用 DNS 区域只能从指定的虚拟网络中解析。 有关详细信息，请参阅[概述](private-dns-overview.md)。

有关 Azure 中其他内部 DNS 选项的信息，请参阅 [VM 和角色实例的名称解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)。

## <a name="will-azure-private-dns-zones-work-across-azure-regions"></a>Azure 专用 DNS 区域是否适用于 Azure 区域？

是。 专用区域支持在跨 Azure 区域的虚拟网络之间进行 DNS 解析。 即使未显式建立虚拟网络对等互连，专用区域也能正常工作。 所有虚拟网络都必须链接到专用 DNS 区域。

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>专用区域是否需要在虚拟网络与 Internet 之间建立连接？

否。 专用区域配合虚拟网络工作。 你可以使用它们来管理虚拟机中的虚拟机或其他资源的域以及跨虚拟网络的其他资源。 无需建立 Internet 连接即可进行名称解析。

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>是否可将同一专用区域用于解析多个虚拟网络？

是。 可以将专用 DNS 区域链接到成千上万个虚拟网络。 有关详细信息，请参阅[Azure DNS 限制](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-dns-limits)

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-linked-to-a-private-zone"></a>属于不同订阅的虚拟网络是否可以链接到专用区域？

是。 你必须对虚拟网络和专用 DNS 区域拥有写入操作权限。 可向多个 RBAC 角色授予“写入”权限。 例如，经典网络参与者 RBAC 角色对虚拟网络具有写入权限，专用 DNS 区域参与者角色对专用 DNS 区域具有写入权限。 有关 RBAC 角色的详细信息，请参阅[基于角色的访问控制](../role-based-access-control/overview.md)。

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>删除虚拟机时，是否会自动删除在专用区域中自动注册的虚拟机 DNS 记录？

是。 如果在已启用自动注册的情况下删除链接的虚拟网络中的虚拟机，则会自动删除已注册的记录。

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>是否可以手动删除从链接虚拟网络的专用区域中自动注册的虚拟机记录？

是。 可以在区域中使用手动创建的 DNS 记录来覆盖此类自动注册的 DNS 记录。 以下问答部分解答了此主题。

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>尝试手动创建新的 DNS 记录到与链接的虚拟网络中的自动注册现有虚拟机具有相同主机名的专用区域时会发生什么情况？

尝试将新的 DNS 记录手动创建到与链接的虚拟网络中的现有、自动注册虚拟机主机名相同的专用区域中。 则新的 DNS 记录会覆盖自动注册的虚拟机记录。 如果再次尝试从区域中删除这条手动创建的 DNS 记录，则删除操作将会成功。 只要虚拟机仍然存在并且其上已附加专用 IP，就会再次发生自动注册。 DNS 记录将在区域中自动重新创建。

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>从专用区域中取消链接链接的虚拟网络时会发生什么情况？ 虚拟网络中自动注册的虚拟机记录是否也会从区域中删除？

是。 若要从专用区域中取消链接链接的虚拟网络，请更新 DNS 区域以删除关联的虚拟网络链接。 在此过程中，自动注册的虚拟机记录将从区域中删除。

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>删除链接到专用区域的链接虚拟网络时会发生什么情况？ 是否必须手动更新专用区域，以将虚拟网络从区域中取消链接为链接的虚拟网络？

否。 如果删除链接的虚拟网络而不先将其从专用区域中取消链接，则删除操作将会成功，并且会自动清除指向 DNS 区域的链接。

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>即使专用区域（例如 private.contoso.com）已链接到虚拟网络，使用默认 FQDN （internal.cloudapp.net）的 DNS 解析仍有效？

是。 专用区域不会替换默认的 Azure 提供的 internal.cloudapp.net 区域。 不管依赖于 Azure 提供的 internal.cloudapp.net 还是自己的专用区域，都请使用要解析的区域的 FQDN。

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>链接的虚拟网络中虚拟机上的 DNS 后缀是否会更改为专用区域的 DNS 后缀？

否。 链接的虚拟网络中虚拟机上的 DNS 后缀将保留为 Azure 提供的默认后缀（“*.internal.cloudapp.net”）。 可以手动将虚拟机上的此 DNS 后缀更改为专用区域的 DNS 后缀。
有关如何更改此后缀的指导，请参阅[使用动态 DNS 在自己的 DNS 服务器中注册主机名](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-ddns#windows-clients)

## <a name="what-are-the-usage-limits-for-azure-dns-private-zones"></a>Azure DNS 专用区域的使用限制是什么？

有关 Azure DNS 专用区域使用限制的详细信息，请参阅[Azure DNS 限制](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-dns-limits)。

## <a name="why-dont-my-existing-private-dns-zones-show-up-in-new-portal-experience"></a>为什么现有的专用 DNS 区域未显示在新的门户体验中？

如果你的现有专用 DNS 区域是使用预览 API 创建的，则必须将这些区域迁移到新的资源模型。 使用预览 API 创建专用 DNS 区域将不会显示在新的门户体验中。 请参阅下面的说明，了解如何迁移到新资源模型。

## <a name="how-do-i-migrate-my-existing-private-dns-zones-to-the-new-model"></a>如何实现将现有的专用 DNS 区域迁移到新模型吗？

强烈建议尽快迁移到新资源模型。 但将支持旧版资源模型，但将不会在此模型的顶层开发进一步的功能。 将来，我们打算弃用它来取代新资源模型。 有关如何将现有专用 DNS 区域迁移到新资源模型的指导，请参阅[Azure DNS 专用区域的迁移指南](private-dns-migration-guide.md)。

## <a name="next-steps"></a>后续步骤

- [了解有关 Azure 专用 DNS 的详细信息](private-dns-overview.md)
