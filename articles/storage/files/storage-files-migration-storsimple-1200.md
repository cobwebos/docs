---
title: StorSimple 1200 迁移到 Azure 文件同步
description: 了解如何将 StorSimple 1200 系列虚拟设备迁移到 Azure 文件同步。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 69225da1506ced879363b10b098d939df93cbfba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502372"
---
# <a name="storsimple-1200-migration-to-azure-file-sync"></a>StorSimple 1200 迁移到 Azure 文件同步

StorSimple 1200 系列是在本地数据中心运行的虚拟设备。 可以将数据从此设备迁移到 Azure 文件同步环境。 Azure 文件同步是 StorSimple 设备可以迁移到的默认且战略性的长期 Azure 服务。

StorSimple 1200 系列将于 2022 年 12 月实现[生命周期结束](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%201200%20Series)。  尽快开始规划迁移非常重要。 本文提供了成功迁移到 Azure 文件同步的必要背景知识和迁移步骤。 

## <a name="azure-file-sync"></a>Azure 文件同步

> [!IMPORTANT]
> Microsoft 致力于协助客户迁移。 Email AzureFilesMigration@microsoft .com，用于自定义迁移计划以及在迁移期间提供帮助。

Azure 文件同步是一种 Microsoft 云服务，基于两个主要组件：

* 文件同步和云分层。
* 文件共享作为 Azure 中的本机存储，可通过多个协议（如 SMB 和文件 REST）进行访问。 Azure 文件共享可与 Windows 服务器上的文件共享相媲美，您可以将其作为网络驱动器本机装载。 它支持重要的文件保真度方面，如属性、权限和时间戳。 与 StorSimple 不同，无需应用程序/服务即可解释存储在云中的文件和文件夹。 在云中存储通用文件服务器数据以及某些应用程序数据的理想且最灵活的方法。

本文重点介绍迁移步骤。 如果要在迁移之前了解有关 Azure 文件同步的更多内容，我们建议您提供以下文章：

