---
title: 什么是 Azure 专用 DNS？
description: Microsoft Azure 上的专用 DNS 托管服务概述。
services: dns
author: vhorne
ms.service: dns
ms.topic: overview
ms.date: 6/12/2019
ms.author: victorh
ms.openlocfilehash: a8548b4972d5853f09630ae3e9ded05ed6fee32b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67076387"
---
# <a name="what-is-azure-private-dns"></a>什么是 Azure 专用 DNS？

> [!IMPORTANT]
> Azure 专用 DNS 目前处于公共预览状态。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

域名系统（或称为 DNS）负责将服务名称转换（或解析）为其 IP 地址。  Azure DNS 是 DNS 域的托管服务，它使用 Microsoft Azure 基础结构提供名称解析。 除了支持面向 internet 的 DNS 域，Azure DNS 还支持专用 DNS 区域。

Azure 专用 DNS 提供可靠、 安全的 DNS 服务来管理和解析虚拟网络而无需添加自定义 DNS 解决方案中的域名。 借助专用 DNS 区域，可以使用自定义域名而不使用当前可用的由 Azure 提供的名称。 使用自定义域名可帮助你定制虚拟网络体系结构以便最好地满足组织需求。 它在虚拟网络内以及在虚拟网络之间针对虚拟机 (VM) 提供名称解析。 除此之外，还可以通过水平分割视图配置区域名称，从而允许专用和公用 DNS 区域共享名称。

若要解决从虚拟网络的专用 DNS 区域的记录，必须链接虚拟网络的区域。 链接虚拟网络具有完全访问权限，可以解决在专用区域中发布的所有 DNS 记录。 此外，还可以启用自动注册虚拟网络链接。 如果启用自动注册虚拟网络链接上的，在专用区域中注册该虚拟网络上的虚拟机的 DNS 记录。 启用自动注册后，Azure DNS 还会更新区域记录时创建虚拟机后，更改其 IP 地址，或已被删除。

![DNS 概述](./media/private-dns-overview/scenario.png)

> [!NOTE]
> 最佳做法是，不要使用 *.local*专用 DNS 区域的域。 并非所有操作系统都支持此功能。

## <a name="benefits"></a>优点

Azure 专用 DNS 提供以下优势：

* **无需使用自定义 DNS 解决方案**。 以前，许多客户创建了自定义 DNS 解决方案来管理其虚拟网络。 现在可以管理 DNS 区域使用本机 Azure 基础结构，这将删除创建和管理自定义 DNS 解决方案的负担。

* **使用所有常见的 DNS 记录类型**。 Azure DNS 支持 A、AAAA、CNAME、MX、PTR、SOA、SRV 和 TXT 记录。

* **自动化主机名记录管理**。 除了承载自定义 DNS 记录之外，Azure 还会自动维护指定虚拟网络中的 VM 的主机名记录。 在此场景中，可以优化所使用的域名，不需要创建自定义 DNS 解决方案或修改应用程序。

* **虚拟网络之间的主机名解析**。 不同于 Azure 提供的主机名，专用 DNS 区域可以在虚拟网络之间共享。 此功能简化了跨网络和服务发现方案，例如，虚拟网络对等互连。

* **熟悉的工具和用户体验**。 为了降低学习难度，此服务，请使用成熟的 Azure DNS 工具 （Azure 门户、 Azure PowerShell、 Azure CLI、 Azure 资源管理器模板和 REST API）。

* **水平分割 DNS 支持**。 借助 Azure DNS，可以使用相同的名称创建在虚拟网络内与在公共 Internet 内分别解析为不同结果的区域。 水平分割 DNS 的典型方案是提供一个专用服务版本以在虚拟网络内部使用。

* **在所有 Azure 区域中可用**。 Azure 公有云中的所有 Azure 区域均已推出 Azure DNS 专用区域功能。

## <a name="capabilities"></a>功能

Azure DNS 提供以下功能：

