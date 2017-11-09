---
title: "Azure 存储可伸缩性和性能目标 | Microsoft Docs"
description: "了解有关 Azure 存储帐户的可伸缩性和性能目标的信息，包括标准和高级存储账户的容量、请求速率以及入站和出站带宽。 了解每个 Azure 存储服务中各分区的性能目标。"
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: be721bd3-159f-40a1-88c1-96418537fe75
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 07/12/2017
ms.author: tamram
ms.openlocfilehash: 805b0eee46846345ee1f33faf0c28393c3e8ebb1
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/02/2017
---
# <a name="azure-storage-scalability-and-performance-targets"></a>Azure 存储可伸缩性和性能目标
## <a name="overview"></a>概述
本主题介绍 Microsoft Azure 存储的可伸缩性和性能主题。 有关其他 Azure 限制的摘要，请参阅 [Azure 订阅和服务限制、配额与约束](../../azure-subscription-service-limits.md)。

> [!NOTE]
> 所有存储帐户都在新的扁平网络拓扑上运行，无论它们创建于何时，都支持下文概述的可伸缩性和性能目标。 有关 Azure 存储的扁平网络体系结构和可伸缩性的详细信息，请参阅 [Microsoft Azure Storage: A Highly Available Cloud Storage Service with Strong Consistency](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)（Microsoft Azure 存储：具有高度一致性的高可用云存储服务）。
> 
> [!IMPORTANT]
> 以下所列的可伸缩性和性能目标为高端目标，但却是能够实现的。 在任何情况下，存储帐户实现的请求速率和带宽取决于存储对象大小、使用的访问模式、应用程序执行的工作负荷类型。 请务必测试服务，以确定其性能是否达到要求。 如果可能，应避免流量速率突发峰值，并确保流量在各个分区上均匀分布。
> 
> 当应用程序达到分区能够处理的工作负荷极限时，Azure 存储将开始返回错误代码 503（服务器忙）或错误代码 500（操作超时）响应。 发生这种情况时，应用程序应使用指数退让策略进行重试。 使用指数退让策略，可以减少分区上的负载，缓解该分区的流量高峰。
> 
> 

如果应用程序的需求超过单个存储帐户的伸缩性目标，则在构建时让应用程序使用多个存储帐户，并将数据对象分布到这些存储帐户中。 有关批量定价的信息，请参阅 [Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/) 。

## <a name="scalability-targets-for-a-storage-account"></a>存储帐户的可伸缩性目标
[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Azure Blob 存储缩放目标
[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Azure 文件缩放目标
有关 Azure 文件和 Azure 文件同步的缩放和性能目标的详细信息，请参阅 [Azure 文件可伸缩性和性能目标](../files/storage-files-scale-targets.md)。

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="azure-file-sync-scale-targets"></a>Azure 文件同步缩放目标
[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="azure-queue-storage-scale-targets"></a>Azure 队列存储缩放目标
[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

## <a name="azure-table-storage-scale-targets"></a>Azure 表存储缩放目标
[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
## <a name="scalability-targets-for-virtual-machine-disks"></a>虚拟机磁盘的可伸缩性目标
[!INCLUDE [azure-storage-limits-vm-disks](../../../includes/azure-storage-limits-vm-disks.md)]

请参阅 [Windows VM 大小](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)或 [Linux VM 大小](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)了解其他详细信息。

## <a name="managed-virtual-machine-disks"></a>托管虚拟机磁盘

[!INCLUDE [azure-storage-limits-vm-disks-managed](../../../includes/azure-storage-limits-vm-disks-managed.md)]

## <a name="unmanaged-virtual-machine-disks"></a>非托管虚拟机磁盘
[!INCLUDE [azure-storage-limits-vm-disks-standard](../../../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../../../includes/azure-storage-limits-vm-disks-premium.md)]

## <a name="see-also"></a>另请参阅
* [存储定价详细信息](https://azure.microsoft.com/pricing/details/storage/)
* [Azure 订阅和服务限制、配额和约束](../../azure-subscription-service-limits.md)
* [高级存储：适用于 Azure 虚拟机工作负荷的高性能存储](../../virtual-machines/windows/premium-storage.md)
* [Azure 存储复制](../storage-redundancy.md)
* [Microsoft Azure 存储性能和可伸缩性清单](../storage-performance-checklist.md)
* [Microsoft Azure 存储：具有高度一致性的高可用云存储服务](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

