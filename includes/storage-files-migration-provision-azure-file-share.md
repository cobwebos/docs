---
title: 预配 Azure 文件共享的注意事项。
description: 预配 Azure 文件共享，以便与 Azure 文件同步一起使用。在迁移文档之间共享的通用文本块。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 8cb398d1b1ec14f52d9c5fa5c122dc2e4ba4376d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78209423"
---
Azure 文件共享存储在 Azure 存储帐户中的云中。
这里还有另一个级别的性能注意事项。

如果您有高度活跃的共享 - 许多用户和/或应用程序使用的共享，则两个 Azure 文件共享可能达到存储帐户的性能限制。

最佳做法是部署存储帐户，每个帐户都有一个文件共享。
您可以将多个 Azure 文件共享池到同一存储帐户中，以防共享具有存档共享，或者预期它们中的日常活动较低。

与应用于 Azure 文件同步相比，这些注意事项更多地应用于直接云访问（通过 Azure VM）。如果计划在这些共享上仅使用 Azure 文件同步，则将多个帐户分组到单个 Azure 存储帐户中可以。

如果您已列出共享，则应将每个共享映射到它们将驻留在的存储帐户。

在上一阶段，您已经确定了适当的股票数量。 在此步骤中，您创建了存储帐户到文件共享的映射。 部署现在适当数量的 Azure 存储帐户，其中具有适当数量的 Azure 文件共享。

确保每个存储帐户的区域相同，并匹配已部署的存储同步服务资源的区域。

> [!CAUTION]
> 如果创建 100 TiB 限制 Azure 文件共享，则该共享只能使用本地冗余存储或区域冗余存储冗余选项。 在使用 100 个 TiB 文件共享之前，请考虑存储冗余需求。

默认情况下，Azure 文件共享仍以 5 TiB 限制创建。 由于您要创建新的存储帐户，请确保按照[该指南创建允许 Azure 文件共享的存储帐户，限制为 100 TiB。](../articles/storage/files/storage-files-how-to-create-large-file-share.md)

部署存储帐户时的另一个考虑因素是 Azure 存储的冗余。 请参阅[：Azure 存储冗余选项](../articles/storage/common/storage-redundancy.md)。

资源的名称也很重要。 例如，如果将 HR 部门的多个共享分组到 Azure 存储帐户中，则应相应地命名存储帐户。 同样，在命名 Azure 文件共享时，应使用与本地对应共享类似的名称。