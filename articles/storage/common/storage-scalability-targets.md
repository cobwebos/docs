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
ms.openlocfilehash: e3e0e9ae4a1939aad9ab2ae42a1b51b1b00e2462
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/12/2019
ms.locfileid: "59521710"
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

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="premium-files-scale-targets"></a>高级文件缩放目标

有三个类别的要考虑的高级文件的限制： 存储帐户、 共享和文件。

例如：单个共享可以实现 100,000 IOPS 和单个文件可以扩展最多 5,000 个 IOPS。 因此，例如，如果您有一个共享中的三个文件，就可以从该共享的最大 IOPs 是 15000。

#### <a name="premium-file-share-limits"></a>高级文件共享限制

> [!IMPORTANT]
> 存储帐户限制适用于所有共享。 最多缩放的存储帐户的最大值才可实现，如果只有一个共享每个存储帐户。

|区域  |确定目标  |
|---------|---------|
|预配的最小大小                        |100 GiB      |
|最大预配大小                        |100 TiB      |
|最小大小增大/减少    |1 GiB      |
|基线 IOPS    |每 GiB 多达 100,000 个 1 IOPS|
|爆发的 IOPS    |3 个 x IOPS 每 GiB 多达 100,000 个|
|出口率         |60 MiB/秒 + 0.06 * 预配 GiB        |
|入口速率| 40 MiB/秒 + 0.04 * 预配 GiB |
|最大快照数        |200       |

#### <a name="premium-file-limits"></a>高级文件限制

|区域  |确定目标  |
|---------|---------|
|大小                  |1 TiB         |
|每个文件的最大 IOPS     |5,000         |
|并发句柄    |2,000         |

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