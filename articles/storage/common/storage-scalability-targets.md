---
title: Azure 存储可伸缩性和性能目标 - 存储帐户
description: 了解 Azure 存储帐户的可伸缩性和性能目标，包括容量、请求速率以及入站和出站带宽。
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: 932d250d6685a1b905e4a03a0118d8c8f1f26418
ms.sourcegitcommit: 6e6813f8e5fa1f6f4661a640a49dc4c864f8a6cb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2019
ms.locfileid: "67151246"
---
# <a name="azure-storage-scalability-and-performance-targets-for-storage-accounts"></a>存储帐户的 Azure 存储可伸缩性和性能目标

本文详细介绍了 Azure 存储帐户的可伸缩性和性能目标。 以下所列的可伸缩性和性能目标为高端目标，但却是能够实现的。 在任何情况下，存储帐户实现的请求速率和带宽取决于存储对象大小、使用的访问模式、应用程序执行的工作负荷类型。

请务必测试服务，以确定其性能是否达到要求。 如果可能，应避免流量速率突发峰值，并确保流量在各个分区上均匀分布。

当应用程序达到分区能够处理的工作负荷极限时，Azure 存储将开始返回错误代码 503（服务器忙）或错误代码 500（操作超时）响应。 如果发生 503 错误，请考虑修改应用程序以使用指数退避策略进行重试。 使用指数退让策略，可以减少分区上的负载，缓解该分区的流量高峰。

## <a name="storage-account-scale-limits"></a>存储帐户规模限制

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

## <a name="premium-performance-storage-account-scale-limits"></a>高级性能存储帐户缩放限制

[!INCLUDE [azure-premium-limits](../../../includes/azure-storage-limits-premium.md)]

## <a name="storage-resource-provider-scale-limits"></a>存储资源提供程序缩放限制

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Azure Blob 存储缩放目标

[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Azure 文件规模目标

有关 Azure 文件和 Azure 文件同步的缩放和性能目标的详细信息，请参阅 [Azure 文件可伸缩性和性能目标](../files/storage-files-scale-targets.md)。

> [!IMPORTANT]
> 存储帐户限制适用于所有共享。 最多缩放的存储帐户的最大值才可实现，如果只有一个共享每个存储帐户。
>
> 大于 5 TiB 的标准文件共享处于预览状态，存在一定的限制。
> 有关列表的限制和要加入到的这些更大的文件共享大小的预览，请参阅[标准文件共享](../files/storage-files-planning.md#standard-file-shares)部分 Azure 文件规划的指导。

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="premium-files-scale-targets"></a>高级文件缩放目标

有三个类别的要考虑的高级文件的限制： 存储帐户、 共享和文件。

例如：单个共享可以实现 100,000 IOPS 和单个文件可以扩展最多 5,000 个 IOPS。 因此，例如，如果您有一个共享中的三个文件，就可以从该共享的最大 IOPs 是 15000。

#### <a name="premium-file-share-limits"></a>高级文件共享限制

[!INCLUDE [storage-files-premium-scale-targets](../../../includes/storage-files-premium-scale-targets.md)]

### <a name="azure-file-sync-scale-targets"></a>Azure 文件同步规模目标

Azure 文件同步的设计目标是无限使用，但并非总是可以无限使用。 下表指示了 Microsoft 测试的边界，还指出了哪些目标是硬限制：

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="azure-queue-storage-scale-targets"></a>Azure 队列存储缩放目标

[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

## <a name="azure-table-storage-scale-targets"></a>Azure 表存储缩放目标

[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="see-also"></a>另请参阅

- [存储定价详细信息](https://azure.microsoft.com/pricing/details/storage/)
- [Azure 订阅和服务限制、配额和约束](../../azure-subscription-service-limits.md)
- [Azure 存储复制](../storage-redundancy.md)
- [Microsoft Azure 存储性能和可伸缩性清单](../storage-performance-checklist.md)