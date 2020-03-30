---
title: 本地 NAS 迁移到 Azure 文件同步
description: 了解如何使用 Azure 文件同步和 Azure 文件共享将文件从本地网络附加存储 （NAS） 位置迁移到混合云部署。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 7b0c7a30580d3863a78e85b8b45287a598bbf394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247344"
---
# <a name="migrate-from-network-attached-storage-nas-to-a-hybrid-cloud-deployment-with-azure-file-sync"></a>通过 Azure 文件同步从网络附加存储 （NAS） 迁移到混合云部署

Azure 文件同步适用于直接附加存储 （DAS） 位置，不支持同步到网络附加存储 （NAS） 位置。
这一事实使得您的文件迁移是必要的，本文将指导您完成此类迁移的规划和执行。

## <a name="migration-goals"></a>迁移目标

目标是将 NAS 设备上的共享移动到 Windows 服务器。 然后利用 Azure 文件同步进行混合云部署。 需要以保证生产数据的完整性以及在迁移期间的可用性的方式完成此迁移。 后者要求将停机时间降至最低，以便它能够适应或仅略高于常规维护窗口。

## <a name="migration-overview"></a>迁移概述

如 Azure 文件[迁移概述一文中](storage-files-migration-overview.md)所述，使用正确的复制工具和方法非常重要。 您的 NAS 设备直接在本地网络上公开 SMB 共享。 RoboCopy 内置于 Windows 服务器中，是在此迁移方案中移动文件的最佳方式。

