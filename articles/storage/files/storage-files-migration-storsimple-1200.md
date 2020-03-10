---
title: StorSimple 1200 到 Azure 文件同步的迁移
description: 了解如何将 StorSimple 1200 系列虚拟设备迁移到 Azure 文件同步。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 6863e7f8ef8e2f263cda824fd13186dc7b035454
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943616"
---
# <a name="storsimple-1200-migration-to-azure-file-sync"></a>StorSimple 1200 到 Azure 文件同步的迁移

StorSimple 1200 系列是在本地数据中心运行的虚拟设备。 可以将此设备中的数据迁移到 Azure 文件同步的环境。 Azure 文件同步是 StorSimple 设备可以迁移到的默认和策略长期 Azure 服务。

StorSimple 1200 系列[将在 12](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%201200%20Series)月2022日到期。  请务必尽快开始规划迁移。 本文提供成功迁移到 Azure 文件同步所需的背景知识和迁移步骤。 

## <a name="azure-file-sync"></a>Azure 文件同步

> [!IMPORTANT]
> Microsoft 致力于帮助客户完成其迁移。 电子邮件 AzureFilesMigration@microsoft .com 用于自定义的迁移计划，并在迁移过程中提供帮助。

Azure 文件同步是一种 Microsoft 云服务，基于两个主要组件：

* 文件同步和云分层。
* 文件共享是 Azure 中的本机存储，可通过多个协议（如 SMB 和文件 REST）进行访问。 Azure 文件共享相当于 Windows Server 上的文件共享，你可以将该文件共享作为网络驱动器进行本机装入。 它支持重要的文件保真方面，如属性、权限和时间戳。 与 StorSimple 不同，无需应用程序/服务来解释存储在云中的文件和文件夹。 在云中存储常规用途文件服务器数据以及某些应用程序数据的理想且最灵活的方法。

本文重点介绍迁移步骤。 如果要在迁移之前详细了解 Azure 文件同步，我们建议阅读以下文章：