* **使用自动注册已启用链接到专用区域的虚拟网络中的虚拟机的自动注册**。 虚拟机都已注册 （添加） 到专用区域作为指向其专用 IP 地址的 A 记录。 当删除启用了自动注册的虚拟网络链接中的虚拟机时，Azure DNS 还会自动从链接的专用区域中删除相应的 DNS 记录。

* **链接到专用区域的虚拟网络之间支持正向 DNS 解析**。 对于跨虚拟网络 DNS 解析，没有任何显式依赖关系，以便与每个其他虚拟网络对等互连。 但是，你可能想要对等互连虚拟网络的其他方案 （例如，HTTP 流量）。

* **在虚拟网络范围内支持反向 DNS 查找**。 对分配到专用区域的虚拟网络中的专用 IP 进行反向 DNS 查找会返回 FQDN，其中包括主机/记录名称以及作为后缀的区域名称。

## <a name="other-considerations"></a>其他注意事项

Azure DNS 具有以下限制：

* 只允许在每个专用区域中使用 1 个“注册”虚拟网络。
* 最多允许在每个专用区域中使用 10 个“解析”虚拟网络。 当此功能正式发行时，将删除此限制。
* 一个特定的虚拟网络只能作为注册虚拟网络链接到 1 个专用区域。
* 一个特定的虚拟网络可以作为解析虚拟网络链接到最多 10 个专用区域。 当此功能正式发行时，将删除此限制。
* 如果指定了注册虚拟网络，则无法通过 Azure Powershell 和 Azure CLI API 查看或检索注册到专用区域的虚拟网络中的 VM 的 DNS 记录。 VM 记录确实已注册并且会成功解析。
* 反向 DNS 仅适用于注册虚拟网络中的专用 IP 空间。
* 未注册到专用区域中的专用 IP（例如，作为解析虚拟网络链接到专用区域的虚拟网络中的虚拟机专用 IP）的反向 DNS 将返回 *internal.cloudapp.net* 作为 DNS 后缀。 但此后缀不可解析。
* 当虚拟网络首次链接到专用区域作为注册或解析虚拟网络时，它必须完全为空。 但是，以后作为注册或解析虚拟网络链接到其他专用区域时，虚拟网络可以是非空的。
* 当前不支持条件转发（例如，为了启用 Azure 与本地网络之间的解析）。 有关客户可以如何通过其他机制实现此方案的信息，请参阅 [VM 和角色实例的名称解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)。
* 反向 DNS 仅适用于链接的虚拟网络中的专用 IP 空间
* 链接的虚拟网络的专用 IP 的反向 DNS 返回"internal.cloudapp.net"作为虚拟机的默认后缀。 对于启用了自动注册与链接到专用区域的虚拟网络，反向 DNS 用于专用 IP 返回 2 Fqdn，另一个使用默认的后缀*internal.cloudapp.net* ，另一个具有专用区域后缀。
* 不支持条件性转发。 例如，若要启用 Azure 和本地网络之间进行解析。 了解如何启用此方案中使用其他机制。 请参阅 [VM 和角色实例的名称解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)
 


## <a name="pricing"></a>定价

有关定价信息，请参阅[Azure DNS 定价](https://azure.microsoft.com/pricing/details/dns/)。

## <a name="next-steps"></a>后续步骤

* 了解如何使用 [Azure PowerShell](./private-dns-getstarted-powershell.md) 或 [Azure CLI](./private-dns-getstarted-cli.md) 在 Azure DNS 中创建专用区域。

* 了解可以通过 Azure DNS 中的专用区域实现的一些常见[专用区域方案](./private-dns-scenarios.md)。

* 有关常见问题和解答有关 Azure DNS 中专用区域，包括特定行为你可以期望对于某些类型的操作，请参阅[专用 DNS 常见问题解答](./dns-faq-private.md)。

* 访问 [DNS 区域和记录概述](dns-zones-records.md)，了解 DNS 区域和记录。

* 了解 Azure 的一些其他关键[网络功能](../networking/networking-overview.md)。
