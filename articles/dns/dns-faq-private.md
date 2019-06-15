---
title: Azure 专用 DNS 常见问题解答
description: 有关 Azure 专用 DNS 常见问题
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 6/12/2019
ms.author: victorh
ms.openlocfilehash: c963cb1b6930b41a703b479e0213311d971e6606
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082841"
---
# <a name="azure-private-dns-faq"></a>Azure 专用 DNS 常见问题解答

> [!IMPORTANT]
> Azure 专用 DNS 目前处于公共预览状态。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="does-azure-dns-support-private-domains"></a>Azure DNS 是否支持专用域？

使用 Azure 专用 DNS 区域功能支持对专用域的支持。 专用 DNS 区域托管面向 internet 的 Azure DNS 区域使用相同的工具。 专用区域只能从指定的虚拟网络内部进行解析。 有关详细信息，请参阅[概述](private-dns-overview.md)。

有关 Azure 中其他内部 DNS 选项的信息，请参阅 [VM 和角色实例的名称解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)。

## <a name="will-azure-dns-private-zones-work-across-azure-regions"></a>Azure DNS 专用区域是否可跨 Azure 区域工作？

是的。 专用区域支持在跨 Azure 区域的虚拟网络之间进行 DNS 解析。 即使未显式建立虚拟网络对等互连，专用区域也能正常工作。 必须将所有虚拟网络指定为专用区域的解析虚拟网络。 客户可能需要建立虚拟网络的对等互连，才能在不同的区域之间传送 TCP/HTTP 流量。

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>专用区域是否需要在虚拟网络与 Internet 之间建立连接？

不。 专用区域配合虚拟网络工作。 客户可在虚拟网络内部或跨虚拟网络管理虚拟机的域或其他资源。 无需建立 Internet 连接即可进行名称解析。

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>是否可将同一专用区域用于解析多个虚拟网络？

是的。 您可以使用一个专用区域关联到 1000年的虚拟网络。

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-added-as-a-linked-virtual-network-to-a-private-zone"></a>可以属于不同订阅的虚拟网络添加为链接的虚拟网络到专用区域？

是的。 你必须对虚拟网络和专用 DNS 区域拥有写入操作权限。 可向多个 RBAC 角色授予“写入”权限。 例如，经典网络参与者 RBAC 角色对虚拟网络拥有写入权限。 有关 RBAC 角色的详细信息，请参阅[基于角色的访问控制](../role-based-access-control/overview.md)。

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>将专用区域中的自动注册的虚拟机 DNS 记录自动删除在删除虚拟机？

是的。 如果使用自动注册已启用删除链接的虚拟网络中的虚拟机，将自动删除已注册的记录。

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>可以手动删除链接的虚拟网络从专用区域中的自动注册的虚拟机记录？

是的。 可以在区域中使用手动创建的 DNS 记录来覆盖此类自动注册的 DNS 记录。 以下问答部分解答了此主题。

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>当我尝试手动链接虚拟网络中具有相同的主机名为自动注册的现有虚拟机的专用区域中创建新的 DNS 记录时，会发生什么情况？

您尝试手动链接虚拟网络中具有相同的主机名作为现有的自动注册的虚拟机的专用区域中创建新的 DNS 记录。 则新的 DNS 记录会覆盖自动注册的虚拟机记录。 如果再次尝试从区域中删除这条手动创建的 DNS 记录，则删除操作将会成功。 只要虚拟机仍然存在并且其上已附加专用 IP，就会再次发生自动注册。 DNS 记录将在区域中自动重新创建。

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>链接的虚拟网络从专用区域中取消链接时，会发生什么情况？ 虚拟网络中自动注册的虚拟机记录是否也会从区域中删除？

是的。 若要取消链接链接从专用区域的虚拟网络，你更新 DNS 区域以删除关联的虚拟网络链接。 在此过程中，自动注册的虚拟机记录将从区域中删除。

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>当我们删除链接的虚拟网络链接到专用区域时，会发生什么情况？ 我们是否需要手动更新作为链接虚拟网络从该区域中取消链接虚拟网络的专用区域？

是的。 在不首先取消从专用区域的情况下删除链接的虚拟网络，能够成功删除操作。 但是，虚拟网络不会从专用区域中自动取消链接（如果存在这种链接）。 必须手动从专用区域中取消链接虚拟网络。 出于此原因，请在删除虚拟网络之前，先从专用区域中取消其链接。

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>使用默认 FQDN (internal.cloudapp.net) 的 DNS 解析将仍能即使专用区域 (例如，private.contoso.com) 链接到的虚拟网络？

是的。 专用区域功能不能取代使用 Azure 提供的 internal.cloudapp.net 区域进行的默认 DNS 解析。 它只是一项补充性的功能或增强功能。 不管依赖于 Azure 提供的 internal.cloudapp.net 还是自己的专用区域，都请使用要解析的区域的 FQDN。

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>链接的虚拟网络中虚拟机上的 DNS 后缀是否会更改为专用区域的 DNS 后缀？

不。 链接的虚拟网络中虚拟机上的 DNS 后缀将保留为 Azure 提供的默认后缀（“*.internal.cloudapp.net”）。 可以手动将虚拟机上的此 DNS 后缀更改为专用区域的 DNS 后缀。

## <a name="what-are-the-usage-limits-for-azure-private-dns"></a>Azure 专用 DNS 的使用情况限制是什么？

使用 Azure 专用 DNS 时，将应用以下默认限制。

| Resource | 默认限制 |
| --- | --- |
|每个订阅的专用 DNS 区域|1000|
|每个专用 DNS 区域的记录集|25,000|
|每个记录集的记录数|20|
|每个专用 DNS 区域的虚拟网络链接|1000|
|每个使用自动注册的专用 DNS 区域的虚拟网络链接已启用|100|
|使用自动注册已启用，可以获取虚拟网络链接到的专用 DNS 区域的数目|第|
|虚拟网络可以获取链接的专用 DNS 区域数|1000|

## <a name="is-there-portal-support-for-private-zones"></a>专用区域是否支持门户？

在 Azure 门户中看到是，并通过 Api、 PowerShell、 CLI 和 Sdk 已创建的专用区域。

## <a name="next-steps"></a>后续步骤

- [了解有关 Azure 专用 DNS 的详细信息](private-dns-overview.md)