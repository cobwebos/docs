---
title: "Azure 文件可伸缩性和性能目标 | Microsoft Docs"
description: "了解 Azure 文件的可伸缩性和性能目标信息，包括容量、请求速率以及入站和出站带宽限制。"
services: storage
documentationcenter: na
author: wmgries
manager: klaasl
editor: tamram
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 12/04/2017
ms.author: wgries
ms.openlocfilehash: 381e96a0a777415b916e4093fe55aa0d355782a1
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2017
---
# <a name="azure-files-scalability-and-performance-targets"></a>Azure 文件可伸缩性和性能目标
[Azure 文件](storage-files-introduction.md)在云中提供完全托管的文件共享，这些共享项可通过行业标准 SMB 协议进行访问。 本文讨论了 Azure 文件和 Azure 文件同步（预览版）的可伸缩性和性能目标。

此处列出的可伸缩性和性能目标是高端目标，但可能会受部署中的其他变量影响。 例如，除了受限于托管着 Azure 文件服务的服务器之外，针对文件的吞吐量还可能会受限于可变的网络带宽。 强烈建议你对使用模式进行测试，以确定 Azure 文件的可伸缩性和性能是否满足你的要求。 随着时间的推移，我们也一直在努力提高这些限制。 如果希望我们提高某些限制，请尽管通过下面的评论或者通过 [Azure 文件 UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) 向我们提供反馈。

## <a name="azure-storage-account-scale-targets"></a>Azure 存储帐户规模目标
Azure 文件共享的父资源是 Azure 存储帐户。 存储帐户表示 Azure 中的一个存储池，该存储池可供包括 Azure 文件在内多个存储服务用来存储数据。 在存储帐户中存储数据的其他服务有 Azure Blob 存储、Azure 队列存储和 Azure 表存储。 以下目标适用于在存储帐户中存储数据的所有存储服务：

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> 其他存储服务对存储帐户的利用率会影响你的存储帐户中的 Azure 文件共享。 例如，如果由于 Azure Blob 存储而达到了最大存储帐户容量，则将无法在 Azure 文件共享上创建新文件，即使 Azure 文件共享低于最大共享大小。

## <a name="azure-files-scale-targets"></a>Azure 文件规模目标
[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

## <a name="azure-file-sync-scale-targets"></a>Azure 文件同步规模目标
对于 Azure 文件同步，在设计时我们已尽最大努力来实现不受限的使用，但这并非始终可能。 下表指明了我们的测试边界以及哪些目标实际上是硬性限制：

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="see-also"></a>另请参阅
- [规划 Azure 文件部署](storage-files-planning.md)
- [规划 Azure 文件同步部署](storage-sync-files-planning.md)
- [其他存储服务的可伸缩性和性能目标](../common/storage-scalability-targets.md)