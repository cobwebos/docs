---
title: 预配 Azure 文件共享的注意事项。
description: 预配用于 Azure 文件同步的 Azure 文件共享。在迁移文档之间共享的通用文本块。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 8cb398d1b1ec14f52d9c5fa5c122dc2e4ba4376d
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209423"
---
Azure 文件共享存储在云中的 Azure 存储帐户中。
这里有另一个性能注意事项级别。

如果你有多个用户和/或应用程序使用的活动非常多的共享，则两个 Azure 文件共享可能会达到存储帐户的性能限制。

最佳做法是部署具有一个文件共享的存储帐户。
可以将多个 Azure 文件共享共用到相同的存储帐户，以防有存档共享或预计其中的日常活动较低。

这些注意事项更适用于直接与应用于 Azure 文件同步的云访问权限（通过 Azure VM）。如果你计划仅在这些共享上使用 Azure 文件同步，则可以将多个组分组到单个 Azure 存储帐户中。

如果已创建共享列表，则应将每个共享映射到其所在的存储帐户。

在上一阶段中，您已确定了合适的共享数目。 在此步骤中，您已创建了存储帐户到文件共享的映射。 立即部署适当数量的 azure 存储帐户，其中包含合适数量的 Azure 文件共享。

请确保每个存储帐户的区域相同，并且与已部署的存储同步服务资源的区域相匹配。

> [!CAUTION]
> 如果创建 100 TiB 限制 Azure 文件共享，该共享只能使用本地冗余存储或区域冗余存储冗余选项。 使用 100 TiB 文件共享之前，请考虑你的存储冗余需求。

默认情况下，默认情况下，将创建具有 5 TiB 限制的 Azure 文件共享。 由于你要创建新的存储帐户，因此请确保遵循相关[指南来创建允许具有 100 TiB 限制的 Azure 文件共享的存储帐户](../articles/storage/files/storage-files-how-to-create-large-file-share.md)。

部署存储帐户时的另一个注意事项是 Azure 存储的冗余。 请参阅： [Azure 存储冗余选项](../articles/storage/common/storage-redundancy.md)。

资源的名称也很重要。 例如，如果将 HR 部门的多个共享分组到一个 Azure 存储帐户中，则应适当地命名存储帐户。 同样，命名 Azure 文件共享时，应使用与用于本地对应项的名称类似的名称。