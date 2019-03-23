---
title: Azure 存储可伸缩性和性能目标的存储帐户
description: 了解有关可伸缩性和性能目标，包括容量、 请求速率以及入站和出站带宽，Azure 存储帐户的信息。
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: ca9c4c959d21f26369600129f3897b7624dd84f2
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2019
ms.locfileid: "58371167"
---
# <a name="azure-storage-scalability-and-performance-targets-for-storage-accounts"></a>存储帐户的 azure 存储可伸缩性和性能目标

本文详细介绍了 Azure 存储帐户的可伸缩性和性能目标。 以下所列的可伸缩性和性能目标为高端目标，但却是能够实现的。 在任何情况下，存储帐户实现的请求速率和带宽取决于存储对象大小、使用的访问模式、应用程序执行的工作负荷类型。

请务必测试服务，以确定其性能是否达到要求。 如果可能，应避免流量速率突发峰值，并确保流量在各个分区上均匀分布。

当应用程序达到分区能够处理的工作负荷极限时，Azure 存储将开始返回错误代码 503（服务器忙）或错误代码 500（操作超时）响应。 如果发生 503 错误，请考虑修改应用程序以使用指数退避策略进行重试。 使用指数退让策略，可以减少分区上的负载，缓解该分区的流量高峰。

## <a name="standard-performance-storage-account-scale-limits"></a>标准性能存储帐户规模限制

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

## <a name="premium-performance-storage-account-scale-limits"></a>高级性能存储帐户规模限制

[!INCLUDE [azure-premium-limits](../../../includes/azure-storage-limits-premium.md)]

## <a name="storage-resource-provider-scale-limits"></a>存储资源提供程序缩放限制

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Azure Blob 存储缩放目标

[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Azure 文件规模目标

有关 Azure 文件和 Azure 文件同步的缩放和性能目标的详细信息，请参阅 [Azure 文件可伸缩性和性能目标](../files/storage-files-scale-targets.md)。

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

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