* [Azure 文件同步 - 概述](https://aka.ms/AFS "概述")
* [Azure 文件同步 - 部署指南](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>迁移目标

目标是保证生产数据的完整性，并保证可用性。 后者要求将停机时间降至最低，以便它能够适应或仅略高于常规维护窗口。

## <a name="storsimple-1200-migration-path-to-azure-file-sync"></a>StorSimple 1200 迁移路径到 Azure 文件同步

运行 Azure 文件同步代理需要本地 Windows 服务器。 Windows 服务器可以至少位于 2012R2 服务器，但理想情况下是 Windows 服务器 2019。

有许多替代迁移路径，它会创建太长的文章来记录所有这些路径，并说明它们为什么在本文中作为最佳实践建议的路径上承担风险或缺点。

:::image type="content" source="media/storage-files-migration-storsimple-shared/storsimple-1200-migration-overview.png" alt-text="迁移路线概述本文中进一步的步骤。":::

上图描述了与本文中各节相对应的步骤。

### <a name="step-1-provision-your-on-premises-windows-server-and-storage"></a>第 1 步：预配本地 Windows 服务器和存储

1. 创建 Windows 服务器 2019 - 至少 2012R2 - 作为虚拟机或物理服务器。 还支持 Windows 服务器故障转移群集。
2. 预配或添加直接连接存储（与不支持 NAS 的 NAS 相比的 DAS）。 Windows Server 存储的大小必须等于或大于虚拟 StorSimple 1200 设备的可用容量大小。

### <a name="step-2-configure-your-windows-server-storage"></a>第 2 步：配置 Windows 服务器存储

在此步骤中，您将 StorSimple 存储结构（卷和共享）映射到 Windows Server 存储结构。
如果计划更改存储结构，即卷数、数据文件夹与卷的关联，或当前 SMB/NFS 共享上方或下方的子文件夹结构，则现在是考虑这些更改的时候了。
在配置 Azure 文件同步后更改文件和文件夹结构是繁琐的，应避免。
本文假定您正在映射 1：1，因此在按照本文中的步骤操作时，必须考虑映射更改。

* 任何生产数据都不应最终位于 Windows Server 系统卷上。 系统卷不支持云分层。 但是，迁移和连续操作作为 StorSimple 替换是必需的。
* 在 Windows 服务器上预配与 StorSimple 1200 虚拟设备上的卷数相同。
* 配置所需的任何 Windows 服务器角色、功能和设置。 我们建议您选择加入 Windows 服务器更新，以确保操作系统的安全和最新。 同样，我们建议选择加入 Microsoft 更新，使 Microsoft 应用程序保持最新，包括 Azure 文件同步代理。
* 在阅读以下步骤之前，不要配置任何文件夹或共享。

### <a name="step-3-deploy-the-first-azure-file-sync-cloud-resource"></a>第 3 步：部署第一个 Azure 文件同步云资源

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

### <a name="step-4-match-your-local-volume-and-folder-structure-to-azure-file-sync-and-azure-file-share-resources"></a>步骤 4：将本地卷和文件夹结构与 Azure 文件同步和 Azure 文件共享资源匹配

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="step-5-provision-azure-file-shares"></a>第 5 步：预配 Azure 文件共享

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

### <a name="step-6-configure-windows-server-target-folders"></a>步骤 6：配置 Windows 服务器目标文件夹

在前面的步骤中，您已经考虑了将确定同步拓扑组件的所有方面。 现在是时候准备服务器接收文件进行上传了。

创建**所有**文件夹，这些文件夹将每个文件夹同步到其自己的 Azure 文件共享。
请务必遵循前面记录的文件夹结构。 例如，如果您决定将多个本地 SMB 共享同步到单个 Azure 文件共享中，则需要将它们放在卷上的公用根文件夹下。 立即在卷上创建此目标根文件夹。

预配的 Azure 文件共享数应与在此步骤中创建的文件夹数匹配 - 将在根级别同步的卷数。

### <a name="step-7-deploy-the-azure-file-sync-agent"></a>第 7 步：部署 Azure 文件同步代理

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="step-8-configure-sync"></a>第 8 步：配置同步

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **请务必打开云分层！** 如果本地服务器没有足够的空间将数据的总大小存储在 StorSimple 云存储中，则需要这样做。 将分层策略（临时用于迁移）设置为 99% 的卷可用空间。

重复同步组创建和添加匹配服务器文件夹的步骤，作为需要配置用于同步的所有 Azure 文件共享/服务器位置的服务器终结点。

### <a name="step-9-copy-your-files"></a>第 9 步：复制文件

基本迁移方法是从 StorSimple 虚拟设备到 Windows 服务器的 RoboCopy，以及 Azure 文件同步到 Azure 文件共享。

将第一个本地副本运行到 Windows Server 目标文件夹：

* 确定虚拟 StorSimple 设备上的第一个位置。
* 标识 Windows 服务器上已配置 Azure 文件同步的匹配文件夹。
* 使用 RoboCopy 启动副本

以下 RoboCopy 命令将从 StorSimple Azure 存储中撤回到本地 StorSimple 文件，然后将这些文件移动到 Windows Server 目标文件夹。 Windows 服务器将将其同步到 Azure 文件共享。 当本地 Windows Server 卷已满时，云分层将启动，并且已成功同步的层文件将启动。 云分层将生成足够的空间来继续从 StorSimple 虚拟设备复制。 云分层每小时检查一次，查看已同步的内容，并释放磁盘空间以达到 99% 的卷可用空间。

```console
Robocopy /MT:32 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

背景色：

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      允许 RoboCopy 运行多线程。 默认值为 8，最大值为 128。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG：<file name>
   :::column-end:::
   :::column span="1":::
      将日志文件输出为 UNICODE（覆盖现有日志）。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      输出到控制台窗口。 与日志文件的输出一起使用。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      以备份应用程序将使用的相同模式运行 RoboCopy。 它允许 RoboCopy 移动当前用户无权访问的文件。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      允许在同一目标/目标上按顺序运行此 RoboCopy 命令几次。 它标识之前复制的内容，并省略它。 只会处理自上次运行以来发生的更改、添加和"*删除*"。 如果之前未运行该命令，则不会省略任何内容。 对于仍在积极使用和更改的源位置，这是一个极好的选项。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY：复制标志*
   :::column-end:::
   :::column span="1":::
      文件副本的保真度（默认为 /COPY：DAT），复制标志：D=数据、A=属性、T=时间戳、S=安全=NTFS ACL、O=所有者信息、U_auditing信息
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /复制
   :::column-end:::
   :::column span="1":::
      复制所有文件信息（等效于 /COPY：DATSOU）
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY：复制标志*
   :::column-end:::
   :::column span="1":::
      目录副本的保真度（默认值为 /DCOPY：DA），复制标志：D=数据、A=属性、T=时间戳
   :::column-end:::
:::row-end:::

首次运行 RoboCopy 命令时，用户和应用程序仍在访问 StorSimple 文件和文件夹，并可能更改它。 有可能，RoboCopy 已经处理了目录，移动到下一个目录，然后在源位置（StorSimple）上添加、更改或删除文件，而该文件现在在此当前 RoboCopy 运行中不会处理。 没关系

第一个运行是将大部分数据移回本地，转移到 Windows 服务器并通过 Azure 文件同步备份到云中。这可能需要很长时间，具体取决于：

* 您的下载带宽
* StorSimple 云服务的召回速度
* 上传带宽
* 需要由任一服务处理的项目（文件和文件夹）的数量

完成初始运行后，再次运行该命令。

第二次完成得更快，因为它只需要传输自上次运行以来发生的更改。 这些变化可能是 StorSimple 的本地更改，因为它们是最新的。 这进一步缩短了时间，因为从云中调用的需求减少了。 在第二次运行期间，仍然会累积新的更改。

重复此过程，直到您确信完成所需的时间是可接受的停机时间。

当您认为停机时间可以接受，并准备使 StorSimple 位置脱机时，请立即执行此操作：例如，删除 SMB 共享，以便任何用户都无法访问该文件夹或采取任何其他阻止内容在此中更改的相应步骤文件夹在Stor简单。

运行最后一个 RoboCopy 轮。 这将选取任何可能错过的更改。
这最后一步需要多长时间，取决于 RoboCopy 扫描的速度。 您可以通过测量上次运行所花的时间来估计时间（相当于停机时间）。

在 Windows Server 文件夹上创建共享，并可能调整 DFS-N 部署以指向该共享。 请务必设置与 StorSimple SMB 共享相同的共享级别权限。

您已完成将共享/共享组迁移到公共根或卷。 （取决于您映射的内容，并决定需要进入相同的 Azure 文件共享。

您可以尝试并行运行其中的几个副本。 我们建议一次处理一个 Azure 文件共享的范围。

> [!WARNING]
> 将所有数据从 StorSimple 移动到 Windows Server 后，迁移已完成：返回到 Azure 门户中的所有***同步组***，并将云分层卷可用空间百分比值调整为更适合缓存利用率的内容，例如 20%。 

云分层卷可用空间策略在卷级别上运行，其中可能有多个服务器终结点从该端点同步。 如果您忘记调整一个服务器终结点上的可用空间，同步将继续应用最严格的规则，并尝试保留 99% 的可用磁盘空间，从而使本地缓存无法按预期执行。 除非您的目标是仅具有仅包含很少访问的存档数据的卷的命名空间。

## <a name="troubleshoot"></a>疑难解答

最有可能遇到的问题是，RoboCopy 命令在 Windows Server 端的 *"卷已满"* 时失败。 如果是这样的话，那么你的下载速度可能比你的上传速度好。 云分层每小时会运行一次，以便从已同步的本地 Windows Server 磁盘中疏散内容。

让同步进度和云分层释放磁盘空间。 您可以在 Windows 服务器上的文件资源管理器中观察到这一点。

当 Windows 服务器具有足够的可用容量时，重新运行该命令将解决此问题。 当你进入这种情况时，没有什么能打破，你可以满怀信心地前进。 再次运行命令的不便是唯一的后果。

您还可以遇到其他 Azure 文件同步问题。
如果发生这种情况，请看**LINK Azure 文件同步故障排除指南**。

## <a name="relevant-links"></a>相关链接

迁移内容：

* [StorSimple 8000 系列迁移指南](storage-files-migration-storsimple-8000.md)

Azure 文件同步内容：

* [AFS 概述](https://aka.ms/AFS)
* [AFS 部署指南](storage-files-deployment-guide.md)
* [AFS 故障排除](storage-sync-files-troubleshoot.md)
