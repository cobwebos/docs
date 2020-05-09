---
title: StorSimple 8000 系列迁移到 Azure 文件同步
description: 了解如何将 StorSimple 8100 或8600设备迁移到 Azure 文件同步。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 9091d3bd6b57f14bd12083319595cb855a0d42ee
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858713"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>StorSimple 8100 和8600迁移到 Azure 文件同步

StorSimple 8000 系列由8100或8600物理、本地设备和其云服务组件表示。 可以将这些设备中的数据迁移到 Azure 文件同步的环境。 Azure 文件同步是 StorSimple 设备可以迁移到的默认和策略长期 Azure 服务。

StorSimple 8000 系列[将在 12](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%208000%20Series)月2022日到期。 请务必尽快开始规划迁移。 本文提供成功迁移到 Azure 文件同步所需的背景知识和迁移步骤。 

## <a name="azure-file-sync"></a>Azure 文件同步

> [!IMPORTANT]
> Microsoft 致力于帮助客户完成其迁移。 用于AzureFiles@microsoft.com自定义迁移计划的电子邮件，并在迁移过程中提供帮助。

Azure 文件同步是一种 Microsoft 云服务，基于两个主要组件：

* 文件同步和云分层。
* 文件共享是 Azure 中的本机存储，可通过多个协议（如 SMB 和文件 REST）进行访问。 Azure 文件共享相当于 Windows Server 上的文件共享，你可以将该文件共享作为网络驱动器进行本机装入。 它支持重要的文件保真方面，如属性、权限和时间戳。 使用 Azure 文件共享时，应用程序或服务不再需要解释存储在云中的文件和文件夹。 你可以通过熟悉的协议和客户端（如 Windows 文件资源管理器）以本机方式访问它们。 这使得 Azure 文件在云中共享了理想且最灵活的方法来存储常规用途文件服务器数据以及某些应用程序数据。

本文重点介绍迁移步骤。 如果要在迁移之前详细了解 Azure 文件同步，我们建议阅读以下文章：

