<properties 
   pageTitle="什么是 StorSimple 组件？" 
   description="介绍 StorSimple 设备、服务和管理技术。" 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="AdinaH" 
   editor=""/>
   
<tags ms.service="storsimple" ms.date="02/17/2015" wacn.date="04/11/2015"/>

# 什么是 StorSimple 组件？ 

欢迎使用 Microsoft Azure StorSimple，它是集成的存储解决方案，用于管理本地设备与 Microsoft Azure 云存储之间的存储任务。StorSimple 旨在降低存储成本、简化存储管理、提高灾难恢复能力和效率，并提供数据移动性。

以下各节介绍 Microsoft Azure StorSimple 组件，并解释该解决方案如何排列数据、分配存储并促进存储管理和数据保护。 

> [AZURE.NOTE] Microsoft Azure 网站上发布的 StorSimple 部署信息仅适用于 StorSimple 8000 系列设备。有关 7000 系列设备的信息，请转到：[StorSimple 帮助](http://onlinehelp.storsimple.com/)。

## StorSimple 设备

Microsoft Azure StorSimple 设备是提供主存储和其他位置所存储数据的 iSCSI 访问权限的本地混合存储阵列。它管理与云存储的通信，并有助于确保 Microsoft Azure StorSimple 解决方案中存储的所有数据的安全性和机密性。

StorSimple 设备包括固态硬盘 (SSD) 和硬盘驱动器 (HDD)，并且支持群集和自动故障转移。它包含一个共享处理器、一个共享存储和两个镜像控制器。每个控制器都提供以下内容：

- 与主机计算机的连接
- 最多六个用于连接到局域网 (LAN) 的网络端口
- 硬件监视
- 非易失性随机存取内存 (NVRAM)，即使断电也可保留信息
- 群集感知更新，能够管理故障转移群集中各服务器的软件更新，以便更新对服务可用性影响最小或没有影响
- 群集服务，与后端群集的运行方式类似，可提供高可用性并将 HDD 或 SSD 出现故障或脱机时可能产生的负面影响降至最低

在任一时间点，只有一个控制器处于活动状态。如果活动控制器出现故障，第二个控制器将自动变为活动状态。 

有关详细信息，请参阅 [StorSimple 设备](https://msdn.microsoft.com/zh-CN/library/azure/dn772363.aspx)。

## StorSimple 虚拟设备

可以使用 StorSimple 创建一个虚拟设备，以复制实际混合存储设备的体系结构和功能。 

StorSimple 虚拟设备在 Azure 虚拟机中的单个节点上运行。（只能在 Azure 虚拟机上创建虚拟设备。不能在 StorSimple 设备或本地服务器上创建虚拟设备。）StorSimple 虚拟设备不同于物理 StorSimple 设备，如下所示： 

- 虚拟设备只有一个接口，而物理设备有六个网络接口。 
- 虚拟设备在设备配置期间注册，而不是作为单独的任务。
- 无法从虚拟设备重新生成服务数据加密密钥。在密钥滚动更新期间，你将在物理设备上重新生成密钥，然后使用新密钥更新虚拟设备。
- 如果你需要将更新应用于虚拟设备，则会出现短暂的停机。物理 StorSimple 设备则不会出现此情况。

对于物理设备不可用的灾难恢复情形（如测试和小型试运行部署），建议使用 StorSimple 虚拟设备。

有关详细信息，请参阅 [StorSimple 虚拟设备](https://msdn.microsoft.com/zh-CN/library/azure/dn772390.aspx)。


## 存储管理技术
 
除了专用 StorSimple 设备和虚拟设备外，Microsoft Azure StorSimple 还使用以下软件技术提供快速数据访问和减少存储消耗：

- 自动存储分层 
- 精简设置 
- 删除重复和压缩 

### 自动存储分层

Microsoft Azure StorSimple 根据当前使用情况、年龄以及与其他数据的关系自动将数据排列在各逻辑层中。使用最频繁的数据存储在本地，不太频繁使用的数据和非活动数据则自动迁移到云中。下图说明了这种存储方法。
 
![StorSimple storage tiers](./media/storsimple-components/hcs-data-services-storsimple-components-tiers.png)

**StorSimple 存储层**

为了能够快速访问，StorSimple 将非常频繁使用的数据（热数据）存储在 StorSimple 设备的各个 SSD 上。它将偶尔使用的数据（暖数据）存储在设备的各个 HDD 上，或者数据中心的各个服务器上。它将非活动数据、备份数据或者为存档或合规性目的而保留的数据移到云中。 

StorSimple 会随着使用模式的变化调整和重新排列数据及存储分配。例如，随着时间的推移，一些信息可能不太频繁使用。随着使用频率的逐渐减少，它会从 SSD 迁移到 HDD，然后再迁移到云。如果该数据再次变得频繁使用，它将迁回到存储设备。

### 精简设置

精简设置是一项虚拟化技术，可使可用存储看起来超过物理资源。StorSimple 使用精简设置分配恰好满足当前需求的足够空间，而不是提前保留充足的存储。云存储的弹性性质促进了这一方法的实施，因为 StorSimple 能够增加或减少云存储以满足不断变化的需求。 

### 删除重复和压缩

Microsoft Azure StorSimple 使用删除重复和数据压缩功能进一步减少存储需求。

删除重复通过消除存储数据集中的冗余来减少总体存储的数据量。当信息更改时，StorSimple 忽略未更改的数据并且仅捕获更改。此外，StorSimple 还标识并删除不必要的信息，从而减少存储的数据量。 

## Windows PowerShell for StorSimple

Windows PowerShell for StorSimple 提供一个命令行界面，你可以使用该界面创建和管理 Microsoft Azure StorSimple 服务并设置和监视 StorSimple 设备。它是基于 Windows PowerShell 的命令行界面，其中包括用于管理 StorSimple 设备的专用 cmdlet。使用 Windows PowerShell for StorSimple 的功能可以：

- 注册设备。
- 配置设备上的网络接口。
- 安装某些类型的更新。
- 通过访问支持会话，对设备进行故障排除。
- 更改设备状态。

可以从串行控制台（位于与设备直接相连的主机计算机上）访问 Windows PowerShell for StorSimple，也可以通过使用 Windows PowerShell 远程处理来远程访问它。请注意，某些 Windows PowerShell for StorSimple 任务（如初始设备注册）只能在串行控制台上完成。 

有关详细信息，请参阅 [Windows PowerShell for StorSimple](https://msdn.microsoft.com/zh-CN/library/azure/dn772425.aspx)。

## StorSimple 管理器服务

Microsoft Azure StorSimple 提供一个基于 Web 的用户界面（StorSimple 管理器服务），可用于集中管理数据中心和云存储。可以使用 StorSimple 管理器服务执行以下任务：

- 配置 StorSimple 设备的系统设置。
- 配置和管理 StorSimple 设备的安全设置。
- 配置云凭据和属性。
- 配置和管理服务器上的卷。
- 配置卷组。
- 备份和还原数据。
- 监视性能。
- 查看系统设置并确定可能的问题。

你可以使用 StorSimple 管理器服务执行需要系统停机（例如，更新的初始设置和安装）之外的所有管理任务。

有关详细信息，请参阅 [StorSimple 管理器服务](https://msdn.microsoft.com/zh-CN/library/azure/dn772396.aspx)。

## StorSimple 快照管理器

StorSimple 快照管理器是 Microsoft 管理控制台 (MMC) 管理单元，可用于创建本地数据和云数据的一致时间点备份副本。该管理单元在基于 Windows Server 的主机上运行。可以使用 StorSimple 快照管理器执行以下操作：

- 配置、备份和删除卷。
- 配置卷组以确保备份的数据是应用程序一致的。
- 管理备份策略，以便按预先确定的计划备份数据并将其存储在指定位置（本地或云中）。
- 还原卷和单个文件。

将备份捕获为快照，快照仅记录自上一快照创建以来的更改，而且需要的存储空间远远少于完全备份。你可以根据需要创建备份计划或进行即时备份。此外，还可以使用 StorSimple 快照管理器建立控制将保存多个少快照的保留策略。如果你以后需要从备份还原数据，StorSimple 快照管理器会让你从本地或云快照目录中进行选择。 

如果发生灾难或因其他原因需要还原数据，StorSimple 快照管理器可在需要时以增量方式还原数据。数据还原不要求你在还原文件、更换设备或将操作移动到另一站点时关闭整个系统。

有关详细信息，请参阅 [StorSimple 快照管理器](https://msdn.microsoft.com/zh-CN/library/azure/dn772365.aspx)。

## StorSimple Adapter for SharePoint

Microsoft Azure StorSimple 包括 StorSimple Adapter for SharePoint，后者是一个可选组件，用于将 StorSimple 存储和数据保护功能透明扩展到 SharePoint 服务器场。该适配器与远程 Blob 存储 (RBS) 提供程序和 SQL Server RBS 功能兼容，允许你将 BLOB 转移到 Microsoft Azure StorSimple 系统所支持的服务器。然后，Microsoft Azure StorSimple 会根据使用情况将 BLOB 数据存储在本地或云中。

StorSimple Adapter for SharePoint 在 SharePoint 管理中心门户内进行管理。因此，SharePoint 管理保持集中方式，而且所有存储看起来都位于 SharePoint 场中。

有关详细信息，请参阅 [StorSimple Adapter for SharePoint](https://msdn.microsoft.com/zh-CN/library/azure/dn757737.aspx)。 


## 后续步骤

查看 [StorSimple 发行说明](https://msdn.microsoft.com/zh-CN/library/azure/dn772367.aspx)






<!--HONumber=51-->