* [Azure 文件同步-概述](https://aka.ms/AFS "概述")
* [Azure 文件同步-部署指南](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>迁移目标

目标是保证生产数据的完整性并保证可用性。 后者需要将停机时间保持在最少，使其适应或仅略超过定期维护时段。

## <a name="storsimple-1200-migration-path-to-azure-file-sync"></a>StorSimple 1200 到 Azure 文件同步的迁移路径

运行 Azure 文件同步代理需要本地 Windows Server。 Windows Server 可以是至少一个2012R2 服务器，但理想情况下为 Windows Server 2019。

有大量的备用迁移路径，并且它将创建的项目太长，可以记录所有这些路径，并说明它们在路由上出现风险或缺点的原因，我们建议将其作为本文的最佳实践。

:::image type="content" source="media/storage-files-migration-storsimple-shared/storsimple-1200-migration-overview.png" alt-text="迁移路由概述本文后面的步骤。":::

上图描述了对应于本文各部分的步骤。

### <a name="step-1-provision-your-on-premises-windows-server-and-storage"></a>步骤1：预配本地 Windows Server 和存储

1. 创建 Windows Server 2019-以最小2012R2 为虚拟机或物理服务器。 还支持 Windows Server 故障转移群集。
2. 预配或添加直接附加存储（与 NAS 相比，不支持 NAS。 Windows Server 存储的大小必须等于或大于你的虚拟 StorSimple 1200 设备的可用容量。

### <a name="step-2-configure-your-windows-server-storage"></a>步骤2：配置 Windows Server 存储

在此步骤中，将 StorSimple 存储结构（卷和共享）映射到 Windows Server 存储结构。
如果你计划对存储结构进行更改（即卷的数量、数据文件夹与卷的关联）或当前 SMB/NFS 共享之上或之下的子文件夹结构，那么现在就是考虑这些更改的时间。
在配置 Azure 文件同步后更改文件和文件夹结构非常麻烦，应避免这样做。
本文假设您映射的是1:1，因此在按照本文中的步骤操作时，您必须考虑您的映射更改。

* 在 Windows Server 系统卷上，不会有任何生产数据最终。 系统卷上不支持云分层。 但是，迁移需要使用此功能，同时也需要使用 StorSimple 替换操作。
* 在 Windows Server 上预配与 StorSimple 1200 虚拟设备上相同数量的卷。
* 配置所需的任何 Windows Server 角色、功能和设置。 建议你选择加入 Windows Server 更新以使你的操作系统保持安全和最新。 同样，我们建议选择 Microsoft 更新以使 Microsoft 应用程序保持最新状态，包括 Azure 文件同步代理。
* 阅读以下步骤之前，请不要配置任何文件夹或共享。

### <a name="step-3-deploy-the-first-azure-file-sync-cloud-resource"></a>步骤3：部署第一个 Azure 文件同步云资源

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

### <a name="step-4-match-your-local-volume-and-folder-structure-to-azure-file-sync-and-azure-file-share-resources"></a>步骤4：将本地卷和文件夹结构与 Azure 文件同步和 Azure 文件共享资源匹配

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="step-5-provision-azure-file-shares"></a>步骤5：预配 Azure 文件共享

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

### <a name="step-6-configure-windows-server-target-folders"></a>步骤6：配置 Windows Server 目标文件夹

在前面的步骤中，您已考虑了将确定同步拓扑组件的所有方面。 现在，准备好服务器来接收要上载的文件。

创建**所有**文件夹，将每个文件夹同步到其自己的 Azure 文件共享。
务必遵循前面所述的文件夹结构。 例如，如果决定将多个本地 SMB 共享一起同步到单个 Azure 文件共享中，则需要将其放在卷上的通用根文件夹下。 立即在卷上创建此目标根文件夹。

你预配的 Azure 文件共享的数量应该与你在此步骤中创建的文件夹数以及要在根级别同步的卷的数目匹配。

### <a name="step-7-deploy-the-azure-file-sync-agent"></a>步骤7：部署 Azure 文件同步代理

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="step-8-configure-sync"></a>步骤8：配置同步

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **确保启用云分层！** 如果本地服务器没有足够的空间来存储 StorSimple 云存储中的数据的总大小，则这是必需的。 将你的分层策略设置为临时迁移到99% 卷可用空间。

为需要配置为进行同步的所有 Azure 文件共享/服务器位置重复创建同步组并添加匹配的服务器文件夹作为服务器终结点的步骤。

### <a name="step-9-copy-your-files"></a>步骤9：复制文件

基本迁移方法是从 StorSimple 虚拟设备到 Windows Server 的 RoboCopy，并 Azure 文件同步到 Azure 文件共享。

运行 Windows Server 目标文件夹的第一个本地副本：

* 确定虚拟 StorSimple 设备上的第一个位置。
* 标识已在 Windows Server 上配置了 Azure 文件同步的匹配文件夹。
* 使用 RoboCopy 开始复制

以下 RoboCopy 命令将从 StorSimple Azure 存储中回调文件到本地 StorSimple，然后将其移到 Windows Server 目标文件夹。 Windows Server 会将其同步到 Azure 文件共享。 当本地 Windows Server 卷已满时，云分层将开始，并且已成功同步的层文件已成功同步。 云分层将生成足够的空间，以便从 StorSimple 虚拟设备继续复制。 云分层检查一小时以查看已同步的内容，并释放磁盘空间以达到99% 的可用空间。

```console
Robocopy /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

背景色：

:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      允许按顺序在同一目标/目标上多次运行此 RoboCopy 命令。 它标识在之前已复制的内容并将其省略。 仅会处理自上次运行后发生的更改、添加和*删除*操作。 如果命令之前未运行，则不会省略任何内容。 对于仍在使用和变化的源位置，这是一个很好的选择。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY： copyflag [s]
   :::column-end:::
   :::column span="1":::
      文件副本的保真度（默认值为/COPY： DAT）、复制标志： D = 数据、A = 属性、T = 时间戳、S = 安全 = NTFS Acl、O = 所有者信息、U = 审核信息
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL
   :::column-end:::
   :::column span="1":::
      复制所有文件信息（等同于/COPY： DATSOU）
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY： copyflag [s]
   :::column-end:::
   :::column span="1":::
      目录副本的保真度（默认值为/DCOPY： DA）、复制标志： D = 数据、A = 属性、T = 时间戳
   :::column-end:::
:::row-end:::

首次运行 RoboCopy 命令时，您的用户和应用程序仍将访问 StorSimple 文件和文件夹，并可能会更改。 这是可能的，RoboCopy 处理了一个目录，转到下一个目录，然后在源位置（StorSimple）上的用户添加、更改或删除将不会在当前的 RoboCopy 运行中处理的文件。 这是正常的。

第一次运行是将大量数据移回本地，通过 Azure 文件同步将数据移回 Windows Server 并备份到云中。这可能需要很长时间，具体取决于：

* 下载带宽
* StorSimple 云服务的撤回速度
* 上传带宽
* 需要由任一服务处理的项目（文件和文件夹）的数量

初始运行完成后，再次运行该命令。

第二次它将更快完成，因为它只需要传输自上次运行后发生的更改。 这些更改可能是 StorSimple 的本地更改，因为它们是最新的。 这会进一步缩短时间，因为降低了从云回调的需求。 在第二次运行期间，新更改可能会累积。

重复此过程，直到你满足完成所需的时间，这是一个可接受的停机时间。

当你考虑到可接受的停机时间，并且你已准备好使 StorSimple 位置脱机时，请立即执行此操作：例如，删除 SMB 共享，以便没有用户可以访问该文件夹或采取任何其他适当的步骤来防止内容在此文件夹。

运行最后一个 RoboCopy 轮。 这会选取任何更改，这些更改可能已丢失。
这最后一步所花费的时间取决于 RoboCopy 扫描的速度。 您可以通过测量上一次运行所用的时间来估计时间（这等于您的停机时间）。

在 Windows Server 文件夹上创建一个共享，并且可能会调整你的 DFS N 部署以指向该文件夹。 请确保设置与 StorSimple SMB 共享上相同的共享级别权限。

你已完成将共享/共享组迁移到公共根或卷中。 （具体取决于所映射的内容并决定需要进入同一个 Azure 文件共享。）

可以尝试并行运行其中几个副本。 建议一次处理一个 Azure 文件共享的作用域。

> [!WARNING]
> 将所有数据从 StorSimple 移到 Windows Server 并完成迁移后：返回到 Azure 门户中的***所有***同步组，并将 "云分层卷可用空间百分比" 值调整为更适合缓存利用率的内容，例如20%。 

云分层卷可用空间策略作用于可能有多个服务器终结点从中同步的卷级别。 如果忘记调整甚至一个服务器终结点上的可用空间，同步将继续应用最严格的规则，并尝试保留99% 的可用磁盘空间，从而使本地缓存不会像预期的那样运行。 除非您的目标只包含一个只包含很少访问的存档数据的卷的命名空间。

## <a name="troubleshoot"></a>故障排除

最可能遇到的问题是 RoboCopy 命令在 Windows Server 端上失败并出现 *"卷已满"* 。 如果是这样，下载速度可能比上传速度更好。 云分层每小时进行一次，用于从已同步的本地 Windows Server 磁盘撤走内容。

让同步进度和云分层释放磁盘空间。 您可以在 Windows Server 上的文件资源管理器中观察。

当你的 Windows 服务器有足够的可用容量时，重新运行该命令将解决此问题。 如果遇到这种情况，您可以放心地继续操作。 再次运行该命令是一个后果。

还可以遇到其他 Azure 文件同步问题。
如果出现这种情况，请查看**Azure 文件同步故障排除指南的链接**。

## <a name="relevant-links"></a>相关链接

迁移内容：

* [StorSimple 8000 系列迁移指南](storage-files-migration-storsimple-8000.md)

Azure 文件同步内容：

* [AFS 概述](https://aka.ms/AFS)
* [AFS 部署指南](storage-files-deployment-guide.md)
* [AFS 故障排除](storage-sync-files-troubleshoot.md)
