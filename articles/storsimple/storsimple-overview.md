---
title: "StorSimple 8000 系列解决方案概述 | Microsoft 文档"
description: "介绍 StorSimple 分层、设备、虚拟设备、服务和存储管理，并介绍了 StorSimple 中使用的关键术语。"
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: 7144d218-db21-4495-88fb-e3b24bbe45d1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 10/05/2016
ms.author: v-sharos@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: 78daa5a75b3414e2761333ea6ad91945596553c8
ms.openlocfilehash: 3575e4f22e17608de7f3b66e9dd577a1d4a4c148


---
# <a name="storsimple-8000-series-a-hybrid-cloud-storage-solution"></a>StorSimple 8000 系列：混合云存储解决方案
## <a name="overview"></a>概述
欢迎使用 Microsoft Azure StorSimple，它是集成的存储解决方案，用于管理本地设备与 Microsoft Azure 云存储之间的存储任务。 StorSimple 是一种经济高效、易于管理的存储区域网络 (SAN) 解决方案，可以消除与企业存储和数据保护相关的很多问题和开支。 它使用专有的 StorSimple 8000 系列设备，与云服务集成，并提供一套管理工具用于无缝查看包括云存储在内的所有企业存储。 （Microsoft Azure 网站上发布的 StorSimple 部署信息仅适用于 StorSimple 8000 系列设备。 如果使用的是 StorSimple 5000/7000 系列设备，请转到 [StorSimple 帮助](http://onlinehelp.storsimple.com/)。）

StorSimple 使用[存储分层](#automatic-storage-tiering)管理各种存储介质中存储的数据。 当前的工作集存储于固态硬盘 (SSD) 本地，使用频率较低的数据存储于硬盘驱动器 (HDD)，而存档数据则被推送到云中。 此外，StorSimple 使用删除重复和压缩来减少数据占用的存储空间量。 有关详细信息，请转到[删除重复和压缩](#deduplication-and-compression)。 有关其他关键术语和 StorSimple 8000 系列文档中所使用概念的定义，请转到本文末尾的 [StorSimple 术语](#storsimple-terminology)。

使用 StorSimple Update 2，可将相应的卷标识为“本地固定”，以确保主数据保留在本地设备中，而不会分层到云中。 这样，你就可以在本地固定卷上运行对云延迟敏感的工作负荷（如 SQL 和虚拟机工作负荷），同时继续使用云进行备份。 有关本地固定卷的详细信息，请参阅[使用 StorSimple Manager 服务管理卷](storsimple-manage-volumes-u2.md)。 

借助 Update 2，还可以创建 StorSimple 虚拟设备，充分利用 Azure 高级存储所提供的低延迟和高性能。 有关 StorSimple 高级虚拟设备的详细信息，请参阅[在 Azure 中部署和管理 StorSimple 虚拟设备](storsimple-virtual-device-u2.md)。 有关 Azure 高级存储的详细信息，请转到[高级存储：Azure 虚拟机工作负载的高性能存储](../storage/storage-premium-storage.md)。

除存储管理外，通过 StorSimple 数据保护功能，也可以创建按需备份和计划备份，然后将它们存储在本地或云中。 以增量快照形式进行备份意味着可快速创建和还原这些备份。 在灾难恢复方案中，云快照可能非常重要，因为它们替换辅助存储系统（如磁带备份），并且允许将数据还原到数据中心或其他站点上（如有必要）。

![video icon](./media/storsimple-overview/video_icon.png) 观看此视频，了解 Microsoft Azure StorSimple 的简要介绍。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/StorSimple-Hybrid-Cloud-Storage-Solution/player]
> 
> 

## <a name="why-use-storsimple"></a>为什么使用 StorSimple？
下表介绍了 Microsoft Azure StorSimple 提供的某些主要优势。

| 功能 | 优势 |
| --- | --- |
| 透明集成 |Microsoft Azure StorSimple 使用 iSCSI 协议以不可见的方式链接数据存储设施。 这样可以确保存储在云中、数据中心或远程服务器上的数据看起来好像存储在单个位置。 |
| 降低存储成本 |Microsoft Azure StorSimple 分配足够的本地存储或云存储以满足当前需求，仅在必要时才扩展云存储。 它可以通过消除相同数据的冗余版本（删除重复）和使用压缩，进一步降低存储需求和开支。 |
| 简化存储管理 |Microsoft Azure StorSimple 提供了系统管理工具，可用于配置和管理存储在本地、远程服务器或云中的数据。 此外，还可以从 Microsoft 管理控制台 (MMC) 管理单元管理备份和还原功能。 StorSimple 提供了独立的可选接口，可用于将 StorSimple 管理和数据保护服务扩展到存储在 SharePoint 服务器上的内容。 |
| 改进灾难恢复和合规性 |Microsoft Azure StorSimple 不需要过长的恢复时间。 相反，它仅在需要时才还原数据。 这意味着正常运行能够得以继续，而很少发生中断。 此外，你可以配置策略以指定备份计划和数据保留期。 |
| 数据移动性 |上传到 Microsoft Azure 云服务的数据可从其他站点访问，以便进行恢复和迁移。 此外，你可以使用 StorSimple 来配置在 Microsoft Azure 中运行的虚拟机 (VM) 上的 StorSimple 虚拟设备。 然后，VM 可使用虚拟设备来访问存储的数据，以便进行测试或恢复。 |
| 对其他云服务提供商的支持 |具有软件更新 1 或更高版本的 StorSimple 8000 系列支持带 RRS 的 Amazon S3、HP 和 OpenStack 云服务，以及 Microsoft Azure。 （为进行设备管理，仍需要 Microsoft Azure 存储帐户。）有关详细信息，请转到 [Update 1.2 中的新增功能](storsimple-update1-release-notes.md#whats-new-in-update-12)。 |
| 业务连续性 |Update 1 或更高版本提供了新的迁移功能，允许 StorSimple 5000-7000 系列用户将他们的数据迁移到 StorSimple 8000 系列设备。 |
| 在 Azure 政府门户中的可用性 |StorSimple Update 1 或更高版本可用于 Azure 政府门户。 有关详细信息，请参阅[在政府门户中部署本地 StorSimple 设备](storsimple-deployment-walkthrough-gov.md)。 |
| 数据保护和可用性 |除了本地冗余存储 (LRS) 和异地冗余存储 (GRS)，具有 Update 1 或更高版本的 StorSimple 8000 系列还支持区域冗余存储空间 (ZRS)。 请参阅[这篇有关 Azure 存储冗余选项的文章](https://azure.microsoft.com/documentation/articles/storage-redundancy/)，了解 ZRS 的详细信息。 |
| 对关键应用程序的支持 |借助 StorSimple Update 2，可将相应的卷标识为本地固定卷。 此功能可确保不会将关键应用程序所需的数据分层到云中。 本地固定卷不受云延迟或连接问题影响。 有关本地固定卷的详细信息，请参阅[使用 StorSimple Manager 服务管理卷](storsimple-manage-volumes-u2.md)。 |
| 低延迟和高性能 |借助 StorSimple Update 2 可以创建充分利用 Azure 高级存储的高性能和低延迟的虚拟设备。 有关 StorSimple 高级虚拟设备的详细信息，请参阅[在 Azure 中部署和管理 StorSimple 虚拟设备](storsimple-virtual-device-u2.md)。 |

![video icon](./media/storsimple-overview/video_icon.png)观看[本视频](https://www.youtube.com/watch?v=4MhJT5xrvQw&feature=youtu.be)，了解 StorSimple 8000 系列功能和优势的概述。

## <a name="storsimple-components"></a>StorSimple 组件
Microsoft Azure StorSimple 解决方案包括以下组件：

* **Microsoft Azure StorSimple 设备** – 是一个本地混合存储阵列，包含 SSD 和 HDD，还具有冗余控制器和自动故障转移功能。 控制器管理存储分层，将当前使用的数据（也就是热数据）放置在本地存储中（在设备或本地服务器上），同时将使用频率较低的数据移动到云。
* **StorSimple 虚拟设备** – 这是 StorSimple 设备的软件版本，复制了物理混合存储设备的体系结构和多数功能。 StorSimple 虚拟设备在 Azure 虚拟机中的单个节点上运行。 Update 2 及更高版本中提供了高级虚拟设备，可充分利用 Azure 高级存储性能。
* **StorSimple Manager 服务** – Azure 经典门户的扩展，让你能够从单个 Web 界面管理 StorSimple 设备或 StorSimple 虚拟设备。 你可以使用 StorSimple 管理器服务来创建和管理服务、查看和管理设备、查看警报、管理卷以及查看和管理备份策略和备份目录。
* **Windows PowerShell for StorSimple** – 一个命令行接口，可用于管理 StorSimple 设备。 Windows PowerShell for StorSimple 具有多种功能，让你能够注册 StorSimple 设备、配置设备上的网络接口、安装特定类型的更新、通过访问支持会话为设备排除故障，以及更改设备状态。 可以通过连接到串行控制台或通过使用 Windows PowerShell 远程处理来访问 Windows PowerShell for StorSimple。
* **Azure PowerShell StorSimple cmdlet** - Windows PowerShell cmdlet 的一个集合，使你能够自动从命令行执行服务级别和迁移任务。 有关适用于 StorSimple 的 Azure Powershell cmdlet 的详细信息，请转到 [cmdlet 参考](https://msdn.microsoft.com/library/dn920427.aspx)。
* **StorSimple Snapshot Manager** – 一个 MMC 管理单元，它使用卷组和 Windows 卷影复制服务来生成应用程序一致性备份。 此外，你还可以使用 StorSimple 快照管理器来创建备份计划，并克隆或还原卷。 
* **StorSimple Adapter for SharePoint** – 一种工具，可将 Microsoft Azure StorSimple 存储和数据保护透明地扩展到 SharePoint 服务器场，同时让用户能够从 SharePoint 中心管理门户查看和管理 StorSimple 存储。

下图提供了 Microsoft Azure StorSimple 体系结构和组件的高级视图。

![StorSimple 体系结构](./media/storsimple-overview/overview-big-picture.png)

以下各节更详细地介绍了其中的每个组件，并解释该解决方案如何排列数据、分配存储并促进存储管理和数据保护。 最后一节提供了一些重要术语的定义和与 StorSimple 组件及其管理相关的概念。

## <a name="storsimple-device"></a>StorSimple 设备
Microsoft Azure StorSimple 设备是提供主存储和其上所存储数据的 iSCSI 访问权限的本地混合存储阵列。 它管理与云存储的通信，并有助于确保 Microsoft Azure StorSimple 解决方案中存储的所有数据的安全性和机密性。

StorSimple 设备包括 SSD 和硬盘驱动器 HDD，并且支持群集和自动故障转移。 它包含一个共享处理器、一个共享存储和两个镜像控制器。 每个控制器都提供以下内容：

* 与主机计算机的连接
* 最多六个用于连接到局域网 (LAN) 的网络端口
* 硬件监视
* 非易失性随机存取内存 (NVRAM)，即使断电也可保留信息
* 群集感知更新，能够管理故障转移群集中各服务器的软件更新，以便更新对服务可用性影响最小或没有影响
* 群集服务，与后端群集的运行方式类似，可提供高可用性并将 HDD 或 SSD 出现故障或脱机时可能产生的负面影响降至最低

在任一时间点，只有一个控制器处于活动状态。 如果活动控制器出现故障，第二个控制器将自动变为活动状态。 

有关详细信息，请转到 [StorSimple 硬件组件和状态](storsimple-monitor-hardware-status.md)。

## <a name="storsimple-virtual-device"></a>StorSimple 虚拟设备
可使用 StorSimple 创建一个虚拟设备，以复制物理混合存储设备的体系结构和功能。 StorSimple 虚拟设备在 Azure 虚拟机中的单个节点上运行。 （只能在 Azure 虚拟机上创建虚拟设备。 不能在 StorSimple 设备或本地服务器上创建虚拟设备。） 

虚拟设备具有以下功能：

* 它类似于物理设备，并可向云中的虚拟机提供 iSCSI 接口。 
* 可在云中创建无数虚拟设备，并根据需要将其打开和关闭。 
* 它可以帮助模拟灾难恢复、开发和测试情形下的本地环境，并可帮助从备份中进行项目级检索。 

借助 Update 2 和更高版本，StorSimple 虚拟设备在以下两个模型中可用：8010 设备（以前称为 1100 模型）和 8020 设备。 8010 设备的最大容量为 30 TB。 充分利用 Azure 高级存储的 8020 设备的最大容量为 64 TB。 （在本地层中，Azure 高级存储将数据存储在 SSD 上，而标准存储将数据存储在 HDD 上。）请注意，必须具有 Azure 高级存储帐户才能使用高级存储。 有关高级存储的详细信息，请转到[高级存储：适用于 Azure 虚拟机工作负荷的高性能存储](../storage/storage-premium-storage.md)。

有关 StorSimple 虚拟设备的详细信息，请转到[在 Azure 中部署和管理 StorSimple 虚拟设备](storsimple-virtual-device-u2.md)。

## <a name="storsimple-manager-service"></a>StorSimple 管理器服务
Microsoft Azure StorSimple 提供一个基于 Web 的用户界面（StorSimple 管理器服务），可用于集中管理数据中心和云存储。 可以使用 StorSimple 管理器服务执行以下任务：

* 配置 StorSimple 设备的系统设置。
* 配置和管理 StorSimple 设备的安全设置。
* 配置云凭据和属性。
* 配置和管理服务器上的卷。
* 配置卷组。
* 备份和还原数据。
* 监视性能。
* 查看系统设置并确定可能的问题。

你可以使用 StorSimple 管理器服务执行需要系统停机（例如，更新的初始设置和安装）之外的所有管理任务。

有关详细信息，请转到[使用 StorSimple Manager 服务管理 StorSimple 设备](storsimple-manager-service-administration.md)。

## <a name="windows-powershell-for-storsimple"></a>Windows PowerShell for StorSimple
Windows PowerShell for StorSimple 提供一个命令行界面，你可以使用该界面创建和管理 Microsoft Azure StorSimple 服务并设置和监视 StorSimple 设备。 它是基于 Windows PowerShell 的命令行界面，其中包括用于管理 StorSimple 设备的专用 cmdlet。 使用 Windows PowerShell for StorSimple 的功能可以：

* 注册设备。
* 配置设备上的网络接口。
* 安装某些类型的更新。
* 通过访问支持会话，对设备进行故障排除。
* 更改设备状态。

可以从串行控制台（位于与设备直接相连的主机计算机上）访问 Windows PowerShell for StorSimple，也可以通过使用 Windows PowerShell 远程处理来远程访问它。 请注意，某些 Windows PowerShell for StorSimple 任务（如初始设备注册）只能在串行控制台上完成。 

有关详细信息，请转到[使用 Windows PowerShell for StorSimple 管理设备](storsimple-windows-powershell-administration.md)。

## <a name="azure-powershell-storsimple-cmdlets"></a>Azure PowerShell StorSimple cmdlet
Azure PowerShell StorSimple cmdlet 是 Windows PowerShell cmdlet 的一个集合，使你能够自动从命令行执行服务级别和迁移任务。 有关适用于 StorSimple 的 Azure Powershell cmdlet 的详细信息，请转到 [cmdlet 参考](https://docs.microsoft.com/powershell/servicemanagement/azure.storsimple/v3.1.0/azure.storsimple)。

## <a name="storsimple-snapshot-manager"></a>StorSimple 快照管理器
StorSimple 快照管理器是 Microsoft 管理控制台 (MMC) 管理单元，可用于创建本地数据和云数据的一致时间点备份副本。 该管理单元在基于 Windows Server 的主机上运行。 可以使用 StorSimple 快照管理器执行以下操作：

* 配置、备份和删除卷。
* 配置卷组以确保备份的数据是应用程序一致的。
* 管理备份策略，以便按预先确定的计划备份数据并将其存储在指定位置（本地或云中）。
* 还原卷和单个文件。

将备份捕获为快照，快照仅记录自上一快照创建以来的更改，而且需要的存储空间远远少于完全备份。 你可以根据需要创建备份计划或进行即时备份。 此外，还可以使用 StorSimple 快照管理器建立控制将保存多个少快照的保留策略。 如果你以后需要从备份还原数据，StorSimple 快照管理器会让你从本地或云快照目录中进行选择。 

如果发生灾难或因其他原因需要还原数据，StorSimple 快照管理器可在需要时以增量方式还原数据。 数据还原不要求你在还原文件、更换设备或将操作移动到另一站点时关闭整个系统。

有关详细信息，请转到[什么是 StorSimple Snapshot Manager？](storsimple-what-is-snapshot-manager.md)

## <a name="storsimple-adapter-for-sharepoint"></a>StorSimple Adapter for SharePoint
Microsoft Azure StorSimple 包括 StorSimple Adapter for SharePoint，后者是一个可选组件，用于将 StorSimple 存储和数据保护功能透明扩展到 SharePoint 服务器场。 该适配器与远程 Blob 存储 (RBS) 提供程序和 SQL Server RBS 功能兼容，允许你将 BLOB 转移到 Microsoft Azure StorSimple 系统所支持的服务器。 然后，Microsoft Azure StorSimple 会根据使用情况将 BLOB 数据存储在本地或云中。

StorSimple Adapter for SharePoint 在 SharePoint 管理中心门户内进行管理。 因此，SharePoint 管理保持集中方式，而且所有存储看起来都位于 SharePoint 场中。

有关详细信息，请转到 [StorSimple Adapter for SharePoint](storsimple-adapter-for-sharepoint.md)。 

## <a name="storage-management-technologies"></a>存储管理技术
除了专用 StorSimple 设备、虚拟设备和其他组件外，Microsoft Azure StorSimple 还使用以下软件技术提供快速数据访问和减少存储消耗：

* [自动存储分层](#automatic-storage-tiering) 
* [精简预配](#thin-provisioning) 
* [删除重复和压缩](#deduplication-and-compression) 

### <a name="automatic-storage-tiering"></a>自动存储分层
Microsoft Azure StorSimple 根据当前使用情况、年龄以及与其他数据的关系自动将数据排列在各逻辑层中。 使用最频繁的数据存储在本地，不太频繁使用的数据和非活动数据则自动迁移到云中。 下图说明了这种存储方法。

![StorSimple 存储层](./media/storsimple-overview/hcs-data-services-storsimple-components-tiers.png)

为了能够快速访问，StorSimple 将非常频繁使用的数据（热数据）存储在 StorSimple 设备的各个 SSD 上。 它将偶尔使用的数据（暖数据）存储在设备的各个 HDD 上，或者数据中心的各个服务器上。 它将非活动数据、备份数据或者为存档或合规性目的而保留的数据移到云中。 

> [!NOTE]
> 在 Update 2 或更高版本中，可将某卷指定为本地固定卷，在这种情况下，数据将保留在本地设备上且不会分层到云中。 
> 
> 

StorSimple 会随着使用模式的变化调整和重新排列数据及存储分配。 例如，随着时间的推移，一些信息可能不太频繁使用。 随着使用频率的逐渐减少，它会从 SSD 迁移到 HDD，然后再迁移到云。 如果该数据再次变得频繁使用，它将迁回到存储设备。

存储分层过程如以下所示：

1. 系统管理员设置 Microsoft Azure 云存储帐户。
2. 管理员使用串行控制台和 StorSimple Manager 服务（在 Azure 经典门户中运行）来配置设备和文件服务器，从而创建卷和数据保护策略。 本地计算机（如文件服务器）使用 Internet 小型计算机系统接口 (iSCSI) 访问 StorSimple 设备。
3. 最初，StorSimple 将数据存储在设备的快速 SSD 层上。
4. 随着 SSD 层接近容量，StorSimple 对最旧的数据块进行删除重复和压缩操作，并将它们移动到 HDD 层。
5. 随着 HDD 层接近容量，StorSimple 将最旧的数据块进行加密，并将其通过 HTTPS 安全地发送到 Microsoft Azure 存储帐户。
6. Microsoft Azure 在其数据中心和远程数据中心中创建多个数据副本，确保灾难发生时可以恢复数据。 
7. 文件服务器请求存储在云中的数据时，StorSimple 将其无缝地返回，并将副本存储在 StorSimple 设备的 SSD 层。

### <a name="thin-provisioning"></a>精简设置
精简设置是一项虚拟化技术，可使可用存储看起来超过物理资源。 StorSimple 使用精简预配分配恰好满足当前需求的足够空间，而不是提前保留充足的存储。 云存储的弹性性质促进了这一方法的实施，因为 StorSimple 能够增加或减少云存储以满足不断变化的需求。 

> [!NOTE]
> 未对本地固定卷进行精简设置。 创建卷时，将分配到仅本地卷的存储器作为一个整体进行设置。
> 
> 

### <a name="deduplication-and-compression"></a>删除重复和压缩
Microsoft Azure StorSimple 使用删除重复和数据压缩功能进一步减少存储需求。

删除重复通过消除存储数据集中的冗余来减少总体存储的数据量。 当信息更改时，StorSimple 忽略未更改的数据并且仅捕获更改。 此外，StorSimple 还标识并删除不必要的信息，从而减少存储的数据量。 

> [!NOTE]
> 未对本地固定卷上的数据进行删除重复或压缩操作。 但对本地固定卷的备份进行了删除重复或压缩操作。
> 
> 

## <a name="storsimple-workload-summary"></a>StorSimple 工作负荷摘要
下表列出了受支持的 StorSimple 工作负荷摘要。

| 方案 | 工作负载 | 支持 | 限制 | 版本 |
| --- | --- | --- | --- | --- |
| 协作 |文件共享 |是 | |所有版本 |
| 协作 |分布式文件共享 |是 | |所有版本 |
| 协作 |SharePoint |是* |仅本地固定卷支持 |Update 2 及更高版本 |
| 存档 |简单文件存档 |是 | |所有版本 |
| 虚拟化 |虚拟机 |是* |仅本地固定卷支持 |Update 2 及更高版本 |
| 数据库 |SQL |是* |仅本地固定卷支持 |Update 2 及更高版本 |
| 视频监视 |视频监视 |是* |StorSimple 设备专用于此工作负荷时，支持 |Update 2 及更高版本 |
| 备份 |主目标备份 |是* |StorSimple 设备专用于此工作负荷时，支持 |Update 3 及更高版本 |
| 备份 |辅助目标备份 |是* |StorSimple 设备专用于此工作负荷时，支持 |Update 3 及更高版本 |

是&#42; - 应该应用解决方案的指导原则和限制。

StorSimple 8000 系列设备不支持以下工作负荷。 如果部署在 StorSimple，这些工作负荷将产生不支持的配置。

* 医学成像
* Exchange
* VDI
* Oracle
* SAP
* 大数据
* 内容分发
* 从 SCSI 启动

下面是 StorSimple 支持的基础结构组件列表。 

| 方案 | 工作负载 | 支持 | 限制 | 版本 |
| --- | --- | --- | --- | --- |
| 常规 |Express Route |是 | |所有版本 |
| 常规 |DataCore FC |是* |受 DataCore SANsymphony 支持 |所有版本 |
| 常规 |DFSR |是* |仅本地固定卷支持 |所有版本 |
| 常规 |索引 |是* |对于分层卷，仅支持元数据索引（非数据）。<br>对于本地固定卷，支持完整索引。 |所有版本 |
| 常规 |防病毒 |是* |对于分层卷，仅支持扫描打开和关闭。<br> 对于本地固定卷，支持完全扫描。 |所有版本 |

是&#42; - 应该应用解决方案的指导原则和限制。

## <a name="storsimple-terminology"></a>StorSimple 术语
部署 Microsoft Azure StorSimple 解决方案之前，我们建议查看以下术语和定义。

### <a name="key-terms-and-definitions"></a>关键术语和定义
| 术语（首字母缩写词或缩写） | 说明 |
| --- | --- |
| 访问控制记录 (ACR) |与 Microsoft Azure StorSimple 设备上的卷相关联的记录，可决定哪些主机可以连接到它。 此决定基于连接到 StorSimple 设备的主机（包含在 ACR 中）上 iSCSI 限定的名称 (IQN)。 |
| AES-256 |当数据在云中出入时，用于加密该数据的 256 位高级加密标准 (AES) 算法。 |
| 分配单元大小 (AUS) |可分配用于保留 Windows 文件系统中的文件的最小磁盘空间。 如果文件大小不是群集大小的偶数倍，则必须使用额外的空间来保留文件（群集大小的下一个倍数），这将导致空间丢失和硬盘产生碎片。 <br>对于 Azure StorSimple 卷，建议的 AUS 为 64 KB，因为它非常适合删除重复算法。 |
| 自动存储分层 |自动将不太活跃的数据从 SSD 移动到 HDD，再移动到云中的某一层，然后从中心用户界面启用所有存储的管理。 |
| 备份目录 |备份的集合，通常通过所使用的应用程序类型相关联。 此集合显示在 StorSimple Manager 服务 UI 的“备份目录”页。 |
| 备份目录文件 |一个包含可用快照列表的文件，此快照当前存储在 StorSimple Snapshot Manager 的备份数据库中。 |
| 备份策略 |卷、备份类型和时间表的选择方案，通过此选择，可以按预定义计划创建备份。 |
| 二进制大型对象 (BLOB) |二进制数据的集合，这些数据作为单个实体存储在数据库管理系统中。 BLOB 通常是图像、音频或其他多媒体对象，但有时将二进制可执行代码存储为 BLOB。 |
| 质询握手身份验证协议 (CHAP) |一种协议，用于根据共享密码或密钥的对等，对连接的对等进行身份验证。 CHAP 可为单向或相互。 在单向 CHAP 中，目标对发起程序进行身份验证。 相互 CHAP 则要求目标对发起程序进行身份验证，发起程序也对目标进行身份验证。 |
| 克隆 |卷的重复副本。 |
| 云即层 (CaaT) |作为存储体系结构中的一个层集成的云存储，以便所有存储看起来都是一个企业存储网络的一部分。 |
| 云服务提供商 (CSP) |云计算服务的提供商。 |
| 云快照 |存储在云中的卷数据的时间点副本。 云快照等效于在不同的异地存储系统上复制的快照。 在灾难恢复方案中，云快照特别有用。 |
| 云存储加密密钥 |StorSimple 设备访问从你的设备发送到云中的加密数据所用的密码或密钥。 |
| 群集感知更新 |管理故障转移群集中各服务器的软件更新，以便更新对服务可用性影响最小或没有影响。 |
| 数据路径 |功能单元的集合，执行互连的数据处理操作。 |
| 停用 |一种永久性操作，可断开 StorSimple 设备和关联的云服务之间的连接。 此过程完成后，仍保留设备的云快照，这些快照可被克隆或用于灾难恢复。 |
| 磁盘镜像 |对独立硬盘上逻辑磁盘卷的实时复制，以确保持续可用性。 |
| 动态磁盘镜像 |对动态磁盘上逻辑磁盘卷的复制。 |
| 动态磁盘 |一种磁盘卷格式，可使用逻辑磁盘管理器 (LDM) 在多个物理磁盘中存储和管理数据。 可扩大动态磁盘，以提供更多可用空间。 |
| 扩展磁盘组 (EBOD) 机箱 |Microsoft Azure StorSimple 设备的辅助机箱，其中包含用于其他存储的额外硬盘驱动器。 |
| FAT 预配 |常规的存储预配，其中，存储空间根据预期需求分配（且通常超出当前需求）。 另请参阅“精简预配”。 |
| 硬盘驱动器 (HDD) |使用旋转盘片来存储数据的驱动器。 |
| 混合云存储 |使用本地资源和异地资源的存储体系结构，包括云存储。 |
| Internet 小型计算机系统接口 (iSCSI) |一种基于存储网络标准的 Internet 协议 (IP)，用于链接数据存储设备或设施。 |
| iSCSI 发起程序 |允许主机计算机运行 Windows，以连接到基于 iSCSI 的外部存储网络的一个软件组件。 |
| iSCSI 限定的名称 (IQN) |标识 iSCSI 目标或发起程序的一个唯一名称。 |
| iSCSI 目标 |在存储区域网络中提供集中式 iSCSI 磁盘子系统的一个软件组件。 |
| 实时存档 |可在任何时间访问存档数据一种存储方法（例如，存档数据并非场外存储在磁带）。 Microsoft Azure StorSimple 使用实时存档。 |
| 本地固定卷 |驻留在设备上且永远不会分层到云中的卷。 |
| 本地快照 |一个卷数据的时间点副本，存储在 Microsoft Azure StorSimple 设备。 |
| Microsoft Azure StorSimple |一个功能强大的解决方案，由数据中心存储设备和软件组成，使 IT 组织能够像数据中心存储一样利用云存储。 在降低成本的同时，StorSimple 可简化数据保护和数据管理。 通过与云无缝集成，该解决方案合并了主存储、存档、备份和灾难恢复 (DR)。 通过在企业级平台上组合 SAN 存储和云数据管理，StorSimple 设备为所有与存储相关的需求带来了高效率、简易性和可靠性。 |
| 电源和散热模块 (PCM) |StorSimple 设备的硬件组件包含电源和散热风扇，因此将其命名为电源和散热模块。 设备的主机箱有两个 764W PCM，而 EBOD 机箱有两个 580W PCM。 |
| 主机箱 |StorSimple 设备的主机箱，其中包含应用程序平台控制器。 |
| 恢复时间目标 (RTO) |在灾难发生后，完全还原业务流程或系统之前，应使用的最大时间量。 |
| 串行附加 SCSI (SAS) |硬盘驱动器 (HDD) 类型。 |
| 服务数据加密密钥 |注册 StorSimple Manager 服务的任何新 StorSimple 设备可用的密钥。 使用公钥对在 StorSimple Manager 服务和设备之间传输的配置数据进行加密，只能通过在该设备上使用私钥进行解密。 服务数据加密密钥允许服务获取该私钥进行解密。 |
| 服务注册密钥 |一个密钥，可帮助注册 StorSimple Manager 服务的 StorSimple 设备，使该设备出现在 Azure 经典门户中从而进行进一步管理操作。 |
| 小型计算机系统接口 (SCSI) |用于以物理方式连接计算机并在它们之间传递数据的一个标准集。 |
| 固态硬盘 (SSD) |不包含任何移动部件的磁盘；例如，U 盘。 |
| 存储帐户 |一组访问凭据，该凭据链接到给定云服务提供商的存储帐户。 |
| StorSimple Adapter for SharePoint |Microsoft Azure StorSimple 组件，用于将 StorSimple 存储和数据保护透明地扩展到 SharePoint 服务器场。 |
| StorSimple 管理器服务 |Azure 经典门户的扩展，使你能管理 Azure StorSimple 本地设备和虚拟设备。 |
| StorSimple 快照管理器 |Microsoft 管理控制台 (MMC) 管理单元，用于管理 Microsoft Azure StorSimple 中的备份和还原操作。 |
| 执行备份 |一种允许用户对卷执行交互备份的功能。 它是另一种手动备份卷的方法，而不是通过已定义的策略自动备份。 |
| 精简预配 |一种优化存储系统使用可用存储空间效率的方法。 在精简预配中，根据每个用户在任何给定时间所需的最小空间，为多个用户分配存储。 另请参阅 FAT 预配。 |
| 分层 |在逻辑分组中，根据当前使用情况、期限以及与其他数据的关系对数据进行排列。 StorSimple 自动排列层中的数据。 |
| 卷 |逻辑存储区，以驱动器的形式表示。 StorSimple 卷对应于主机装载​​的卷，包括通过使用 iSCSI 和 StorSimple 设备发现的卷。 |
| 卷容器 |卷和应用到卷的设置的一个分组。 StorSimple 设备中的所有卷都分组到卷容器。 卷容器设置包括存储帐户、使用关联加密密钥将数据发送到云所采用的加密设置，以及涉及云的操作所消耗的带宽。 |
| 卷组 |在 StorSimple Snapshot Manager 中，卷组是为方便备份处理配置的卷的集合。 |
| 卷影复制服务 (VSS) |一种 Windows Server 操作系统服务，通过与识别 VSS 的应用程序通信来协调增量快照的创建，进而促进应用程序一致性。 VSS 确保拍摄快照时应用程序暂时处于非活动状态。 |
| Windows PowerShell for StorSimple |基于 Windows PowerShell 的命令行接口，用于操作和管理 StorSimple 设备。 此界面在保持 Windows PowerShell 的某些基本功能的同时，还提供附加的专用 cmdlet，它们都是专门针对管理 StorSimple 设备而设计的。 |

## <a name="next-steps"></a>后续步骤
了解 [StorSimple 安全性](storsimple-security.md)。




<!--HONumber=Jan17_HO5-->


