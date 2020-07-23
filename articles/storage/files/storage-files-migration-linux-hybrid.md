---
title: Linux 迁移到 Azure 文件同步
description: 了解如何使用 Azure 文件同步和 Azure 文件共享将文件从 Linux 服务器位置迁移到混合云部署。
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: fd2e4f5c81427413e3f3f3eceaa0cc41a3b9e318
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85510378"
---
# <a name="migrate-from-linux-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>使用 Azure 文件同步从 Linux 迁移到混合云部署

Azure 文件同步适用于带有直接连接的存储（DAS）的 Windows Server 实例。 它不支持与 Linux 或远程服务器消息块（SMB）共享进行同步。

因此，将文件服务转换为混合部署将导致需要迁移到 Windows Server。 本文将指导你完成此类迁移的计划和执行。

## <a name="migration-goals"></a>迁移目标

目标是将你在 Linux Samba 服务器上的共享移动到 Windows Server 实例。 然后，将 Azure 文件同步用于混合云部署。 此迁移需要在迁移期间保证生产数据的完整性和可用性的方式进行。 后者需要将停机时间保持在最少，使其适应或仅略超过定期维护时段。

## <a name="migration-overview"></a>迁移概述

如 Azure 文件[迁移概述一文](storage-files-migration-overview.md)中所述，使用正确的复制工具和方法非常重要。 Linux Samba 服务器直接在本地网络上公开 SMB 共享。 Robocopy 内置于 Windows Server 中是在此迁移方案中移动文件的最佳方式。

