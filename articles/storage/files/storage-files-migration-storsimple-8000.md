---
title: StorSimple 8000 系列迁移到 Azure 文件同步
description: 了解如何将 StorSimple 8100 或 8600 设备迁移到 Azure 文件同步。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 7f0c4da7caf71670746e84d5cfaa457ebae57156
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80755044"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>StorSimple 8100 和 8600 迁移到 Azure 文件同步

StorSimple 8000 系列由 8100 或 8600 物理本地设备及其云服务组件表示。 可以将数据从其中任一设备迁移到 Azure 文件同步环境。 Azure 文件同步是 StorSimple 设备可以迁移到的默认且战略性的长期 Azure 服务。

StorSimple 8000 系列将于 2022 年 12 月实现[生命周期结束](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%208000%20Series)。 尽快开始规划迁移非常重要。 本文提供了成功迁移到 Azure 文件同步的必要背景知识和迁移步骤。 

## <a name="azure-file-sync"></a>Azure 文件同步

> [!IMPORTANT]
> Microsoft 致力于协助客户迁移。 Email AzureFilesMigration@microsoft .com，用于自定义迁移计划以及在迁移期间提供帮助。

Azure 文件同步是一种 Microsoft 云服务，基于两个主要组件：

* 文件同步和云分层。
* 文件共享作为 Azure 中的本机存储，可通过多个协议（如 SMB 和文件 REST）进行访问。 Azure 文件共享可与 Windows 服务器上的文件共享相媲美，您可以将其作为网络驱动器本机装载。 它支持重要的文件保真度方面，如属性、权限和时间戳。 使用 Azure 文件共享时，不再需要应用程序或服务来解释存储在云中的文件和文件夹。 您可以通过熟悉的协议和客户端（如 Windows 文件资源管理器）以本机方式访问它们。 这使得 Azure 文件共享成为在云中存储通用文件服务器数据以及某些应用程序数据的理想和最灵活的方法。

本文重点介绍迁移步骤。 如果要在迁移之前了解有关 Azure 文件同步的更多内容，我们建议您提供以下文章：

