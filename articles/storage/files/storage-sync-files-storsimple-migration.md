---
title: 从 StorSimple 迁移到 Azure 文件同步
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: fauhse
ms.subservice: files
description: StorSimple 是一种生存期产品，Azure 文件同步是迁移到的解决方案。 了解迁移概念并了解自定义迁移帮助 AzureFiles@microsoft.com。
ms.openlocfilehash: 1cc88080522a62085d9a515223512ef25c20a9e4
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895081"
---
# <a name="storsimple-migration-to-azure-file-sync"></a>StorSimple 到 Azure 文件同步的迁移

StorSimple 是一种不再使用的 Microsoft 产品。 此产品及其云服务的扩展支持将持续到2022年底。
应立即开始规划从 StorSimple 开始迁移，这一点很重要。

默认和战略性长期 Azure service StorSimple 设备可以迁移到，Azure 文件同步。它是一项公开上市的 Azure 服务，具有基于 StorSimple 的一组功能。

## <a name="full-cloud-side-migration-with-limited-downtime"></a>具有有限停机时间的完整云端迁移
本文介绍迁移的开始方式的概念。
需要注意的是，需要从 StorSimple 迁移到 Azure 文件同步的客户无需自行进行迁移。

> [!IMPORTANT]
> Microsoft 致力于帮助客户完成其迁移。 电子邮件 AzureFiles@microsoft .com 用于自定义的迁移计划，并在迁移过程中提供帮助。

## <a name="migration-approach"></a>迁移方法
迁移到 Azure 文件同步将以对本地和有限停机时间的最小影响来开始云端。
以下概念面向 StorSimple 8000 系列设备。
如果需要从 StorSimple 7000 系列迁移，则第一步需要从 Microsoft 免费升级到匹配的8000系列 loaner 设备。
与 AzureFiles@microsoft.com 联系，我们将帮助你组织适当数量的 loaner 设备。

### <a name="general-approach"></a>常规方法
![Alt](media/storage-sync-files-storsimple-migration/storsimple-docs-overview-concept.png "通过临时虚拟设备和 Windows Server 向新的本地 Windows Server 替换本地 StorSimple 设备，阐释云端迁移")

1. 获取本地 StorSimple 设备的卷克隆，并将其装载到临时 StorSimple 虚拟设备。
2. 通过 iSCSI 将虚拟设备连接到临时 Azure VM。
3. 在临时 Windows Server VM 上安装 Azure 文件同步-在服务器上配置同步之前，还需要设置此迁移的特定注册表项。
    * 根据 StorSimple 卷上共享的数量，部署的 Azure 文件共享就会多了。 （我们建议为每个存储帐户部署一个 Azure 文件共享。）
    * 在 Windows Server cloud VM 和 Azure 文件共享上配置单个共享之间的同步。 （1:1 映射）
    * （可选）添加本地服务器作为本地性能缓存，启用云分层。 如果你希望将本地 StorSimple 替换为功能丰富的本地缓存（由 Windows Server 和 Azure 文件同步的云分层提供支持），则此步骤是必需的。 本地 Windows Server 可以是物理计算机、群集或虚拟机。 它无需将太多存储部署为数据集大小。 它只需要足够的存储空间来本地缓存最常访问的文件。

## <a name="minimizing-downtime"></a>最小化停机时间
在上述第3步之后，用户和应用程序仍在使用 StorSimple 本地设备。 因此，在同步完成时，从初始卷克隆同步的文件集会略微过时。
最大限度地减少停机时间的方法是从卷克隆过程中重复同步，这样，每次迭代都可以更快、更快速地完成同步，因为卷克隆之间的更改会变少且更少。
您可以重复此过程，直到卷克隆的同步在可接受的停机时间内完成。
出现这种情况后，阻止用户和应用程序对 StorSimple 设备进行任何更改。 停机时间开始。
获取另一个卷克隆，并让它同步到连接的服务器。
将用户和应用程序的访问权限建立到你的新的、Azure 文件同步的支持 Windows Server。
请考虑部署/调整 DFS 命名空间，使其从旧的 StorSimple 设备转移到新的 Windows Server 对应用和用户透明。
迁移已完成。

## <a name="migration-goal"></a>迁移目标
迁移完成后，可以取消预配临时 StorSimple 虚拟设备和 Azure VM。

此外，还可以取消预配 StorSimple 本地设备，因为你的用户和应用程序已访问 Windows Server。
下图中描述了您所遗留的内容。 标准 Azure 文件同步部署通过 Azure 文件同步，为多个 Azure 文件共享和 Windows Server 连接到它们。请记住，单台服务器可以同时将不同的本地文件夹连接到不同的文件共享。
此外，一个 Azure 文件共享可以同步到多个不同的服务器，在这种情况下，你需要在分支机构中缓存数据。 还要检查是否可以优化云分层策略，以更有效地利用本地存储空间。

![Alt](media/storage-sync-files-storsimple-migration/storsimple-docs-goal.PNG "显示迁移完成后的目标的插图。它描述了将多个文件共享同步到本地 Windows Server，以及访问云中或 Windows Server 上的文件的用户和应用程序。")

## <a name="next-steps"></a>后续步骤
熟悉 Azure 文件和 Azure 文件同步。务必了解成功迁移的 Azure 文件同步术语和部署模式。 本概述文章中的每个步骤都提供了更多详细信息。 在规划和执行迁移过程中，请务必联系 Microsoft 获取自定义帮助。

> [!IMPORTANT]
> Microsoft 致力于帮助客户完成其迁移。 电子邮件 AzureFiles@microsoft .com 用于自定义的迁移计划，并在迁移过程中提供帮助。

## <a name="additional-resources"></a>其他资源
如果你不熟悉 Azure 文件同步，作为目标服务，Azure 文件同步会提供两个基本文档。
* [Azure 文件同步-概述](storage-sync-files-planning.md)
* [Azure 文件同步-部署指南](storage-sync-files-deployment-guide.md)

Azure 文件是 Azure 中的一项存储服务，它将文件共享作为一项服务提供。 无需为 VM 或关联的 VM 存储付费。
* [Azure 文件-概述](storage-files-introduction.md)
* [Azure 文件-如何部署 Azure 文件共享](storage-how-to-create-file-share.md)