* [Azure 文件同步-概述](https://aka.ms/AFS "概述")
* [Azure 文件同步-部署指南](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>迁移目标

目标是保证生产数据的完整性并保证可用性。 后者需要将停机时间保持在最少，使其适应或仅略超过定期维护时段。

## <a name="storsimple-8000-series-migration-path-to-azure-file-sync"></a>StorSimple 8000 系列到 Azure 文件同步的迁移路径

运行 Azure 文件同步代理需要本地 Windows Server。 Windows Server 可以是至少一个2012R2 服务器，但理想情况下为 Windows Server 2019。

有大量的备用迁移路径，并且它将创建的项目太长，可以记录所有这些路径，并说明它们在路由上出现风险或缺点的原因，我们建议将其作为本文的最佳实践。

![StorSimple 8000 系列迁移阶段概述](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-overview.png "StorSimple 8000 系列迁移路由概述本文后面的阶段。")

上图描述了对应于本文各部分的阶段。
我们使用云端迁移，以避免不必要地重新调用本地 StorSimple 设备上的文件。 此方法可避免影响本地缓存行为或网络带宽使用，其中的任何一个都可能会影响你的生产工作负荷。
云端迁移对你的数据的快照（卷克隆）进行操作。 因此，在迁移结束之前，你的生产数据与此过程无关。 从本质上来说，这是一种备份，使迁移安全且易于重复，但你会遇到任何问题。

## <a name="considerations-around-existing-storsimple-backups"></a>有关现有 StorSimple 备份的注意事项

StorSimple 允许以卷克隆的形式进行备份。 本文使用新的卷克隆来迁移活动文件。
如果除实时数据外还需要迁移备份，则本文中的所有指南仍适用。 唯一的区别在于，您将从需要迁移的最早备份卷克隆开始，而不是从新的卷克隆开始。

顺序如下：

* 确定必须迁移的卷克隆的最小集。 建议尽可能将此列表保持在最低限度，因为迁移的备份越多，整个迁移过程所需的时间就越长。
* 完成迁移过程时，请从要迁移的最早卷克隆开始，并在每个后续迁移中使用下一个最早的迁移。
* 每个卷克隆迁移完成后，必须使用 Azure 文件共享快照。 [Azure 文件共享快照](storage-snapshots-files.md)是指如何保存 Azure 文件共享的文件和文件夹结构的时间点备份。 迁移完成后，你将需要这些快照，以确保你在迁移过程中完成每个卷克隆的保留版本。
* 确保将 Azure 文件共享快照用于由同一 StorSimple 卷提供服务的所有 Azure 文件共享。 卷克隆在卷级别上，Azure 文件共享快照位于共享级别。 完成卷克隆的迁移后，必须在每个 Azure 文件共享上拍摄共享快照。
* 重复卷克隆的迁移过程，并在每个卷克隆后拍摄共享快照，直到捕获到实时数据的快照。 以下阶段介绍了迁移卷克隆的过程。 

如果根本不需要移动备份，并且在仅迁移实时数据后，可以在 Azure 文件共享端开始新的备份链，那么，降低迁移的复杂性和迁移所需的时间会很有用。 你可以决定是否移动备份，以及每个卷（而不是每个共享）在 StorSimple 中的备份量。

## <a name="phase-1-get-ready"></a>阶段1：准备就绪

:::row:::
    :::column:::
        ![说明前面的概述图像部分的图像，该图像可帮助重点介绍文章的此子节。](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-1.png)
    :::column-end:::
    :::column:::
        迁移的基础是一个卷克隆和一个称为 StorSimple 8020 的虚拟云设备。
此阶段重点介绍如何在 Azure 中部署这些资源。
    :::column-end:::
:::row-end:::

### <a name="deploy-a-storsimple-8020-virtual-appliance"></a>部署 StorSimple 8020 虚拟设备

部署云设备需要安全、联网和其他一些注意事项。

> [!IMPORTANT]
> 以下指南包含一些不必要的部分。 阅读并按照文章开始，直到出现 "步骤 3"。 然后返回到本文。 此时，无需在该指南中完成 "步骤 3" 或任何内容。

[部署 StorSimple 8020 虚拟设备](../../storsimple/storsimple-8000-cloud-appliance-u2.md)

### <a name="determine-a-volume-clone-to-use"></a>确定要使用的卷克隆

准备好开始迁移时，第一步是使用新的卷克隆，就像备份时一样，它会捕获 StorSimple 云存储的当前状态。 为拥有的每个 StorSimple 卷拍摄一个克隆。
如果需要移动备份，则所使用的第一个卷克隆不是新创建的克隆，而是需要迁移的最早卷克隆（最早的备份）。
有关详细指南，请参阅["现有 StorSimple 备份的注意事项"](#considerations-around-existing-storsimple-backups)部分。

> [!IMPORTANT]
> 以下指南包含一些不必要的部分。 阅读并按照链接到部分中概述的步骤进行操作。 然后返回到本文。 不需要执行 "后续步骤" 部分。

[创建卷的克隆](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)

### <a name="use-the-volume-clone"></a>使用卷克隆

阶段1的最后一个阶段是创建所选的卷克隆，该克隆可在 Azure 中的8020虚拟设备上使用。

> [!IMPORTANT]
> 以下指南包含所需的步骤，但也包含用于格式化卷的最后一条指令。 **不格式化卷**阅读并按照从上到的链接到 "第7部分" 的说明进行操作，直到出现说明： "10。 若要格式化简单卷，请 ... " 请在执行此步骤之前停止，并返回到本文。

[在 Azure 中的8020虚拟设备上装载卷克隆](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-1-summary"></a>阶段1摘要

完成阶段1后，你将完成以下操作：

* 在 Azure 中部署了 StorSimple 8020 虚拟设备。
* 确定将开始迁移的卷克隆。
* 已将卷克隆（每个活动卷一个）装载到 Azure 中的 StorSimple 虚拟设备，并使其数据可供将来使用。

## <a name="phase-2-cloud-vm"></a>阶段2：云 VM

:::row:::
    :::column:::
        ![说明前面的概述图像部分的图像，该图像可帮助重点介绍文章的此子节。](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-2.png)
    :::column-end:::
    :::column:::
        在 Azure 中的 StorSimple 8020 虚拟设备上提供初始克隆后，现在可以预配 VM，并通过 iSCSI 向该 VM 公开卷克隆（或多个卷克隆）。
    :::column-end:::
:::row-end:::

### <a name="deploy-an-azure-vm"></a>部署 Azure VM

Azure 中的 Windows Server 虚拟机与 StorSimple 8020 一样，只是在迁移过程中需要的临时基础结构。
部署的 VM 的配置主要取决于要同步的项目（文件和文件夹）的数量。 如果有任何问题，建议采用更高的性能配置。

单个 Windows 服务器最多可以同步30个 Azure 文件共享。
您决定的规范需要包含每个共享/路径或 StorSimple 卷的根目录并对项进行计数（文件和文件夹）。

数据的总体大小不太瓶颈-它是定制计算机规格所需的项数。

* [了解如何根据需要同步的项目（文件和文件夹）的数量调整 Windows Server 的大小。](storage-sync-files-planning.md#recommended-system-resources)

    **请注意：** 之前链接的文章显示了一个表，其中包含一个服务器内存（RAM）范围。 面向 Azure VM 的较大数量。 你可以为本地计算机提供较小的数字。

* [了解如何部署 Windows 服务器 VM。](../../virtual-machines/windows/quick-create-portal.md)

> [!IMPORTANT]
> 请确保将 VM 部署在与 StorSimple 8020 虚拟设备相同的 Azure 区域中。 如果在此迁移过程中，你还需要从当前存储的区域更改云数据的区域，则可以在稍后预配 Azure 文件共享时执行此操作。

> [!IMPORTANT]
> 通常，本地 Windows Server 用于本地 StorSimple 设备的前端。 在此类配置中，可以在该 Windows Server 上启用 "[重复数据删除](https://docs.microsoft.com/windows-server/storage/data-deduplication/install-enable)" 功能。 **如果对 StorSimple 数据使用重复数据删除，请确保同时启用此 Azure VM 上的重复数据删除。** 不要将此文件级别的重复数据删除与 StorSimples 内置的块级删除重复，因此无需执行任何操作。

> [!IMPORTANT]
> 若要优化性能，请为云 VM 部署**快速操作系统磁盘**。 你将在 OS 磁盘上存储所有数据卷的同步数据库。 此外，请确保创建**大型 OS 磁盘**。 根据 StorSimple 卷上的项目（文件和文件夹）的数量，操作系统磁盘可能需要**几百个 GiB**的空间来容纳同步数据库。

### <a name="expose-the-storsimple-8020-volumes-to-the-azure-vm"></a>向 Azure VM 公开 StorSimple 8020 卷

在此阶段中，你要将一个或多个 StorSimple 卷从8020虚拟设备通过 iSCSI 连接到已预配的 Windows Server VM。

> [!IMPORTANT]
> 对于以下文章，只完成为**云设备获取专用 IP**并**连接 iSCSI**部分并返回到本文。

1. [获取云设备的专用 IP](../../storsimple/storsimple-8000-cloud-appliance-u2.md#get-private-ip-for-the-cloud-appliance)
2. [通过 iSCSI 进行连接](../../storsimple/storsimple-8000-deployment-walkthrough-u2.md#step-7-mount-initialize-and-format-a-volume)

### <a name="phase-2-summary"></a>阶段2摘要

完成阶段2后，你已完成了以下操作： 

* 在与8020虚拟 StorSimple 设备相同的区域中预配 Windows Server VM
* 通过 iSCSI 将所有适用的卷从8020公开到 Windows Server VM。
* 你现在应会看到 "文件和文件夹" 内容，当你在装入的卷上的服务器 VM 上使用文件资源管理器时。

仅当你为需要迁移的所有卷完成这些步骤时，才会继续执行阶段3。

## <a name="phase-3-set-up-azure-file-shares-and-get-ready-for-azure-file-sync"></a>阶段3：设置 Azure 文件共享并准备好 Azure 文件同步

:::row:::
    :::column:::
        ![说明前面的概述图像部分的图像，该图像可帮助重点介绍文章的此子节。](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-3.png)
    :::column-end:::
    :::column:::
        在此阶段中，你将确定和预配多个 Azure 文件共享，创建作为 StorSimple 设备替换的本地 Windows Server，并将该服务器配置为 Azure 文件同步。 
    :::column-end:::
:::row-end:::

### <a name="map-your-existing-namespaces-to-azure-file-shares"></a>将现有命名空间映射到 Azure 文件共享

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="deploy-azure-file-shares"></a>部署 Azure 文件共享

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

> [!TIP]
> 如果需要更改 StorSimple 数据所在的当前区域的 Azure 区域，请在要使用的新区域中预配 Azure 文件共享。 设置保存 Azure 文件共享的存储帐户时，可以通过选择区域来确定区域。 请确保在此相同的新区域中，还可以设置以下所 Azure 文件同步资源。

### <a name="deploy-the-azure-file-sync-cloud-resource"></a>部署 Azure 文件同步云资源

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> 如果需要更改 StorSimple 数据所在的当前区域的 Azure 区域，请在新区域中为 Azure 文件共享设置存储帐户。 在部署此存储同步服务时，请确保你选择了该区域。

### <a name="deploy-an-on-premises-windows-server"></a>部署本地 Windows Server

* 创建 Windows Server 2019-以最小2012R2 为虚拟机或物理服务器。 还支持 Windows Server 故障转移群集。 不要重复使用该服务器，你可能已前方 StorSimple 8100 或8600。
* 预配或添加直接附加存储（与 NAS 相比，不支持 NAS。

与 StorSimple 8100 或8600设备本地可用于缓存相比，最佳做法是为新的 Windows 服务器提供相等或更大的存储量。 如果你使用的是与使用 StorSimple 设备相同的存储量，则将使用 Windows Server，如果该设备具有与设备相同的存储量，则缓存体验应类似（如果不相同）。
你可以根据需要在 Windows Server 中添加或删除存储。 这使你可以缩放本地卷大小和可用于缓存的本地存储量。

### <a name="prepare-the-windows-server-for-file-sync"></a>准备 Windows Server 以进行文件同步

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="configure-azure-file-sync-on-the-windows-server"></a>在 Windows Server 上配置 Azure 文件同步

已注册的本地 Windows 服务器必须已准备就绪，并且已连接到 internet 以执行此过程。

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **确保启用云分层！** 云分层是一项 AFS 功能，它使本地服务器的存储容量比存储在云中的存储容量少，同时还提供完整的命名空间。 本地关注的数据也会在本地缓存，以实现快速的本地访问性能。 启用云分层的另一个原因是我们不想在此阶段同步文件内容，此时只应移动命名空间。

## <a name="phase-4-configure-the-azure-vm-for-sync"></a>阶段4：配置要同步的 Azure VM

:::row:::
    :::column:::
        ![说明前面的概述图像部分的图像，该图像可帮助重点介绍文章的此子节。](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-4.png)
    :::column-end:::
    :::column:::
        此阶段涉及到 Azure VM，其中包含已装入 iSCSI 的第一卷克隆。 在此阶段中，你将通过 Azure 文件同步连接 VM，并从 StorSimple 卷克隆开始第一轮移动文件。
        
    :::column-end:::
:::row-end:::

你已经配置了本地服务器，将替换 StorSimple 8100 或8600设备，以便 Azure 文件同步。 

配置 Azure VM 几乎完全相同，只是另外一个步骤。 以下步骤将引导你完成此过程。

> [!IMPORTANT]
> 重要的是，Azure VM**未配置云分层功能**非常重要！ 在整个迁移过程中，会将此服务器的卷与较新的卷克隆交换。 云分层不会带来任何好处和 CPU 使用率的开销。

1. [部署 AFS 代理。（请参阅上一节）](#prepare-the-windows-server-for-file-sync)
2. [正在为 VM 准备好 Azure 文件同步。](#get-the-vm-ready-for-azure-file-sync)
3. [配置同步](#configure-azure-file-sync-on-the-azure-vm)

### <a name="get-the-vm-ready-for-azure-file-sync"></a>获取虚拟机的准备就绪 Azure 文件同步

Azure 文件同步用于将文件从已装载的 iSCSI StorSimple 卷移动到目标 Azure 文件共享。
在此迁移过程中，你将在同一驱动器号下将多个卷克隆装载到 VM 中。 必须将 Azure 文件同步配置为查看已装载为较新版本的文件和文件夹的下一个卷克隆，并更新通过 Azure 文件同步连接的 Azure 文件共享。 

> [!IMPORTANT]
> 为此，必须先在服务器上设置注册表项，然后才能配置 Azure 文件同步。

1. 在 VM 的系统驱动器上创建新目录。 需要将 Azure 文件同步信息保存在该卷中，而不是保存在已装载的卷克隆中。 例如： `"C:\syncmetadata"`
2. 打开 regedit，找到以下注册表配置单元：`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync`
3. 创建名为 "String" 的新密钥，名为： ***MetadataRootPath***
4. 设置在系统卷上创建的目录的完整路径，例如：`C:\syncmetadata"`

### <a name="configure-azure-file-sync-on-the-azure-vm"></a>在 Azure VM 上配置 Azure 文件同步

此步骤类似于上一节，讨论如何在本地服务器上配置 AFS。

差别在于，您不能在此服务器上启用云分层，而且需要确保正确的文件夹连接到正确的 Azure 文件共享。 否则，你对 Azure 文件共享和数据内容的命名不匹配，因此无法重命名云资源或本地文件夹，而无需重新配置同步。

请参阅上一[节，了解如何在 Windows Server 上配置 Azure 文件同步](#configure-azure-file-sync-on-the-windows-server)。

### <a name="step-4-summary"></a>步骤4摘要

此时，你已成功地在 Azure VM 上配置了通过 iSCSI 装载了 StorSimple 卷克隆的 Azure 文件同步。
数据现在从 Azure VM 流动到各种 Azure 文件共享，并且在本地 Windows Server 上出现完全被完全厌倦的命名空间。

> [!IMPORTANT]
> 请确保此时未进行任何更改，或对 Windows Server 授予了用户访问权限。

通过 Azure VM 迁移到 Azure 文件共享的初始卷克隆数据可能需要较长时间，可能需要几周的时间。 估计这将花费的时间很棘手，并取决于许多因素。 最值得注意的是，Azure VM 可以访问 StorSimple 卷上的文件，以及速度 Azure 文件同步处理需要同步的文件和文件夹的速度。 

根据经验，我们可以假设带宽-因此，实际数据大小会扮演从属角色。 此或任何后续的迁移轮的时间主要取决于每秒可处理的项数。 例如，对于包含100000个文件和文件夹的 TiB，其最有可能完成速度低于 1 TiB，只包含50000个文件和文件夹。

## <a name="phase-5-iterate-through-multiple-volume-clones"></a>阶段5：循环访问多个卷克隆

:::row:::
    :::column:::
        ![说明前面的概述图像部分的图像，该图像可帮助重点介绍文章的此子节。](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-5.png)
    :::column-end:::
    :::column:::
        如前一阶段中所述，初始同步可能需要较长时间。 你的用户和应用程序仍在访问本地 StorSimple 8100 或8600设备。 这意味着更改将会积累，在每天的实时数据和初始卷克隆之间存在较大的差异时，你当前正在迁移。 在本部分中，你将了解如何使用多个卷克隆来最大程度地减少停机时间，并告诉你何时完成同步。
    :::column-end:::
:::row-end:::

遗憾的是，迁移过程不是即时的。 这意味着，对实时数据的上述差异是不可避免的。 好消息是，可以重复装载新卷克隆的过程。 每个卷克隆的增量将逐渐缩小。 最终，同步将在一段时间内完成，你可以考虑将用户和应用脱机剪切到本地 Windows server。

重复以下步骤，直到同步完成速度足够快，使用户和应用脱机：

1. [确定给定卷克隆的同步已完成。](#determine-when-sync-is-done)
2. [获取一个新的卷克隆，并将其装载到8020虚拟设备。](#the-next-volume-clones)
3. [确定何时完成同步。](#determine-when-sync-is-done)
4. [截止策略](#cut-over-strategy)

### <a name="the-next-volume-clones"></a>下一个卷克隆

我们讨论过本文前面部分的卷克隆。
此阶段有两个操作：

1. [拍摄卷克隆](../../storsimple/storsimple-8000-clone-volume-u2.md#create-a-clone-of-a-volume)
2. [装载该卷克隆（参见上文）](#use-the-volume-clone)

### <a name="determine-when-sync-is-done"></a>确定何时完成同步

完成同步后，可以停止时间度量，确定是否需要重复执行卷克隆并装载卷克隆，或者如果最后一个卷克隆所用的时间是否足够小。

为了确定同步是否完成：

1. 打开事件查看器，导航到**应用程序和服务**
2. 导航并打开**Microsoft\FileSync\Agent\Telemetry**
3. 查找与已完成的同步会话对应的最近**事件 9102**
4. 选择 "**详细信息**" 并确认**SyncDirection**值为 "**上传**"
5. 检查**HResult**并确认它显示为**0**。 这意味着同步会话已成功。 如果 HResult 为非零值，则在同步过程中出现错误。如果**PerItemErrorCount**大于0，则某些文件或文件夹未正确同步。 有可能 HResult 为 0，但 PerItemErrorCount 大于 0。 此时，无需担心 PerItemErrorCount。 稍后我们将捕获这些文件。 如果此错误计数非常重要，则请与客户支持部门联系，并请求连接到 Azure 文件同步的产品组，以直接指导最佳、后续阶段。
6. 检查以查看行中的多个9102事件，其中 HResult 为0。 这表示此卷克隆的同步已完成。

### <a name="cut-over-strategy"></a>截止策略

1. 立即确定卷克隆的同步速度是否足够快。 （增量足够小。）
2. 使 StorSimple 设备脱机。
3. 最终 RoboCopy。

测量时间，确定最近的卷克隆中的同步是否可以在足够小的时间范围内完成，你可以在系统中承受停机。

现在，它可以禁用对 StorSimple 设备的用户访问。 无更多更改。 已开始停机。
你需要让设备处于联机和连接状态，但现在必须阻止对其进行更改。

在阶段6中，你将在上一次卷克隆后的实时数据中捕获任何增量。

## <a name="phase-6-a-final-robocopy"></a>阶段6：最终 RoboCopy

此时，本地 Windows Server 与 StorSimple 8100 或8600设备之间存在两个差异：

1. 可能存在尚未同步的文件（请参阅上面事件日志中的**PerItemErrors** ）
2. StorSimple 设备上填充了缓存，而不是 Windows Server，此时不会在本地存储任何文件内容的命名空间。

![说明前面的概述图像部分的图像，该图像可帮助重点介绍文章的此子节。](media/storage-files-migration-storsimple-shared/storsimple-8000-migration-phase-6.png)

我们可以将 Windows 服务器的缓存更新到设备的状态，并确保没有任何文件出现在最终 RoboCopy 后面。

> [!CAUTION]
> 必须遵循的 RoboCopy 命令是正确的，如下所述。 我们只想要复制本地文件和之前未通过卷克隆 + 同步方法移动的文件。 在迁移完成之后，我们可以解决以后不同步的问题。 （请参阅[Azure 文件同步故障排除](storage-sync-files-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)。 不会在删除时不会错过的文件名中最可能出现不可打印的字符。

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
      允许 RoboCopy 运行多线程。 默认值为8，最大值为128。
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:<file name>
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
      允许 RoboCopy 仅考虑源（StorSimple 设备）和目标（Windows Server 目录）之间的增量。
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

应为 Windows Server 上作为目标的每个目录运行此 RoboCopy 命令，并将文件同步配置为 Azure 文件。

可以并行运行其中的多个命令。
完成此 RoboCopy 步骤后，你可以允许用户和应用访问 Windows Server，就像以前在使用 StorSimple 设备一样。

请查阅 robocopy 日志文件，查看文件是否已遗留。 如果存在问题，则在大多数情况下，你可以在迁移完成之后解决这些问题，并将用户和应用重新托管到 Windows Server。 如果需要解决任何问题，请在阶段7之前执行此操作。

在之前，可能需要在 Windows Server 上创建 SMB 共享。 您可以在此之前加载此步骤并在之前执行此步骤，但您必须确保在此之后，Windows server 上不会对文件进行任何更改。

如果你有一个 DFS-N 部署，则可以将 DFN 命名空间指向新的服务器文件夹位置。 如果你没有使用 DFS N 进行部署，并且你在使用 Windows Server 在本地前端了 8100 8600 设备，则可以将该服务器从域中取出，并将新的 Windows Server 加入到域中，并为其提供与旧服务器相同的服务器名称和相同的共享名，然后向新服务器进行的剪切将对用户保持透明。、组策略或脚本。

## <a name="phase-7-deprovision"></a>阶段7：取消预配

在上一阶段中，你循环访问多个卷克隆，并最终能够在将 StorSimple 设备脱机后减少用户对新 Windows 服务器的访问。

你现在可以开始取消设置不必要的资源。
在开始之前，最佳做法是在生产环境中观察新的 Azure 文件同步部署。 这为你提供了解决你可能遇到的任何问题的选项。

一旦你满意并观察到你的 AFS 部署至少几天，你就可以按以下顺序开始预配资源：

1. 关闭已用于通过文件同步将数据从卷克隆移到 Azure 文件共享的 Azure VM。
2. 请在 Azure 中中转到存储同步服务资源，并取消注册 Azure VM。 这会将其从所有同步组中删除。

    > [!WARNING]
    > **确保选择正确的计算机。** 已关闭云 VM，这意味着它应显示为已注册服务器列表中的唯一脱机服务器。 你不能在此步骤中选择本地 Windows Server，这样做将取消注册。

3. 删除 Azure VM 及其资源。
4. 禁用 8020 virtual StorSimple 设备。
5. 取消预配 Azure 中的所有 StorSimple 资源。
6. 从数据中心拔出 StorSimple 物理设备。

迁移已完成。

## <a name="next-steps"></a>后续步骤

更熟悉 Azure 文件同步。尤其是云分层策略的灵活性。

如果在 Azure 门户中，或在以前的事件中看到某些文件未同步，请参阅故障排除指南以获取解决这些问题的步骤。

* [Azure 文件同步概述： aka.ms/AFS](https://aka.ms/AFS)
* [云分层](storage-sync-cloud-tiering.md) 
* [Azure 文件同步故障排除指南](storage-sync-files-troubleshoot.md)