如果你不在 Linux 服务器上运行 Samba，而是想要将文件夹迁移到 Windows Server 上的混合部署，则可以使用 Linux 复制工具而不是 Robocopy。 如果这样做，请注意文件复制工具中的保真度功能。 请参阅迁移概述一文中的[迁移基础部分](storage-files-migration-overview.md#migration-basics)，了解要在复制工具中查找的内容。

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>阶段1：确定需要多少个 Azure 文件共享

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-provision-a-suitable-windows-server-instance-on-premises"></a>阶段2：在本地预配合适的 Windows Server 实例

* 创建作为虚拟机或物理服务器的 Windows Server 2019 实例。 Windows Server 2012 R2 是最低要求。 还支持 Windows Server 故障转移群集。
* 预配或添加直连存储（DAS）。 不支持网络附加存储 (NAS)。

  如果你使用 Azure 文件同步[云分层](storage-sync-cloud-tiering.md)功能，则你预配的存储量可能会小于你当前在 Linux Samba 服务器上使用的存储量。 但是，在稍后的阶段，当你将文件从较大的 Linux Samba 服务器空间复制到较小的 Windows 服务器卷时，你将需要批量工作：

  1. 移动一组适合该磁盘的文件。
  2. 让文件同步和云分层参与。
  3. 在卷上创建更多可用空间时，请继续执行下一批文件。 
    
  通过在 Linux Samba 服务器上的文件所占用的 Windows Server 实例上设置等效空间，可以避免这种批处理方法。 请考虑在 Windows 上启用删除重复。 如果你不想将此大量存储永久提交给 Windows Server 实例，则可以在迁移后和调整云分层策略之前减小卷大小。 这会创建更小的 Azure 文件共享本地缓存。

所部署的 Windows Server 实例的资源配置（计算和 RAM）主要取决于要同步的项目（文件和文件夹）的数量。 如果有任何问题，我们建议使用更高性能的配置。

[了解如何根据需要同步的项目（文件和文件夹）的数量调整 Windows Server 实例的大小。](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> 之前链接的文章显示了一个表，其中包含一个服务器内存（RAM）范围。 你可以为服务器的较小数量定位，但会预计初始同步可能需要更长时间。

## <a name="phase-3-deploy-the-azure-file-sync-cloud-resource"></a>阶段3：部署 Azure 文件同步云资源

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-4-deploy-azure-storage-resources"></a>阶段4：部署 Azure 存储资源

在此阶段中，请参阅阶段1中的映射表，并使用它来设置正确的 Azure 存储帐户和其中的文件共享数。

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-5-deploy-the-azure-file-sync-agent"></a>阶段5：部署 Azure 文件同步代理

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-6-configure-azure-file-sync-on-the-windows-server-deployment"></a>阶段6：在 Windows Server 部署上配置 Azure 文件同步

注册的本地 Windows Server 实例必须已准备就绪，并且已连接到 internet 以执行此过程。

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> 云分层是一项 Azure 文件同步功能，它允许本地服务器的存储容量比存储在云中的存储容量少，同时还提供完整的命名空间。 本地关注的数据也会在本地缓存以实现快速访问性能。 云分层是每个 Azure 文件同步服务器终结点的一项可选功能。

> [!WARNING]
> 如果在 Windows Server 卷上预配的存储空间比在 Linux Samba 服务器上使用的数据少，则云分层是必需的。 如果未启用云分层，服务器将不会释放空间来存储所有文件。 为迁移暂时设置分层策略，将卷的可用空间设置为99%。 请确保在迁移完成后返回到云分层设置，并将策略设置为长时间的更有用的级别。

重复创建同步组的步骤，并将匹配的服务器文件夹添加为要为同步配置的所有 Azure 文件共享和服务器位置的服务器终结点。

创建所有服务器终结点后，同步将正常运行。 你可以创建一个测试文件，并查看它从服务器位置同步到连接的 Azure 文件共享（如同步组中的云终结点所述）。

这两个位置（服务器文件夹和 Azure 文件共享）都为空并等待数据。 在下一步中，你将开始将文件复制到 Windows Server 实例，以便 Azure 文件同步将它们移到云中。 如果已启用云分层，则当你的本地卷上的容量用尽时，服务器将开始对文件进行分级。

## <a name="phase-7-robocopy"></a>阶段7： Robocopy

基本迁移方法是使用 Robocopy 复制文件并使用 Azure 文件同步执行同步。

运行 Windows Server 目标文件夹的第一个本地副本：

1. 确定 Linux Samba 服务器上的第一个位置。
1. 标识已在其上配置了 Azure 文件同步的 Windows Server 实例上的匹配文件夹。
1. 使用 Robocopy 开始复制。

以下 Robocopy 命令会将文件从 Linux Samba 服务器的存储复制到 Windows Server 目标文件夹。 Windows Server 会将其同步到 Azure 文件共享。 

如果在 Windows Server 实例上预配的存储空间小于 Linux Samba 服务器上的文件占用的存储空间，则已配置云分层。 当本地 Windows Server 卷已满时，将启动[云分层](storage-sync-cloud-tiering.md)，并对已成功同步的文件进行分层。 云分层将生成足够的空间，以从 Linux Samba 服务器继续复制。 云分层检查一小时以查看已同步的内容，并释放磁盘空间以达到卷的99% 可用空间的策略。

由于 Robocopy 可以更快地移动文件，因此它可以比本地同步到云和层，从而导致用尽本地磁盘空间。 Robocopy 随后会失败。 建议你按阻止问题的顺序处理共享。 例如，请考虑不同时为所有共享启动 Robocopy 作业。 或考虑移动适合 Windows Server 实例上当前可用空间量的共享。 如果你的 Robocopy 作业失败，则只要你使用以下镜像/清除选项，你始终可以重新运行该命令：

```console
Robocopy /MT:32 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

背景色：

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      允许 Robocopy 运行多线程。 默认值为8，最大值为128。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:\<file name\>
   :::column-end:::
   :::column span="1":::
      将状态作为 Unicode 输出到日志文件（覆盖现有日志）。
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
      在备份应用程序将使用的相同模式下运行 Robocopy。 它允许 Robocopy 移动当前用户没有权限的文件。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      允许按顺序在同一目标/目标上多次运行此 Robocopy 命令。 它标识并忽略以前复制的内容。 仅处理自上次运行以来发生的更改、添加和删除操作。 如果命令之前未运行，则不会省略任何内容。 对于仍在使用和变化的源位置， **/MIR**标志是一个极佳的选项。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY： copyflag [s]
   :::column-end:::
   :::column span="1":::
      文件副本的保真度（默认值为/COPY： DAT）。 复制标志为： D = data，A = 属性，T = 时间戳，S = 安全性 = NTFS Acl，O = 所有者信息，U = 审核信息。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL
   :::column-end:::
   :::column span="1":::
      复制所有文件信息（等同于/COPY： DATSOU）。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY： copyflag [s]
   :::column-end:::
   :::column span="1":::
      目录副本的保真度（默认值为/DCOPY： DA）。 复制标志为： D = data，A = 属性，T = 时间戳。
   :::column-end:::
:::row-end:::

## <a name="phase-8-user-cut-over"></a>阶段8：用户缩减

当你首次运行 Robocopy 命令时，你的用户和应用程序仍将访问 Linux Samba 服务器上的文件，并有可能更改这些文件。 此方法可能会处理目录并移至下一个目录，然后在源位置（Linux）中添加、更改或删除现在不会在此当前 Robocopy 运行中处理的文件。 这是预期的行为。

第一次运行是将大量数据移到 Windows Server 实例，并通过 Azure 文件同步将数据移动到云中。此第一个副本可能需要较长时间，具体取决于：

* 下载带宽。
* 上传带宽。
* 本地网络速度和 Robocopy 线程数与之匹配的最佳数目。
* Robocopy 和 Azure 文件同步需要处理的项目（文件和文件夹）的数量。

初始运行完成后，再次运行该命令。

它的运行速度更快，因为它只需传输自上次运行后发生的更改。 在此秒内，运行新的更改仍可能会累积。

重复此过程，直到你满足为特定位置完成 Robocopy 操作所需的时间量在可接受的时间范围内。

如果考虑到可接受的停机时间，并且已准备好使 Linux 位置脱机，则可以更改共享根目录上的 Acl，使用户无法再访问该位置。 或者，你可以采取任何其他适当的措施，防止在 Linux 服务器上的此文件夹中更改内容。

运行最后一个 Robocopy 轮。 它将选取可能已丢失的任何更改。 这最后一步所花费的时间取决于 Robocopy 扫描的速度。 您可以通过测量上一次运行所用的时间来估计时间（这等于您的停机时间）。

在 Windows Server 文件夹上创建一个共享，并且可能会调整你的 DFS N 部署以指向该文件夹。 请确保设置与 Linux Samba 服务器 SMB 共享上相同的共享级别权限。 如果在 Linux Samba 服务器上使用了本地用户，则需要将这些用户重新创建为 Windows Server 本地用户。 还需要将 Robocopy 移动到 Windows Server 实例的现有 Sid 映射到新的 Windows Server 本地用户的 Sid。 如果使用 Active Directory 帐户和 Acl，则 Robocopy 会按原样移动它们，无需执行其他操作。

已完成将共享或一组共享迁移到公共根或卷中（具体取决于你在阶段1的映射）。

可以尝试并行运行其中几个副本。 建议一次处理一个 Azure 文件共享的作用域。

> [!WARNING]
> 将所有数据从 Linux Samba 服务器移至 Windows Server 实例并完成迁移后，返回到 Azure 门户中的*所有*同步组。 调整云分层卷的可用空间百分比，使其更适合缓存利用率，如20%。 

云分层卷中的可用空间策略作用于可能有多个服务器终结点从中同步的卷级别。 如果忘记调整甚至一个服务器终结点上的可用空间，同步将继续应用最严格的规则，并尝试将可用磁盘空间保留为99%。 然后，本地缓存可能无法按预期执行。 如果你的目标是只包含很少访问的存档数据的卷的命名空间，并且你为其他方案保留存储空间的剩余部分，则性能可能是可接受的。

## <a name="troubleshoot"></a>疑难解答

最常见的问题是 Robocopy 命令在 Windows Server 端上的**卷已满**时失败。 云分层每小时进行一次，以撤走已同步的本地 Windows Server 磁盘的内容。 其目标是在卷上达到99% 的可用空间。

让同步进度和云分层释放磁盘空间。 您可以在 Windows Server 上的文件资源管理器中观察。

当 Windows Server 实例具有足够的可用容量时，重新运行该命令将解决此问题。 出现这种情况时，无需中断，您可以放心地继续操作。 再次运行该命令会给您带来不便。

请查看以下部分中的链接，以了解 Azure 文件同步问题的疑难解答。

## <a name="next-steps"></a>后续步骤

还介绍了有关 Azure 文件共享和 Azure 文件同步的详细信息。以下文章包含高级选项、最佳做法和故障排除帮助。 这些文章链接到相应的[Azure 文件共享文档](storage-files-introduction.md)。

* [Azure 文件同步概述](https://aka.ms/AFS)
* [Azure 文件同步部署指南](storage-files-deployment-guide.md)
* [Azure 文件同步故障排除](storage-sync-files-troubleshoot.md)
