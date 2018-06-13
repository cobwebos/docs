---
title: Azure DNS 专用区域的方案 | Microsoft Docs
description: 使用 Azure DNS 专用区域的常见方案的概述。
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2018
ms.author: kumud
ms.openlocfilehash: de543913d4f8264fa8e5b3bca0c510c99c479cae
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2018
ms.locfileid: "32771865"
---
# <a name="azure-dns-private-zones-scenarios"></a>Azure DNS 专用区域方案
Azure DNS 专用区域在虚拟网络内或虚拟网络之间提供名称解析功能。 在本文中，我们将查看可以使用此功能实现的一些常见方案。 

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

## <a name="scenario-name-resolution-scoped-to-a-single-virtual-network"></a>方案：在单个虚拟网络范围内的名称解析
在此方案中，你在 Azure 中有一个包含大量 Azure 资源的虚拟网络，包括虚拟机 (VM)。 你希望通过一个特定域名（DNS 区域）从虚拟网络内解析资源，并且你要求名称解析是专用的并且不可从 Internet 访问。 而且，对于 VNET 中的 VM，你要求 Azure 自动将它们注册到 DNS 区域中。 

下面描绘了此方案。 名为“A”的虚拟网络包括两个 VM（VNETA-VM1 和 VNETA-VM2）。 它们每个都关联了专用 IP。 在你创建名为 contoso.com 的专用区域并将此虚拟网络作为“注册”虚拟网络进行链接后，Azure DNS 将自动在该区域中创建两个 A 记录，如下图中所示。 现在，从 VNETA-VM1 发出的对 VNETA-VM2.contoso.com 进行解析的 DNS 查询将收到包含 VNETA-VM2 的专用 IP 的 DNS 响应。 而且，如你所料，从 VNETA-VM2 发出的对 VNETA-VM1 的专用 IP (10.0.0.1) 的反向 DNS 查询 (PTR) 将收到包含 VNETA-VM1 的名称的 DNS 响应。 

![单虚拟网络解析](./media/private-dns-scenarios/single-vnet-resolution.png)

## <a name="scenario-name-resolution-across-virtual-networks"></a>方案：跨虚拟网络的名称解析

此方案是更常见的案例，其中，你需要将一个专用区域与多个虚拟网络进行关联。 此方案适合中心辐射模型之类的体系结构，其中有一个位于中央的中心虚拟网络，多个其他辐射虚拟网络都连接到中心虚拟网络。 中央的中心虚拟网络可以作为“注册”虚拟网络链接到专用区域，辐射虚拟网络可以作为“解析”虚拟网络进行链接。 

下图显示了此方案的一个简单版本，其中只有两个虚拟网络 - A 和 B。A 被指定为“注册”虚拟网络，B 被指定为“解析”虚拟网络。 目的是使两个虚拟网络共享公用区域 contoso.com。当创建了该区域并将“解析”和“注册”虚拟网络链接到该区域后，Azure 将自动为虚拟网络 A 中的 VM（VNETA-VM1 和 VNETA-VM2）注册 DNS 记录。你还可以手动为“解析”虚拟网络 B 中的 VM 将 DNS 记录添加到该区域中。使用此设置时，对于正向和反向 DNS 查询，你将会看到以下行为：
* 从“解析”虚拟网络 B 中的 VNETB-VM1 发出的针对 VNETA-VM1.contoso.com 的 DNS 查询将收到包含 VNETA-VM1 的专用 IP 的 DNS 响应。
* 从“解析”虚拟网络 B 中的 VNETB-VM2 发出的针对 10.1.0.1 的反向 DNS (PTR) 查询将收到包含 FQDN VNETB-VM1.contoso.com 的 DNS 响应。原因是反向 DNS 查询的范围是同一虚拟网络。 
* 从“解析”虚拟网络 B 中的 VNETB-VM3 发出的针对 10.0.0.1 的反向 DNS (PTR) 查询将收到 NXDOMAIN。 原因是反向 DNS 查询的范围仅限于同一虚拟网络。 


![多虚拟网络解析](./media/private-dns-scenarios/multi-vnet-resolution.png)

## <a name="scenario-split-horizon-functionality"></a>方案：水平分割功能

在此方案中，你希望根据客户端所在的位置（位于 Azure 内部，还是位于外部在 Internet 上）为同一 DNS 区域实现不同的 DNS 解析行为。 例如，你的应用程序可能有专用和公用版本，它们具有不同的功能或行为，但你希望为两个版本使用同一域名。 可以使用 Azure DNS 通过创建同名的公用 DNS 区域和专用区域来实现此方案。

下图描绘了此方案。 虚拟网络 A 中有两个 VM（VNETA-VM1 和 VNETA-VM2），它们同时分配有专用 IP 和公用 IP。 你创建一个名为 contoso.com 的公用 DNS 区域，并且将这些 VM 的公用 IP 注册为该区域中的 DNS 记录。 你还创建名称也是 contoso.com 的一个专用 DNS 区域，并将 A 指定为“注册”虚拟网络。 Azure 会自动将 VM 作为 A 记录注册到专用区域中并指向其专用 IP。

现在，当 Internet 客户端发出 DNS 查询来查找 VNETA-VM1.contoso.com 时，Azure 将返回来自公用区域的公用 IP 记录。 如果同一虚拟网络 A 中的另一 VM（例如 VNETA-VM2）发出同一 DNS 查询，则 Azure 将返回来自专用区域的专用 IP 记录。 

![裂脑解析](./media/private-dns-scenarios/split-brain-resolution.png)

## <a name="next-steps"></a>后续步骤
若要详细了解专用 DNS 区域，请参阅[将 Azure DNS 用于专用域](private-dns-overview.md)。

了解如何在 Azure DNS 中[创建专用 DNS 区域](./private-dns-getstarted-powershell.md)。

若要了解 DNS 区域和记录，请访问 [DNS 区域和记录概述](dns-zones-records.md)。

了解 Azure 的一些其他关键[网络功能](../networking/networking-overview.md)。

