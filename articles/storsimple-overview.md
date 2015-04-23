<properties 
   pageTitle="什么是 StorSimple？" 
   description="介绍 StorSimple 功能、体系结构和组件。" 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="AdinaH" 
   editor=""/>
   
<tags ms.service="storsimple" ms.date="02/17/2015" wacn.date="04/11/2015"/>

# 什么是 StorSimple？ 

Microsoft Azure StorSimple 是一种经济高效的可管理解决方案，可以消除与企业存储和数据保护相关的很多问题和开支。它使用专有设备（Microsoft Azure StorSimple 设备）和集成管理工具，提供包括云存储在内的所有企业存储的无缝视图。

## 为什么使用 StorSimple？

Microsoft Azure StorSimple 提供以下好处：

- **透明集成** - Microsoft Azure StorSimple 使用 Internet 小型计算机系统接口 (iSCSI) 协议，在用户毫无察觉的情况下连接数据存储设施。这样可以确保存储在云中、数据中心或远程服务器上的数据看起来好像存储在单个位置。
- **降低存储成本** - Microsoft Azure StorSimple 分配足够的本地存储或云存储以满足当前需求，仅在必要时才扩展云存储。它可以通过消除相同数据的冗余版本（删除重复）和使用压缩，进一步降低存储需求和开支。
- **简化存储管理** - Microsoft Azure StorSimple 提供了系统管理工具，可用于配置和管理存储在本地、远程服务器或云中的数据。此外，你还可以从 Microsoft 管理控制台 (MMC) 管理单元管理备份和还原功能。StorSimple 提供了独立的可选接口，可用于将 StorSimple 管理和数据保护服务扩展到存储在 SharePoint 服务器上的内容。 
- **改进灾难恢复和合规性** - Microsoft Azure StorSimple 不需要过长的恢复时间。相反，它仅在需要时才还原数据。这意味着正常运行能够得以继续，而很少发生中断。此外，你可以配置策略以指定备份计划和数据保留期。
- **数据移动性** - 上载到 Microsoft Azure 云服务的数据可从其他站点访问，以便进行恢复和迁移。此外，你可以使用 StorSimple 来配置在 Microsoft Azure 中运行的虚拟机 (VM) 上的 StorSimple 虚拟设备。然后，VM 可使用虚拟设备来访问存储的数据，以便进行测试或恢复。 

下图提供了 Microsoft Azure StorSimple 解决方案的高级视图。

![StorSimple architecture](./media/storsimple-overview/hcs-data-services-storsimple-system-architecture.png)

**Microsoft Azure StorSimple 体系结构**

## StorSimple 组件

Microsoft Azure StorSimple 解决方案包括以下组件：

- **Microsoft Azure StorSimple 设备** - 是一个本地混合存储阵列，包含固态硬盘 (SSD) 和硬盘驱动器 (HDD)，还具有冗余控制器和自动故障转移功能。控制器管理存储分层，将当前使用的数据（也就是热数据）放置在本地存储中（在设备或本地服务器上），同时将使用频率较低的数据移动到云。
- **StorSimple 虚拟设备** - 这是 StorSimple 设备的软件版本，复制了物理混合存储设备的体系结构和功能。StorSimple 虚拟设备在 Azure 虚拟机中的单个节点上运行。虚拟设备适合在测试和小型试运行方案中使用。不能在 StorSimple 设备或在本地服务器上创建 StorSimple 虚拟设备。
- **Windows PowerShell for StorSimple** - 一个命令行界面，可用于管理 StorSimple 设备。Windows PowerShell for StorSimple 具有多种功能，让你能够注册 StorSimple 设备、配置设备上的网络接口、安装特定类型的更新、通过访问支持会话为设备排除故障，以及更改设备状态。可以通过连接到串行控制台或使用 Windows PowerShell 远程处理来访问 Windows PowerShell for StorSimple。
- **StorSimple 管理器服务** - Azure 管理门户的扩展，让你能够从单个 Web 界面管理 StorSimple 设备或 StorSimple 虚拟设备。你可以使用 StorSimple 管理器服务来创建和管理服务、查看和管理设备、查看警报、管理卷以及查看和管理备份策略和备份目录。
- **StorSimple 快照管理器** - 一个 MMC 管理单元，它使用卷组和 Windows 卷影复制服务来生成应用程序一致性备份。此外，你还可以使用 StorSimple 快照管理器来创建备份计划，以及克隆或还原卷。 
- **StorSimple Adapter for SharePoint** - 一种工具，可将 Microsoft Azure StorSimple 存储和数据保护透明地扩展到 SharePoint 服务器场，同时让用户能够从 SharePoint 管理门户查看和管理 StorSimple 存储。

## 后续步骤

阅读有关 [StorSimple 组件](https://technet.microsoft.com/zh-CN/library/cc754482.aspx)的信息并查看 [StorSimple 发行说明](https://msdn.microsoft.com/zh-CN/library/azure/dn772367.aspx)





<!--HONumber=51-->