* [Azure 文件同步 - 概述](https://aka.ms/AFS "概述")
* [Azure 文件同步 - 部署指南](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>迁移目标

目标是保证生产数据的完整性，并保证可用性。 后者要求将停机时间降至最低，以便它能够适应或仅略高于常规维护窗口。

## <a name="storsimple-8000-series-migration-path-to-azure-file-sync"></a>StorSimple 8000 系列迁移路径到 Azure 文件同步

运行 Azure 文件同步代理需要本地 Windows 服务器。 Windows 服务器可以至少位于 2012R2 服务器，但理想情况下是 Windows 服务器 2019。

有许多替代迁移路径，它会创建太长的文章来记录所有这些路径，并说明它们为什么在本文中作为最佳实践建议的路径上承担风险或缺点。

![StorSimple 8000 系列迁移阶段概述](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-overview.png "StorSimple 8000 系列迁移路线概述本文中进一步介绍了以下阶段。")

前面的图像描述了与本文中各节相对应的阶段。
我们使用云端迁移来避免不必要地将文件撤回到您当地的 StorSimple 设备。 此方法可避免影响本地缓存行为或网络带宽使用，其中任一都可能影响生产工作负载。
云端迁移正在数据快照（卷克隆）上运行。 因此，您的生产数据与此过程隔离， 直到迁移结束时进行翻越。 处理本质上是备份，使迁移安全且易于重复，如果遇到任何困难。

## <a name="considerations-around-existing-storsimple-backups"></a>有关现有 StorSimple 备份的注意事项

StorSimple 允许您以卷克隆的形式进行备份。 本文使用新的卷克隆来迁移实时文件。
如果您需要迁移备份以及实时数据，则本文中的所有指南仍适用。 唯一的区别是，您不是从新的卷克隆开始，而是从需要迁移的最旧的备份卷克隆开始。

顺序如下：

* 确定必须迁移的最小卷克隆集。 如果可能，我们建议将此列表保持在最低水平，因为迁移的备份越多，整个迁移过程所花的时间就越长。
* 完成迁移过程时，从要迁移的最早卷克隆开始，然后在每个后续迁移中，使用下一个最旧的卷克隆。
* 完成每个卷克隆迁移后，必须拍摄 Azure 文件共享快照。 [Azure 文件共享快照](storage-snapshots-files.md)是 Azure 文件共享的文件和文件夹结构的时间点备份方式。 迁移完成后将需要这些快照，以确保在迁移中完成迁移时保留每个卷克隆的版本。
* 确保为同一 StorSimple 卷提供的所有 Azure 文件共享快照获取。 卷克隆位于卷级别，Azure 文件共享快照位于共享级别。 卷克隆的迁移完成后，必须拍摄共享快照（每个 Azure 文件共享）。
* 重复卷克隆的迁移过程，并在每次卷克隆后拍摄共享快照，直到您赶上实时数据的快照。 迁移卷克隆的过程在以下阶段中介绍。 

如果根本不需要移动备份，并且在仅迁移实时数据后可以在 Azure 文件共享端启动新的备份链，则这有利于降低迁移的复杂性和迁移所花费的时间。 您可以决定是否移动备份，以及 StorSimple 中每个卷（不是每个共享）有多少。

## <a name="phase-1-get-ready"></a>第 1 阶段：做好准备

:::row:::
    :::column:::
        ![一种图像，用于说明早期概述图像的一部分，可帮助集中文章的本小节。](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-1.png)
    :::column-end:::
    :::column:::
        迁移的基础是卷克隆和虚拟云设备，称为 StorSimple 8020。
此阶段侧重于在 Azure 中部署这些资源。
    :::column-end:::
:::row-end:::

### <a name="deploy-a-storsimple-8020-virtual-appliance"></a>部署 StorSimple 8020 虚拟设备

部署云设备是一个需要安全性、网络和一些其他注意事项的过程。

> [!IMPORTANT]
> 以下指南包含一些不必要的部分。 阅读并遵循文章从开始到"步骤 3"。 然后返回到本文。 此时，您无需完成本指南中超出该指南的"步骤 3"或任何其他内容。

[部署 StorSimple 8020 虚拟设备](../../storsimple/storsimple-8000-cloud-appliance-u2.md)

### <a name="determine-a-volume-clone-to-use"></a>确定要使用的卷克隆

准备好开始迁移后，第一步是采用新的卷克隆（就像备份一样）来捕获 StorSimple 云存储的当前状态。 获取每个 StorSimple 卷的克隆。
如果需要移动备份，则使用的第一个卷克隆不是新创建的克隆，而是需要迁移的最旧的卷克隆（最旧备份）。
有关详细指导，请参阅["有关现有 StorSimple 备份的注意事项"](#considerations-around-existing-storsimple-backups)一节。

> [!IMPORTANT]
> 以下指南包含一些不必要的部分。 只阅读并遵循链接到部分中概述的步骤。 然后返回到本文。 您无需遵循"后续步骤"部分。

[创建卷的克隆](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)

### <a name="use-the-volume-clone"></a>使用卷克隆

阶段 1 的最后阶段是使您选择的卷克隆在 Azure 中的 8020 虚拟设备上可用。

> [!IMPORTANT]
> 以下指南包含必要的步骤，但最后还包含设置卷格式的说明。 **不格式化卷**阅读并遵循链接到"第 7 节"从开始到指令："10。 要格式化一个简单的卷，..." 在按照此步骤之前停止，然后返回到本文。

[在 Azure 中的 8020 虚拟设备上安装卷克隆](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-1-summary"></a>第 1 阶段摘要

现在已完成第 1 阶段，您将完成以下操作：

* 在 Azure 中部署了 StorSimple 8020 虚拟设备。
* 确定您将从哪个卷克隆开始迁移。
* 将卷克隆（每个实时卷一个）装载到 Azure 中的 StorSimple 虚拟设备，其数据可供进一步使用。

## <a name="phase-2-cloud-vm"></a>第 2 阶段：云 VM

:::row:::
    :::column:::
        ![一种图像，用于说明早期概述图像的一部分，可帮助集中文章的本小节。](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-2.png)
    :::column-end:::
    :::column:::
        初始克隆在 Azure 中的 StorSimple 8020 虚拟设备上可用后，现在是时候预配 VM 并在 iSCSI 上向该 VM 公开卷克隆（或多个）。
    :::column-end:::
:::row-end:::

### <a name="deploy-an-azure-vm"></a>部署 Azure VM

Azure 中的 Windows Server 虚拟机与 StorSimple 8020 类似，这是一个临时基础结构，仅在迁移期间才是必需的。
部署的 VM 的配置主要取决于要同步的项目（文件和文件夹）数。 如果您有任何疑问，我们建议您采用更高的性能配置。

单个 Windows 服务器最多可以同步 30 个 Azure 文件共享。
您决定需要包含每个共享/路径或 StorSimple 卷的根并计算项目（文件和文件夹）的规范。

数据的总体大小不是瓶颈 - 它是需要定制机器规格的项目数量。

* [了解如何根据需要同步的项目（文件和文件夹）数量对 Windows 服务器进行大小调整。](storage-sync-files-planning.md#recommended-system-resources)

    **请注意：** 前面链接的文章提供了一个表，其中提供了服务器内存 （RAM） 的范围。 定向到 Azure VM 的大数。 您可以定向到本地机器的较小数字。

* [了解如何部署 Windows 塞弗 VM。](../../virtual-machines/windows/quick-create-portal.md)

> [!IMPORTANT]
> 确保 VM 部署在同一 Azure 区域中，与 StorSimple 8020 虚拟设备相同。 如果作为此迁移的一部分，还需要从今天存储的区域更改云数据的区域，则可以在预配 Azure 文件共享时在后续步骤中执行此操作。

> [!IMPORTANT]
> 通常，本地 Windows 服务器用于在本地 StorSimple 设备前面。 在这种配置中，可以在该 Windows 服务器上启用"[数据重复数据消除](https://docs.microsoft.com/windows-server/storage/data-deduplication/install-enable)"功能。 **如果将数据重复数据与 StorSimple 数据一起使用，请确保在此 Azure VM 上启用数据重复数据消除。** 不要将此文件级重复数据消除与 StorSimples 内置块级重复数据消除混为一谈，对此无需执行任何操作。

> [!IMPORTANT]
> 要优化性能，请为云 VM 部署**快速操作系统磁盘**。 您将将同步数据库存储在操作系统磁盘上，用于所有数据卷。 此外，请确保创建**大型 OS 磁盘**。 根据 StorSimple 卷上的项目（文件和文件夹）数，操作系统磁盘可能需要**几百 GiB**的空间来容纳同步数据库。

### <a name="expose-the-storsimple-8020-volumes-to-the-azure-vm"></a>将 StorSimple 8020 卷公开到 Azure VM

在此阶段，您正在通过 iSCSI 将一个或多个 StorSimple 卷从 8020 虚拟设备连接到已预配的 Windows Server VM。

> [!IMPORTANT]
> 对于以下文章，请仅完成**云设备的获取专用 IP，****然后通过 iSCSI 部分进行连接**，然后返回到本文。

1. [获取云设备的专用 IP](../../storsimple/storsimple-8000-cloud-appliance-u2.md#get-private-ip-for-the-cloud-appliance)
2. [通过 iSCSI 连接](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-2-summary"></a>第 2 阶段摘要

现在，您已完成第 2 阶段，您有： 

* 在与 8020 虚拟 StorSimple 设备相同的区域中预配 Windows 服务器 VM
* 通过 iSCSI 将 8020 到 Windows 服务器 VM 的所有适用卷公开。
* 现在，当您在装载的卷上的服务器 VM 上使用文件资源管理器时，您应该会看到文件和文件夹内容。

仅当已完成所有需要迁移的卷的这些步骤时，才能继续阶段 3。

## <a name="phase-3-set-up-azure-file-shares-and-get-ready-for-azure-file-sync"></a>第 3 阶段：设置 Azure 文件共享并为 Azure 文件同步做好准备

:::row:::
    :::column:::
        ![一种图像，用于说明早期概述图像的一部分，可帮助集中文章的本小节。](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-3.png)
    :::column-end:::
    :::column:::
        在此阶段，您将确定和预配许多 Azure 文件共享，在本地创建 Windows 服务器作为 StorSimple 设备替换，并为 Azure 文件同步配置该服务器。 
    :::column-end:::
:::row-end:::

### <a name="map-your-existing-namespaces-to-azure-file-shares"></a>将现有命名空间映射到 Azure 文件共享

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="deploy-azure-file-shares"></a>部署 Azure 文件共享

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

> [!TIP]
> 如果需要从 StorSimple 数据所在的当前区域更改 Azure 区域，则在要使用的新区域中预配 Azure 文件共享。 在预配保存 Azure 文件共享的存储帐户时，通过选择区域来确定该区域。 确保将在下面预配的 Azure 文件同步资源也位于同一新区域中。

### <a name="deploy-the-azure-file-sync-cloud-resource"></a>部署 Azure 文件同步云资源

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> 如果需要从 StorSimple 数据所在的当前区域更改 Azure 区域，则已为新区域中的 Azure 文件共享预配了存储帐户。 请确保在部署此存储同步服务时选择了同一区域。

### <a name="deploy-an-on-premises-windows-server"></a>部署本地 Windows 服务器

* 创建 Windows 服务器 2019 - 至少 2012R2 - 作为虚拟机或物理服务器。 还支持 Windows 服务器故障转移群集。 不要重复使用 StorSimple 8100 或 8600 前面可能具有的服务器。
* 预配或添加直接连接存储（与不支持 NAS 的 NAS 相比的 DAS）。

最佳做法是，为新的 Windows Server 提供与 StorSimple 8100 或 8600 设备在本地可用于缓存的同等或更大的存储空间。 如果 Windows 服务器的存储量与设备相同，则缓存体验应该类似（如果不是相同的话）。
您可以自此添加或删除 Windows 服务器中的存储。 这使您能够扩展本地卷大小和可用于缓存的本地存储量。

### <a name="prepare-the-windows-server-for-file-sync"></a>准备 Windows 服务器进行文件同步

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="configure-azure-file-sync-on-the-windows-server"></a>在 Windows 服务器上配置 Azure 文件同步

您注册的本地 Windows 服务器必须已准备就绪，并连接到 Internet 进行此过程。

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **请务必打开云分层！** 云分层是 AFS 功能，它允许本地服务器的存储容量少于存储在云中的存储容量，但具有完整的命名空间可用。 本地有趣的数据也缓存在本地，以快速的本地访问性能。 在此步骤中打开云分层的另一个原因是，我们不希望在此阶段同步文件内容，此时仅应移动命名空间。

## <a name="phase-4-configure-the-azure-vm-for-sync"></a>第 4 阶段：配置 Azure VM 进行同步

:::row:::
    :::column:::
        ![一种图像，用于说明早期概述图像的一部分，可帮助集中文章的本小节。](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-4.png)
    :::column-end:::
    :::column:::
        此阶段涉及 Azure VM，并安装了 iSCSI，这是第一个卷克隆。 在此阶段，您将通过 Azure 文件同步连接 VM，并开始从 StorSimple 卷克隆中移动文件的第一轮移动。
        
    :::column-end:::
:::row-end:::

您已经配置了本地服务器，该服务器将替换 StorSimple 8100 或 8600 设备，用于 Azure 文件同步。 

配置 Azure VM 是一个几乎相同的过程，还有一个步骤。 以下步骤将指导您完成此过程。

> [!IMPORTANT]
> 未**启用云分层后配置**Azure VM 非常重要！ 在整个迁移过程中，您将使用此服务器的卷与较新的卷克隆交换该卷。 云分层对 CPU 使用率没有好处和开销，您应该避免。

1. [部署 AFS 代理。（见上一节）](#prepare-the-windows-server-for-file-sync)
2. [为 Azure 文件同步准备 VM。](#get-the-vm-ready-for-azure-file-sync)
3. [配置同步](#configure-azure-file-sync-on-the-azure-vm)

### <a name="get-the-vm-ready-for-azure-file-sync"></a>为 Azure 文件同步准备好 VM

Azure 文件同步用于将文件从装载的 iSCSI StorSimple 卷移动到目标 Azure 文件共享。
在此迁移过程中，您将在同一驱动器号下将多个卷克隆装载到 VM。 必须将 Azure 文件同步配置为查看已装载为文件和文件夹的较新版本的下一个卷克隆，并更新通过 Azure 文件同步连接的 Azure 文件共享。 

> [!IMPORTANT]
> 为此，必须在配置 Azure 文件同步之前在服务器上设置注册表项。

1. 在 VM 的系统驱动器上创建新目录。 Azure 文件同步信息需要保留在那里，而不是在装载的卷克隆上。 例如： `"C:\syncmetadata"`
2. 打开注册并查找以下注册表配置单元：`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync`
3. 创建新的字符串类型键，名称为：***元数据根路径***
4. 设置在系统卷上创建的目录的完整路径，例如：`C:\syncmetadata"`

### <a name="configure-azure-file-sync-on-the-azure-vm"></a>在 Azure VM 上配置 Azure 文件同步

此步骤与上一节类似，该部分讨论了如何在本地服务器上配置 AFS。

区别在于，不得在此服务器上启用云分层，并且需要确保正确的文件夹已连接到正确的 Azure 文件共享。 否则，对 Azure 文件共享和数据内容的命名将不匹配，并且无法在不重新配置同步的情况下重命名云资源或本地文件夹。

请参阅上一[节关于如何在 Windows 服务器上配置 Azure 文件同步](#configure-azure-file-sync-on-the-windows-server)。

### <a name="step-4-summary"></a>步骤 4 摘要

此时，您将在通过 iSCSI 装载 StorSimple 卷克隆的 Azure VM 上成功配置 Azure 文件同步。
数据现在从 Azure VM 流向各种 Azure 文件共享，并且从该共享中会出现完全疲惫的命名空间。

> [!IMPORTANT]
> 确保此时未对 Windows 服务器进行任何更改或授予用户访问权限。

通过 Azure VM 移动到 Azure 文件共享的初始卷克隆数据可能需要很长时间，可能需要数周时间。 估计这需要时间是棘手的，取决于许多因素。 最显著的是 Azure VM 访问 StorSimple 卷上文件的速度，以及 Azure 文件同步处理需要同步的文件和文件夹的速度。 

根据经验，我们可以假设带宽（因此实际数据大小）起着从属作用。 此或任何后续迁移轮采用的时间主要取决于每秒可处理的项目数。 因此，例如，具有 100，000 个文件和文件夹的 1 TiB 很可能完成得慢于只有 50，000 个文件和文件夹的 1 TiB。

## <a name="phase-5-iterate-through-multiple-volume-clones"></a>第 5 阶段：通过多个卷克隆迭代

:::row:::
    :::column:::
        ![一种图像，用于说明早期概述图像的一部分，可帮助集中文章的本小节。](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-5.png)
    :::column-end:::
    :::column:::
        如前一阶段所述，初始同步可能需要很长时间。 您的用户和应用程序仍在访问本地 StorSimple 8100 或 8600 设备。 这意味着更改正在累积，并且随着实时数据与初始卷克隆之间的更大增量，您当前正在迁移窗体。 在本节中，您将学习如何通过使用多个卷克隆并告知何时完成同步来最大程度地减少停机时间。
    :::column-end:::
:::row-end:::

遗憾的是，迁移过程不是即时的。 这意味着上述增量对实时数据是一个不可避免的后果。 好消息是，您可以重复安装新卷克隆的过程。 每个卷克隆的增量将逐渐变小。 因此，最终，同步将在您认为可以接受的用户和应用程序脱机以剪切到本地 Windows 服务器的持续时间内完成。

重复以下步骤，直到同步完成足够快，让您放心地使用户和应用程序脱机：

1. [确定给定卷克隆的同步已完成。](#determine-when-sync-is-done)
2. [获取新的卷克隆并将其装载到 8020 虚拟设备。](#the-next-volume-clones)
3. [确定同步何时完成。](#determine-when-sync-is-done)
4. [截止策略](#cut-over-strategy)

### <a name="the-next-volume-clones"></a>下一个卷克隆

本文前面讨论了使用卷克隆。
此阶段有两个操作：

1. [获取卷克隆](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)
2. [安装该卷克隆（见上文）](#use-the-volume-clone)

### <a name="determine-when-sync-is-done"></a>确定同步何时完成

同步完成后，您可以停止时间测量，并确定是否需要重复获取卷克隆并安装它的过程，或者与上次卷克隆的同步是否足够小。

为了确定同步是否完成：

1. 打开事件查看器并导航到**应用程序和服务**
2. 导航并打开**Microsoft_文件同步\代理\遥测**
3. 查找与已完成的同步会话对应的最新**事件 9102**
4. 选择**详细信息**并确认**同步方向**值为 **"上载"**
5. 检查**HResult**并确认它显示**0**。 这意味着同步会话成功。 如果 HResult 是非零值，则同步期间出错。如果**PerItemErrorCount**大于 0，则某些文件或文件夹未正确同步。 有可能 HResult 为 0，但 PerItemErrorCount 大于 0。 此时，您无需担心 PerItemErrorCount。 稍后我们将捕获这些文件。 如果此错误计数很大，则数千个项目将与客户支持联系，并要求连接到 Azure 文件同步产品组，以便直接指导最佳的下一阶段。
6. 检查以查看多个 9102 事件，并连续出现 HResult 0。 这表示此卷克隆的同步已完成。

### <a name="cut-over-strategy"></a>截止策略

1. 确定来自卷克隆的同步现在是否足够快。 （增量足够小。
2. 使 StorSimple 设备脱机。
3. 最后的RoboCopy。

测量时间并确定最近卷克隆的同步是否可以在足够小的时间窗口内完成，这样您才能承受系统中的停机时间。

现在是时候禁用用户访问 StorSimple 设备了。 不再更改。 停机时间已经开始。
您需要使产品保持联机并连接，但现在必须防止对产品进行更改。

在第 6 阶段中，您将赶上自上次卷克隆以来的实时数据中的任何增量。

## <a name="phase-6-a-final-robocopy"></a>第 6 阶段：最终 RoboCopy

此时，本地 Windows 服务器与 StorSimple 8100 或 8600 设备之间存在两种差异：

1. 可能有一些文件尚未同步（请参阅上面的事件日志中的**PerItem 错误**）
2. StorSimple 设备具有填充缓存，而 Windows 服务器只是一个命名空间，此时本地没有存储任何文件内容。

![一种图像，用于说明早期概述图像的一部分，可帮助集中文章的本小节。](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-6.png)

我们可以将 Windows Server 的缓存带到设备的状态，并确保没有文件留下最终的 RoboCopy。

> [!CAUTION]
> 您遵循的 RoboCopy 命令必须完全按照下文所述。 我们只想复制本地文件和以前未通过卷克隆同步方法移动的文件。 我们可以解决他们以后在迁移完成后没有同步的问题。 （请参阅[Azure 文件同步故障排除](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)。 文件名中很可能无法打印的字符，删除时不会错过这些字符。

RoboCopy 命令：

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
      允许 RoboCopy 只考虑源（StorSimple 设备）和目标（Windows Server 目录）之间的增量。
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

您应该将此 RoboCopy 命令作为 Windows Server 上每个目录的目标运行，该目录已配置到 Azure 文件的文件同步。

您可以并行运行这些命令中的多个。
完成此 RoboCopy 步骤后，您可以允许用户和应用程序像之前使用 StorSimple 设备一样访问 Windows 服务器。

请参阅 robocopy 日志文件，查看文件是否已留有。 如果问题仍然存在，在大多数情况下，您可以在迁移完成后解决这些问题，并且您的用户和应用已重新驻留在您的 Windows 服务器。 如果需要修复任何问题，在阶段 7 之前执行此操作。

可能需要在 Windows 服务器上创建之前在 StorSimple 数据上拥有的 SMB 共享。 您可以预先加载此步骤，并提前执行此操作，以免在此处浪费时间，但必须确保在这一点之前，Windows 服务器上不会发生对文件的更改。

如果您有 DFS-N 部署，则可以将 DFN 命名空间指向新的服务器文件夹位置。 如果您没有 DFS-N 部署，并且在本地使用 Windows 服务器面对 8100 8600 设备，则可以将该服务器从域中取下，并将新 Windows 服务器与 AFS 联接到域中，为其指定与旧服务器相同的服务器名称和相同的共享名称，则新服务器的截止时间对用户保持透明组策略或脚本。

## <a name="phase-7-deprovision"></a>第 7 阶段：取消预配

在最后阶段，您通过多个卷克隆进行了迭代，并最终在使 StorSimple 设备脱机后，能够减少用户对新 Windows 服务器的访问。

您现在可以开始取消预配不必要的资源。
在开始之前，最佳做法是观察生产中的新 Azure 文件同步部署。 这为您提供了修复可能遇到的任何问题的选项。

满足并观察 AFS 部署至少几天后，可以按此顺序开始取消预配资源：

1. 关闭用于通过文件同步将数据从卷克隆移动到 Azure 文件共享的 Azure VM。
2. 转到 Azure 中的存储同步服务资源，然后取消注册 Azure VM。 这将从所有同步组中删除它。

    > [!WARNING]
    > **确保选择正确的机器。** 您已关闭云 VM，这意味着它应显示为注册服务器列表中的唯一脱机服务器。 在此步骤中，您不得选择本地 Windows 服务器，这样做将取消注册它。

3. 删除 Azure VM 及其资源。
4. 禁用 8020 虚拟 StorSimple 设备。
5. 取消预配 Azure 中的所有 StorSimple 资源。
6. 从数据中心拔下 StorSimple 物理设备。

您的迁移已完成。

## <a name="next-steps"></a>后续步骤

更加熟悉 Azure 文件同步。特别是具有云分层策略的灵活性。

如果在 Azure 门户或早期事件中看到某些文件永久未同步，请查看故障排除指南，了解解决这些问题的步骤。

* [Azure 文件同步概述：aka.ms/AFS](https://aka.ms/AFS)
* [云分层](storage-sync-cloud-tiering.md) 
* [Azure 文件同步故障排除指南](storage-sync-files-troubleshoot.md)
