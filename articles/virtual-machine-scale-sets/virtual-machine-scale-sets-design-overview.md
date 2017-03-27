---
title: "设计规模化的 Azure 虚拟机规模集 | Microsoft Docs"
description: "了解如何设计规模化的 Azure 虚拟机规模集"
keywords: "linux 虚拟机, 虚拟机缩放集"
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
ms.date: 02/13/2017
ms.author: negat
translationtype: Human Translation
ms.sourcegitcommit: e869b06935736fae72bd3b5407ebab7c3830098d
ms.openlocfilehash: de3687a1bf36bf49db400a5660ac631f20b629d0
ms.lasthandoff: 02/14/2017


---
# <a name="designing-vm-scale-sets-for-scale"></a>设计规模化的 VM 规模集
本主题讨论虚拟机规模集的设计注意事项。 有关什么是虚拟机规模集的信息，请参阅[虚拟机规模集概述](virtual-machine-scale-sets-overview.md)。

## <a name="storage"></a>存储

### <a name="scale-sets-with-azure-managed-disks"></a>使用 Azure 托管磁盘创建规模集
现可使用 [Azure 托管磁盘](../storage/storage-managed-disks-overview.md)创建规模集。 托管磁盘可以提供以下优点：
- 无需为规模集 VM 预先创建一组 Azure 存储帐户。
- 可以为规模集中的 VM 定义[附加的数据磁盘](virtual-machine-scale-sets-attached-disks.md)。
- 可以将规模集配置为[在一个规模集中最多支持 1,000 个 VM](virtual-machine-scale-sets-placement-groups.md)。 

从 Azure Compute API 的版本“2016-04-30-preview”开始可使用托管磁盘创建规模集。 有关将规模集模板转换为托管磁盘的信息，请参阅[将规模集模板转换为托管磁盘规模集模板](virtual-machine-scale-sets-convert-template-to-md.md)。

### <a name="user-managed-storage"></a>用户管理的存储
未使用 Azure 托管磁盘定义的规模集依赖于用户创建的存储帐户在规模集中存储 VM 的 OS 磁盘。 建议为每个存储帐户预配不超过 20 个 VM 的比率，以实现最大 IO，并充分利用_过度预配_（请参见下文）。 还建议存储帐户名称的开始部分字符采用字母表中的不同字符。 这样做有助于在多个不同的内部系统中分散负载。 

>[!NOTE]
>VM 规模集 API 版本 `2016-04-30-preview` 支持对操作系统磁盘和任何额外数据磁盘使用 Azure 托管磁盘。 有关详细信息，请参阅[托管磁盘概述](../storage/storage-managed-disks-overview.md)和[使用附加数据磁盘](virtual-machine-scale-sets-attached-disks.md)。 

## <a name="overprovisioning"></a>预配过度
从 "2016-03-30" API 版本开始，VM 规模集默认设置为“过度预配”VM。 开启过度预配时，规模集实际预配的 VM 数量超过所要求的数量，在成功预配所请求数量的 VM 后删除额外的 VM。 过度预配可提高预配成功率和减少部署时间。 这些额外的 VM 不会计费，并且不会计入配额限制。

虽然过度预配可以提高预配成功率，但对于不是用于处理时而出现时而消失的额外 VM 的应用程序，会导致其行为混乱。 为了关闭预配过度，请确保你的模板中包含以下字符串："overprovision": "false"。 可在 [VM 规模集 REST API 文档](https://msdn.microsoft.com/library/azure/mt589035.aspx)找到更多详细信息。

如果规模集使用用户管理的存储，并且关闭了过度预配，则可为每个存储帐户预配超过 20 个 VM，但是出于 IO 性能考虑，建议不要超过 40 个 VM。 

## <a name="limits"></a>限制
在应用商店映像（也称为平台映像）上构建并配置为使用 Azure 托管磁盘的规模集最多支持 1,000 个 VM 的容量。 如果将规模集配置为支持超过 100 个 VM，则并非所有方案的运行方式都相同（例如负载均衡）。 有关详细信息，请参阅[使用大型虚拟机规模集](virtual-machine-scale-sets-placement-groups.md)。 

使用用户管理的存储帐户配置的规模集目前限制为 100 个 VM（建议为此规模使用 5 个存储帐户）。

基于自定义映像（用户构建的映像）构建的规模集配置 Azure 托管磁盘后可具有 100 个 VM 的容量。 如果规模集配置了用户管理的存储帐户，则必须在同一存储帐户中创建所有 OS 磁盘 VHD。 因此，基于自定义映像和用户管理的存储构建的规模集中 VM 的最大建议数目为 20。 如果关闭预配过度，最大可为 40。

对于高出这些限制所允许的 VM，需要部署多个规模集，如[此模板](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale)所示。


