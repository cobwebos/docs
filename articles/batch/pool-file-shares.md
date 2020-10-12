---
title: Azure Batch 池的 Azure 文件共享
description: 如何在 Azure Batch 中从 Linux 或 Windows 池中的计算节点装载 Azure 文件共享。
ms.topic: how-to
ms.date: 05/24/2018
ms.openlocfilehash: 6bbaba20512a17de563e74ba095057c5c3f71f6b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87986417"
---
# <a name="use-an-azure-file-share-with-a-batch-pool"></a>将 Batch 池与 Azure 文件共享配合使用

[Azure 文件](../storage/files/storage-files-introduction.md)在云中提供完全托管的文件共享，这些共享项可通过服务器消息块 (SMB) 协议进行访问。 本文提供有关在池计算节点上装载和使用 Azure 文件共享的信息与代码示例。

## <a name="considerations-for-use-with-batch"></a>与 Batch 配合使用时的注意事项

* 在使用非高级 Azure 文件存储的情况下，如果池中运行的并行任务数相对较少，请考虑使用 Azure 文件共享。 请查看[性能和可伸缩性目标](../storage/files/storage-files-scale-targets.md)，确定在指定了预期的池大小和资产文件数的情况下，是否应使用 Azure 文件（Azure 文件使用 Azure 存储帐户）。 

* Azure 文件共享非常[经济高效](https://azure.microsoft.com/pricing/details/storage/files/)，可以使用数据复制将其配置到另一个区域，以实现全局冗余。 

* 可从本地计算机并行装载 Azure 文件共享。 但是，请务必了解[并发性影响](../storage/common/storage-concurrency.md)，尤其是在使用 REST API 的情况下。

* 另请参阅 Azure 文件共享的一般[规划注意事项](../storage/files/storage-files-planning.md)。


## <a name="create-a-file-share"></a>创建文件共享

在已链接到 Batch 帐户的存储帐户中，或者在单独的存储帐户中[创建文件共享](../storage/files/storage-how-to-create-file-share.md)。

## <a name="mount-an-azure-file-share-on-a-batch-pool"></a>在 Batch 池中装载 Azure 文件共享

请参阅相关文档，了解如何[在 Batch 池中装载虚拟文件系统](virtual-file-mount.md)。

## <a name="next-steps"></a>后续步骤

* 有关 Batch 中用于读取和写入数据的其他选项，请参阅[保存作业和任务的输出](batch-task-output.md)。
* 另请参阅 [Batch Shipyard](https://github.com/Azure/batch-shipyard) 工具包，其中包含用于部署 Batch 容器工作负荷文件系统的 [Shipyard 脚本](https://github.com/Azure/batch-shipyard/tree/master/recipes)。