- 第 1 阶段：[确定需要多少 Azure 文件共享](#phase-1-identify-how-many-azure-file-shares-you-need)
- 第 2 阶段：[在本地预配合适的 Windows 服务器](#phase-2-provision-a-suitable-windows-server-on-premises)
- 第 3 阶段：[部署 Azure 文件同步云资源](#phase-3-deploy-the-azure-file-sync-cloud-resource)
- 第 4 阶段：[部署 Azure 存储资源](#phase-4-deploy-azure-storage-resources)
- 第 5 阶段：[部署 Azure 文件同步代理](#phase-5-deploy-the-azure-file-sync-agent)
- 第 6 阶段：[在 Windows 服务器上配置 Azure 文件同步](#phase-6-configure-azure-file-sync-on-the-windows-server)
- 第 7 阶段[：RoboCopy](#phase-7-robocopy)
- 第 8 阶段：[用户剪切](#phase-8-user-cut-over)

## <a name="phase-1-identify-how-many-azure-file-shares-you-need"></a>第 1 阶段：确定需要多少 Azure 文件共享

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-provision-a-suitable-windows-server-on-premises"></a>第 2 阶段：在本地预配合适的 Windows 服务器

* 创建 Windows 服务器 2019 - 至少 2012R2 - 作为虚拟机或物理服务器。 还支持 Windows 服务器故障转移群集。
* 预配或添加直接连接存储（与不支持 NAS 的 NAS 相比的 DAS）。

    如果使用 Azure 文件同步[云分层](storage-sync-cloud-tiering.md)功能，则预配的存储量可能小于 NAS 设备上当前使用的存储量。
    但是，当您在稍后阶段将文件从较大的 NAS 空间复制到较小的 Windows Server 卷时，需要分批工作：

    1. 将一组适合磁盘的文件移到磁盘上
    2. 让文件同步和云分层参与
    3. 在卷上创建更多可用空间时，继续下一批文件。 
    
    通过在 Windows 服务器上预配文件在 NAS 设备上占用的等效空间，可以避免此批处理方法。 请考虑 NAS/ Windows 上的重复数据消除。 如果不想将大量高存储空间永久提交到 Windows 服务器，则可以在迁移后和调整云分层策略之前减小卷大小。 这将创建 Azure 文件共享的较小本地缓存。

部署的 Windows 服务器的资源配置（计算和 RAM）主要取决于要同步的项目（文件和文件夹）的数量。 如果您有任何疑问，我们建议您采用更高的性能配置。

[了解如何根据需要同步的项目（文件和文件夹）数量对 Windows 服务器进行大小调整。](storage-sync-files-planning.md#recommended-system-resources)

> [!NOTE]
> 前面链接的文章提供了一个表，其中提供了服务器内存 （RAM） 的范围。 您可以定向到服务器的较小数量，但预计初始同步可能需要更多的时间。

## <a name="phase-3-deploy-the-azure-file-sync-cloud-resource"></a>第 3 阶段：部署 Azure 文件同步云资源

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-4-deploy-azure-storage-resources"></a>第 4 阶段：部署 Azure 存储资源

在此阶段，请查阅阶段 1 中的映射表，并用它来预配其中正确数量的 Azure 存储帐户和文件共享。

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-5-deploy-the-azure-file-sync-agent"></a>第 5 阶段：部署 Azure 文件同步代理

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-6-configure-azure-file-sync-on-the-windows-server"></a>第 6 阶段：在 Windows 服务器上配置 Azure 文件同步

您注册的本地 Windows 服务器必须已准备就绪，并连接到 Internet 进行此过程。

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> 云分层是 AFS 功能，它允许本地服务器的存储容量少于存储在云中的存储容量，但具有完整的命名空间可用。 本地有趣的数据也缓存在本地，以快速访问性能。 云分层是每个 Azure 文件同步"服务器终结点"的可选功能。

> [!WARNING]
> 如果 Windows 服务器卷上的存储空间比 NAS 设备上使用的数据少，则必须进行云分层。 如果不打开云分层，服务器将不会释放空间来存储所有文件。 将分层策略（临时用于迁移）设置为 99% 的卷可用空间。 请务必在迁移完成后返回到云分层设置，并将其设置为更长期有用的级别。

重复同步组创建和添加匹配服务器文件夹的步骤，作为需要配置用于同步的所有 Azure 文件共享/服务器位置的服务器终结点。

创建所有服务器终结点后，同步工作。 您可以创建测试文件，并看到它从服务器位置同步到连接的 Azure 文件共享（如同步组中的云终结点所述）。

两个位置、服务器文件夹和 Azure 文件共享在任一位置都为空，正在等待任何位置的数据。 在下一步中，您将开始将文件复制到 Azure 文件同步的 Windows 服务器中，以将它们移动到云中。 如果启用了云分层，则服务器将开始分层文件，以防本地卷上的容量不足。

## <a name="phase-7-robocopy"></a>第 7 阶段：RoboCopy

基本迁移方法是从 NAS 设备到 Windows 服务器的 RoboCopy，以及 Azure 文件同步到 Azure 文件共享。

将第一个本地副本运行到 Windows Server 目标文件夹：

* 确定 NAS 设备上的第一个位置。
* 标识 Windows 服务器上已配置 Azure 文件同步的匹配文件夹。
* 使用 RoboCopy 启动副本

以下 RoboCopy 命令会将文件从 NAS 存储复制到 Windows Server 目标文件夹。 Windows 服务器将将其同步到 Azure 文件共享。 

如果在 Windows 服务器上预配的存储空间比文件在 NAS 设备上占用的存储少，则已配置云分层。 当本地 Windows Server 卷已满时，[云分层](storage-sync-cloud-tiering.md)将启动，并且已成功同步的层文件将启动。 云分层将生成足够的空间来继续从 NAS 设备复制。 云分层每小时检查一次，查看已同步的内容，并释放磁盘空间以达到 99% 的卷可用空间。
RoboCopy 移动文件的速度可能超过您同步到云和本地层的速度，从而耗尽了本地磁盘空间。 RoboCopy 将失败。 建议您按一个防止此情况的顺序处理共享。 例如，不同时启动所有共享的 RoboCopy 作业，或者仅移动适合 Windows 服务器上当前可用空间量的共享，仅举几例。

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
      /UNILOG：\<文件名\>
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
      允许在同一目标/目标上按顺序运行此 RoboCopy 命令几次。 它标识之前复制的内容，并省略它。 只会处理自上次运行以来发生的更改、添加和"*删除*"。 如果之前未运行该命令，则不会省略任何内容。 对于仍在积极使用和更改的源位置 *，/MIR*标志是一个极好的选项。
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

## <a name="phase-8-user-cut-over"></a>第 8 阶段：用户剪切

首次运行 RoboCopy 命令时，用户和应用程序仍在访问 NAS 上的文件，并可能更改这些文件。 有可能，RoboCopy 已经处理了目录，移动到下一个目录，然后在源位置 （NAS） 上的用户添加、更改或删除现在在此 RoboCopy 运行中不会处理的文件。 此行为是预期的行为。

第一个运行是有关通过 Azure 文件同步将大部分数据移动到 Windows 服务器并移动到云中。此第一个副本可能需要很长时间，具体取决于：

* 您的下载带宽
* 上传带宽
* 本地网络速度和 RoboCopy 线程数量与它匹配的最佳数量
* 需要由 RoboCopy 和 Azure 文件同步处理的项目（文件和文件夹）数

完成初始运行后，再次运行该命令。

第二次完成得更快，因为它只需要传输自上次运行以来发生的更改。 在第二次运行期间，仍然会累积新的更改。

重复此过程，直到您确信为特定位置完成 RoboCopy 所需的时间在可接受的停机时间范围内。

当您认为停机时间可以接受，并准备使 NAS 位置脱机时：为了让用户脱机访问，您可以选择更改共享根上的 ACL，以便用户无法再访问该位置或执行任何其他适当的步骤防止内容在 NAS 上的此文件夹中更改。

运行最后一个 RoboCopy 轮。 它将接收任何可能错过的更改。
这最后一步需要多长时间，取决于 RoboCopy 扫描的速度。 您可以通过测量上次运行所花的时间来估计时间（相当于停机时间）。

在 Windows Server 文件夹上创建共享，并可能调整 DFS-N 部署以指向该共享。 请务必设置与 NAS SMB 共享相同的共享级别权限。 如果您有企业级域加入 NAS，则用户 SID 将自动匹配，因为用户存在于 Active Directory 中，RoboCopy 以全保真度复制文件和元数据。 如果您在 NAS 上使用了本地用户，则需要将这些用户重新创建为 Windows Server 本地用户，并将现有 SIDs RoboCopy 移动到您的 Windows 服务器到新 Windows Server 本地用户的 SID。

您已完成将共享/共享组迁移到公共根或卷。 （取决于从阶段 1 的映射）

您可以尝试并行运行其中的几个副本。 我们建议一次处理一个 Azure 文件共享的范围。

> [!WARNING]
> 将所有数据从 NAS 移动到 Windows 服务器并完成迁移后：返回到 Azure 门户***中的所有同步组***，并将云分层卷可用空间百分比值调整为更适合缓存利用率的内容，例如 20%。 

云分层卷可用空间策略在卷级别上运行，其中可能有多个服务器终结点从该端点同步。 如果您忘记调整一个服务器终结点上的可用空间，同步将继续应用最严格的规则，并尝试保留 99% 的可用磁盘空间，从而使本地缓存无法按预期执行。 除非您的目标是仅具有仅包含很少访问的卷的命名空间，否则您会将存储空间的其余部分保留为另一个方案。

## <a name="troubleshoot"></a>疑难解答

最有可能遇到的问题是，RoboCopy 命令在 Windows Server 端的 *"卷已满"* 时失败。 云分层每小时会运行一次，以便从已同步的本地 Windows Server 磁盘中疏散内容。 它的目标是达到您的99%的可用空间的音量。

让同步进度和云分层释放磁盘空间。 您可以在 Windows 服务器上的文件资源管理器中观察到这一点。

当 Windows 服务器具有足够的可用容量时，重新运行该命令将解决此问题。 当你进入这种情况时，没有什么能打破，你可以满怀信心地前进。 再次运行命令的不便是唯一的后果。

检查以下部分中的链接，以排除 Azure 文件同步问题。

## <a name="next-steps"></a>后续步骤

有关 Azure 文件共享和 Azure 文件同步，还有更多要了解的问题。以下文章有助于了解高级选项、最佳实践，并包含故障排除帮助。 这些文章根据需要链接到[Azure 文件共享文档](storage-files-introduction.md)。

* [AFS 概述](https://aka.ms/AFS)
* [AFS 部署指南](storage-files-deployment-guide.md)
* [AFS 故障排除](storage-sync-files-troubleshoot.md)
