---
title: "Azure 虚拟机规模集的设计注意事项 | Microsoft Docs"
description: "了解 Azure 虚拟机规模集的设计注意事项"
keywords: "linux 虚拟机, 虚拟机规模集"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: madhana
editor: tysonn
tags: azure-resource-manager
ms.assetid: c27c6a59-a0ab-4117-a01b-42b049464ca1
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: negat
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 615361975e2ee15ce80f6efb39f57cae381209e5
ms.contentlocale: zh-cn
ms.lasthandoff: 08/09/2017

---
# <a name="design-considerations-for-scale-sets"></a>规模集的设计注意事项
本主题讨论虚拟机规模集的设计注意事项。 有关什么是虚拟机规模集的信息，请参阅[虚拟机规模集概述](virtual-machine-scale-sets-overview.md)。

## <a name="when-to-use-scale-sets-instead-of-virtual-machines"></a>何时使用规模集而不使用虚拟机？
一般而言，规模集非常适合用于部署高可用性基础结构（其中的一组计算机采用类似配置）。 但是，有些功能只能在规模集中使用，还有些功能只能在 VM 中使用。 若要就何时使用哪种技术做出明智的决策，我们首先应该大致了解可在规模集中使用，但不能在 VM 中使用的一些常用功能：

### <a name="scale-set-specific-features"></a>特定于规模集的功能

- 指定规模集配置后，只需更新“容量”属性即可同时部署更多的 VM。 这比编写一个脚本来协调众多 VM 的同时部署要简单得多。
- 可以[使用 Azure 自动缩放来自动缩放规模集](./virtual-machine-scale-sets-autoscale-overview.md)，但不能使用它来自动缩放单个 VM。
- 可以[重置规模集 VM 的映像](https://docs.microsoft.com/rest/api/virtualmachinescalesets/manage-a-vm)，但[不能重置单个 VM 的映像](https://docs.microsoft.com/rest/api/compute/virtualmachines)。
- 可以[过度预配](./virtual-machine-scale-sets-design-overview.md)规模集 VM 以提高可靠性和加快部署速度。 除非编写自定义代码，否则在单个 VM 上无法做到这一点。
- 可以指定[升级策略](./virtual-machine-scale-sets-upgrade-scale-set.md)，方便在规模集中的各个 VM 上实施升级。 使用单个 VM 时，必须自行协调更新。

### <a name="vm-specific-features"></a>特定于 VM 的功能

另一方面，某些功能只能在 VM 中使用（至少目前是这样）：

- 可将数据磁盘附加到特定的单个 VM，但附加的数据磁盘是针对规模集中的所有 VM 配置的。
- 可将非空数据磁盘附加到单个 VM，但不能附加到规模集中的 VM。
- 可以创建单个 VM 的快照，但不能创建规模集中 VM 的快照。
- 可以从单个 VM 捕获映像，但不能从规模集中的 VM 捕获映像。
- 可将单个 VM 从本机磁盘迁移到托管磁盘，但对于规模集中的 VM，无法执行此操作。
- 可将 IPv6 公共 IP 地址分配给单个 VM 的 NIC，但对于规模集中的 VM，无法执行此操作。 请注意，可将 IPv6 公共 IP 地址分配到单个 VM 或规模集 VM 前面的负载均衡器。

## <a name="storage"></a>存储

### <a name="scale-sets-with-azure-managed-disks"></a>使用 Azure 托管磁盘创建规模集
可以使用 [Azure 托管磁盘](../storage/storage-managed-disks-overview.md)而不是传统的 Azure 存储帐户创建规模集。 托管磁盘可以提供以下优点：
- 无需为规模集 VM 预先创建一组 Azure 存储帐户。
- 可以为规模集中的 VM 定义[附加的数据磁盘](virtual-machine-scale-sets-attached-disks.md)。
- 可以将规模集配置为[在一个规模集中最多支持 1,000 个 VM](virtual-machine-scale-sets-placement-groups.md)。 

如果存在现有的模板，则还可以[将该模板更新为使用托管磁盘](virtual-machine-scale-sets-convert-template-to-md.md)。

### <a name="user-managed-storage"></a>用户管理的存储
未使用 Azure 托管磁盘定义的规模集依赖于用户创建的存储帐户在规模集中存储 VM 的 OS 磁盘。 建议为每个存储帐户预配不超过 20 个 VM 的比率，以实现最大 IO，并充分利用_过度预配_（请参见下文）。 还建议存储帐户名称的开始部分字符采用字母表中的不同字符。 这样做有助于在多个不同的内部系统中分散负载。 


## <a name="overprovisioning"></a>预配过度
规模集当前默认为“过度预配”VM。 开启过度预配时，规模集实际预配的 VM 数量超过所要求的数量，在成功预配所请求数量的 VM 后删除额外的 VM。 过度预配可提高预配成功率和减少部署时间。 这些额外的 VM 不会计费，并且不会计入配额限制。

虽然过度预配可以提高预配成功率，但对于不是用于处理时而出现时而消失的额外 VM 的应用程序，会导致其行为混乱。 若要关闭过度预配，请确保模板中包含以下字符串：`"overprovision": "false"`。 可在[规模集 REST API 文档](/rest/api/virtualmachinescalesets/create-or-update-a-set)中找到更多详细信息。

如果规模集使用用户管理的存储，并且关闭了过度预配，则可为每个存储帐户预配超过 20 个 VM，但是出于 IO 性能考虑，建议不要超过 40 个 VM。 

## <a name="limits"></a>限制
在 Marketplace 映像（也称为平台映像）上构建并配置为使用 Azure 托管磁盘的规模集最多支持 1,000 个 VM 的容量。 如果将规模集配置为支持超过 100 个 VM，则并非所有方案的运行方式都相同（例如负载均衡）。 有关详细信息，请参阅[使用大型虚拟机规模集](virtual-machine-scale-sets-placement-groups.md)。 

使用用户管理的存储帐户配置的规模集目前限制为 100 个 VM（建议为此规模使用 5 个存储帐户）。

基于自定义映像（用户构建的映像）构建的规模集配置 Azure 托管磁盘后可具有 100 个 VM 的容量。 如果规模集配置了用户管理的存储帐户，则必须在同一存储帐户中创建所有 OS 磁盘 VHD。 因此，基于自定义映像和用户管理的存储构建的规模集中 VM 的最大建议数目为 20。 如果关闭预配过度，最大可为 40。

对于高出这些限制所允许的 VM，需要部署多个规模集，如[此模板](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale)所示。


