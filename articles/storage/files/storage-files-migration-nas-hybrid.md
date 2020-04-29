---
title: 本地 NAS 迁移到 Azure 文件同步
description: 了解如何使用 Azure 文件同步和 Azure 文件共享将文件从本地网络附加存储（NAS）位置迁移到混合云部署。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 7b0c7a30580d3863a78e85b8b45287a598bbf394
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80247344"
---
# <a name="migrate-from-network-attached-storage-nas-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>使用 Azure 文件同步从网络附加存储（NAS）迁移到混合云部署

Azure 文件同步适用于直连存储（DAS）位置，并且不支持同步到网络连接存储（NAS）位置。
这种情况会迁移你需要的文件，本文将指导你完成此类迁移的计划和执行。

## <a name="migration-goals"></a>迁移目标

目标是将你在 NAS 设备上的共享移动到 Windows 服务器。 然后利用混合云部署的 Azure 文件同步。 此迁移需要在迁移期间保证生产数据的完整性和可用性的方式进行。 后者需要将停机时间保持在最少，使其适应或仅略超过定期维护时段。

## <a name="migration-overview"></a>迁移概述

如 Azure 文件[迁移概述一文](storage-files-migration-overview.md)中所述，使用正确的复制工具和方法非常重要。 你的 NAS 设备直接在本地网络上公开 SMB 共享。 RoboCopy （内置于 Windows Server）是在此迁移方案中移动文件的最佳方式。

- 阶段1：[确定需要多少个 Azure 文件共享](#phase-1-identify-how-many-azure-file-shares-you-need)
- 阶段2：[在本地预配适用于 Windows Server 的 Windows Server](#phase-2-provision-a-suitable-windows-server-on-premises)
- 阶段3：[部署 Azure 文件同步云资源](#phase-3-deploy-the-azure-file-sync-cloud-resource)
- 阶段4：[部署 Azure 存储资源](#phase-4-deploy-azure-storage-resources)
- 阶段5：[部署 Azure 文件同步代理](#phase-5-deploy-the-azure-file-sync-agent)
- 阶段6：在[Windows Server 上配置 Azure 文件同步](#phase-6-configure-azure-file-sync-on-the-windows-server)
- 阶段7： [RoboCopy](#phase-7-robocopy)
- 阶段8：[用户缩减](#phase-8-user-cut-over)

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>阶段1：确定需要多少个 Azure 文件共享

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-provision-a-suitable-windows-server-on-premises"></a>阶段2：在本地预配适用于 Windows Server 的 Windows Server

* 创建 Windows Server 2019-以最小2012R2 为虚拟机或物理服务器。 还支持 Windows Server 故障转移群集。
* 预配或添加直接附加存储（与 NAS 相比，不支持 NAS。

    如果使用 Azure 文件同步[云分层](storage-sync-cloud-tiering.md)功能，预配的存储量可能会小于你当前在 NAS 设备上使用的存储量。
    但是，当你将文件从较大的 NAS 空间复制到较小的 Windows 服务器卷时，你将需要批量工作：

    1. 移动一组容纳在磁盘上的文件
    2. 允许文件同步和云分层
    3. 在卷上创建更多可用空间时，请继续执行下一批文件。 
    
    可以通过在 NAS 设备上设置文件所占用的 Windows Server 上的等效空间来避免这种批处理方法。 考虑在 NAS/Windows 上进行重复数据删除。 如果你不想将此大量存储永久提交给 Windows Server，可以在迁移后和调整云分层策略之前减小卷大小。 这会创建更小的 Azure 文件共享本地缓存。

部署的 Windows 服务器的资源配置（计算和 RAM）主要取决于要同步的项目（文件和文件夹）的数量。 如果有任何问题，建议采用更高的性能配置。

[了解如何根据需要同步的项目（文件和文件夹）的数量调整 Windows Server 的大小。](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> 之前链接的文章显示了一个表，其中包含一个服务器内存（RAM）范围。 你可以为服务器的更小数量方向，但会预计初始同步可能需要更长时间。

## <a name="phase-3-deploy-the-azure-file-sync-cloud-resource"></a>阶段3：部署 Azure 文件同步云资源

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-4-deploy-azure-storage-resources"></a>阶段4：部署 Azure 存储资源

在此阶段中，请参阅阶段1中的映射表，并使用它来设置正确的 Azure 存储帐户和其中的文件共享数。

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-5-deploy-the-azure-file-sync-agent"></a>阶段5：部署 Azure 文件同步代理

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-6-configure-azure-file-sync-on-the-windows-server"></a>阶段6：在 Windows Server 上配置 Azure 文件同步

已注册的本地 Windows 服务器必须已准备就绪，并且已连接到 internet 以执行此过程。

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> 云分层是一项 AFS 功能，它使本地服务器的存储容量比存储在云中的存储容量少，同时还提供完整的命名空间。 本地关注的数据也会在本地缓存以实现快速访问性能。 云分层是每个 Azure 文件同步 "服务器终结点" 的一项可选功能。

> [!WARNING]
> 如果在 Windows server 卷上预配的存储空间比 NAS 设备上使用的数据少，则云分层是必需的。 如果未启用云分层，服务器将不会释放空间来存储所有文件。 将你的分层策略设置为临时迁移到99% 卷可用空间。 请确保在迁移完成后返回到云分层设置，并将其设置为更长期的有用级别。

为需要配置为进行同步的所有 Azure 文件共享/服务器位置重复创建同步组并添加匹配的服务器文件夹作为服务器终结点的步骤。

创建所有服务器终结点后，同步将正常运行。 你可以创建一个测试文件，并查看它从服务器位置同步到连接的 Azure 文件共享（如同步组中的云终结点所述）。

在这两个位置中，服务器文件夹和 Azure 文件共享都为空，并在任一位置等待数据。 在下一步中，你将开始将文件复制到 Windows Server，以便 Azure 文件同步将它们移到云中。 如果已启用云分层，则服务器将开始对文件进行分层，以防本地卷上的容量用尽。

## <a name="phase-7-robocopy"></a>阶段7： RoboCopy

基本迁移方法是从 NAS 设备到 Windows Server 的 RoboCopy，并 Azure 文件同步到 Azure 文件共享。

运行 Windows Server 目标文件夹的第一个本地副本：

* 确定 NAS 设备上的第一个位置。
* 标识已在 Windows Server 上配置了 Azure 文件同步的匹配文件夹。
* 使用 RoboCopy 开始复制

以下 RoboCopy 命令会将文件从 NAS 存储复制到 Windows Server 目标文件夹。 Windows Server 会将其同步到 Azure 文件共享。 

如果在 Windows Server 上预配的存储空间少于 NAS 设备上的文件占用的存储空间，则已配置云分层。 当本地 Windows Server 卷已满时，[云分层](storage-sync-cloud-tiering.md)将开始，并且已成功同步的层文件已成功同步。 云分层将生成足够的空间，以便从 NAS 设备继续复制。 云分层检查一小时以查看已同步的内容，并释放磁盘空间以达到99% 的可用空间。
此 RoboCopy 可以更快地移动文件，而不能在本地同步到云和层，从而用尽了本地磁盘空间。 RoboCopy 将失败。 建议你按阻止该操作的顺序浏览共享。 例如，不是同时为所有共享启动 RoboCopy 作业，或者只是在 Windows Server 上移动适合当前可用空间量的共享来提几个。

```console
Robocopy /MT:32 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

背景色：

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      允许 RoboCopy 运行多线程。 默认值为8，最大值为128。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG：\<文件名\>
   :::column-end:::
   :::column span="1":::
      将状态作为 UNICODE 输出到日志文件（覆盖现有日志）。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      输出到控制台窗口。 与输出一起用于日志文件。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      在备份应用程序使用的相同模式下运行 RoboCopy。 它允许 RoboCopy 移动当前用户没有权限的文件。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      允许按顺序在同一目标/目标上多次运行此 RoboCopy 命令。 它标识在之前已复制的内容并将其省略。 仅会处理自上次运行后发生的更改、添加和*删除*操作。 如果命令之前未运行，则不会省略任何内容。 对于仍在使用和变化的源位置， */MIR*标志是一个极佳的选项。
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

## <a name="phase-8-user-cut-over"></a>阶段8：用户缩减

首次运行 RoboCopy 命令时，您的用户和应用程序仍将在 NAS 上访问文件，并有可能更改这些文件。 这是可能的，RoboCopy 处理了一个目录，移到下一个目录，然后在源位置（NAS）上的用户添加、更改或删除了当前未在此当前 RoboCopy 运行中处理的文件。 此行为是预期的行为。

第一次运行是将大量数据移到 Windows 服务器，并通过 Azure 文件同步将数据移动到云中。此第一个副本可能需要较长时间，具体取决于：

* 下载带宽
* 上传带宽
* 本地网络速度和 RoboCopy 线程数与之匹配的最佳方式
* 需要由 RoboCopy 和 Azure 文件同步处理的项目（文件和文件夹）数

初始运行完成后，再次运行该命令。

第二次它将更快完成，因为它只需要传输自上次运行后发生的更改。 在第二次运行期间，新更改可能会累积。

重复此过程，直到你满足完成某个特定位置的 RoboCopy 所需的时间量在可接受的时间范围内。

如果你认为可接受的停机时间，并且你已准备好使 NAS 位置脱机：为了使用户脱机访问，你可以选择更改共享根目录上的 Acl，使用户无法再访问该位置，或采取任何其他适当的措施来阻止内容在 NAS 上的此文件夹中发生更改。

运行最后一个 RoboCopy 轮。 它将选取任何更改，这些更改可能已丢失。
这最后一步所花费的时间取决于 RoboCopy 扫描的速度。 您可以通过测量上一次运行所用的时间来估计时间（这等于您的停机时间）。

在 Windows Server 文件夹上创建一个共享，并且可能会调整你的 DFS N 部署以指向该文件夹。 请确保设置与 NAS SMB 共享上相同的共享级别权限。 如果你有一个企业级已加入域的 NAS，则用户 Sid 将自动匹配，因为用户存在于 Active Directory 中，RoboCopy 会完全保真地复制文件和元数据。 如果在 NAS 上使用了本地用户，则需要将这些用户重新创建为 Windows Server 本地用户，并将现有的 Sid RoboCopy 移到 Windows Server 上，并将其映射到新的 Windows Server 本地用户的 Sid。

你已完成将共享/共享组迁移到公共根或卷中。 （具体取决于阶段1中的映射）

可以尝试并行运行其中几个副本。 建议一次处理一个 Azure 文件共享的作用域。

> [!WARNING]
> 将所有数据从 NAS 移动到 Windows Server 并完成迁移后：返回到 Azure 门户中的***所有***同步组，并将云分层可用空间百分比值调整为更适合缓存利用率的内容（如20%）。 

云分层卷可用空间策略作用于可能有多个服务器终结点从中同步的卷级别。 如果忘记调整甚至一个服务器终结点上的可用空间，同步将继续应用最严格的规则，并尝试保留99% 的可用磁盘空间，从而使本地缓存不会像预期的那样运行。 除非您的目标只是只包含很少访问的卷的命名空间、存档数据，并且您保留另一方案的存储空间的剩余部分。

## <a name="troubleshoot"></a>疑难解答

最可能遇到的问题是 RoboCopy 命令在 Windows Server 端上失败并出现 *"卷已满"* 。 云分层每小时进行一次，用于从已同步的本地 Windows Server 磁盘撤走内容。 其目标是在卷上达到99% 的可用空间。

让同步进度和云分层释放磁盘空间。 您可以在 Windows Server 上的文件资源管理器中观察。

当你的 Windows 服务器有足够的可用容量时，重新运行该命令将解决此问题。 如果遇到这种情况，您可以放心地继续操作。 再次运行该命令是一个后果。

请查看以下部分中的链接，以了解 Azure 文件同步问题的疑难解答。

## <a name="next-steps"></a>后续步骤

有关 Azure 文件共享和 Azure 文件同步的详细信息，请阅读。以下文章可帮助了解高级选项、最佳做法和故障排除帮助。 这些文章链接到相应的[Azure 文件共享文档](storage-files-introduction.md)。

* [AFS 概述](https://aka.ms/AFS)
* [AFS 部署指南](storage-files-deployment-guide.md)
* [AFS 故障排除](storage-sync-files-troubleshoot.md)
