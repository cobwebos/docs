---
title: "什么是 StorSimple Snapshot Manager？ | Microsoft 文档"
description: "介绍 StorSimple Snapshot Manager 及其体系结构和功能。"
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: 
ms.assetid: 6094c31e-e2d9-4592-8a15-76bdcf60a754
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: v-sharos
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 43eb76f2d51c586f3cd44d4d34b307257a655182
ms.openlocfilehash: 38c197c7bc57110b29b1d8cb789d5b7310823da2
ms.contentlocale: zh-cn
ms.lasthandoff: 02/28/2017

---
# <a name="an-introduction-to-storsimple-snapshot-manager"></a>StorSimple Snapshot Manager 简介

## <a name="overview"></a>概述
StorSimple Snapshot Manager 是一种 Microsoft 管理控制台 (MMC) 管理单元，可以简化 Microsoft Azure StorSimple 环境中的保护和备份管理。 借助 StorSimple Snapshot Manager，可以将数据中心和云中的 Microsoft Azure StorSimple 数据作为单一集成的存储解决方案进行管理，这样可以简化备份流程并降低成本。

本概述介绍 StorSimple Snapshot Manager 及其功能，并阐述它在 Microsoft Azure StorSimple 中的作用。 

有关整个 Microsoft Azure StorSimple 系统的概述，包括 StorSimple 设备、StorSimple Manager 服务、StorSimple Snapshot Manager 和 StorSimple Adapter for SharePoint，请参阅 [StorSimple 8000 系列：混合云存储解决方案](storsimple-overview.md)。 

> [!NOTE]
> * 不能使用 StorSimple Snapshot Manager 管理 Microsoft Azure StorSimple 虚拟数组（也称为 StorSimple 本地虚拟设备）。
> * 如果你使用的是旧版 StorSimple Snapshot Manager，则需要更新（**安装新版本前，无需卸载旧版本**）。 最新版本的 StorSimple Snapshot Manager 向后兼容，并可与所有已发布的 Microsoft Azure StorSimple 版本一起使用。 如果你使用的是旧版 StorSimple Snapshot Manager，则需要更新（安装新版本前，无需卸载旧版本）。
> 
> 

## <a name="storsimple-snapshot-manager-purpose-and-architecture"></a>StorSimple Snapshot Manager 的用途和体系结构
StorSimple Snapshot Manager 提供了一个中心管理控制台，可用于创建本地数据和云数据的一致时间点备份副本。 例如，可以将控制台用于：

* 配置、备份和删除卷。
* 配置卷组以确保备份的数据是应用程序一致的。
* 管理备份策略，以便在预先确定的计划上备份数据。
* 创建本地和云快照，它们可以存储在云中并用于灾难恢复。

StorSimple Snapshot Manager 提取使用主机上的 VSS 提供程序注册的应用程序的列表。 然后，若要创建应用程序一致性备份，它会检查应用程序使用的卷并建议要配置的卷组。 StorSimple Snapshot Manager 使用这些卷组来生成应用程序一致的备份副本。 （当所有的相关文件和数据库已同步并表示应用程序在特定时间点的真实状态时，即存在应用程序一致性。） 

StorSimple Snapshot Manager 备份采用增量快照的形式，仅捕获自上次备份以来的更改。 因此，备份需要较少的存储空间，可以快速创建和恢复。 StorSimple Snapshot Manager 使用 Windows 卷影复制服务 (VSS) 以确保快照捕获应用程序一致性数据。 （有关详细信息，请转到“与 Windows 卷影复制服务集成”部分。）使用 StorSimple Snapshot Manager，你可以根据需要创建备份计划或进行即时备份。 如果你需要从备份还原数据，StorSimple Snapshot Manager 会让你从本地或云快照目录中进行选择。 Azure StorSimple 仅在需要时还原所需的数据，这样可以防止在还原操作期间数据可用性发生延迟。）

![StorSimple Snapshot Manager 体系结构](./media/storsimple-what-is-snapshot-manager/HCS_SSM_Overview.png)

**StorSimple Snapshot Manager 体系结构** 

## <a name="support-for-multiple-volume-types"></a>支持多个卷类型
可以使用 StorSimple Snapshot Manager 配置和备份以下类型的卷： 

* **基本卷** － 基本卷是基本磁盘上的单个分区。 
* **简单卷** － 简单卷是一个动态卷，包含单个动态磁盘中的磁盘空间。 简单卷包含磁盘上的单个区域或同一磁盘上链接在一起的多个区域。 （只能在动态磁盘上创建简单卷。）简单卷不具备容错能力。
* **动态卷** － 动态卷是在动态磁盘上创建的卷。 动态磁盘使用数据库来跟踪有关卷的信息，这些卷包含在计算机的动态磁盘中。 
* **具有镜像的动态卷** － 具有镜像的动态卷基于 RAID 1 体系结构。 通过 RAID 1 体系结构，将相同的数据写入两个或更多磁盘中，从而生成镜像集。 然后可以由任意一个包含所请求的数据的磁盘处理读取请求。
* **群集共享卷** － 通过群集共享卷 (CSV)，故障转移群集中的多个节点可以同时读取或写入到同一磁盘中。 可以快速从一个节点故障转移到另一个节点，无需更改驱动器所有权或安装、卸载和移除卷。 

> [!IMPORTANT]
> 不要在同一快照中混用 CSV 和非 CSV。 不支持在一个快照中混用 CSV 和非 CSV。 
> 
> 

可以使用 StorSimple Snapshot Manager 还原整个卷组或克隆单个卷并恢复单个文件。

* [卷和卷组](#volumes-and-volume-groups) 
* [备份类型和备份策略](#backup-types-and-backup-policies) 

有关 StorSimple Snapshot Manager 的功能以及如何使用它们的详细信息，请参阅 [StorSimple Snapshot Manager 用户界面](storsimple-use-snapshot-manager.md)。

## <a name="volumes-and-volume-groups"></a>卷和卷组
通过 StorSimple Snapshot Manager，可以创建卷，然后将其配置到卷组。 

StorSimple Snapshot Manager 使用卷组创建应用程序一致性备份副本。 当所有的相关文件和数据库已同步并表示应用程序在特定时间点的真实状态时，即存在应用程序一致性。 卷组（也称为*一致性组*）构成了备份或还原作业的基础。

卷组与卷容器不同。 卷容器包含一个或多个卷，这些卷共享云存储帐户和其他属性，例如加密和带宽消耗。 单个卷容器最多可以包含 256 个精简预配的 StorSimple 卷。 有关卷容器的详细信息，请转到 [管理卷容器](storsimple-manage-volume-containers.md)。 卷组是为了方便备份操作而配置的卷集合。 如果你选择两个属于不同卷容器的卷，请将它们放置在单个卷组中，然后为该卷组创建备份策略，使用合适的存储帐户将每个卷备份在相应的卷容器中。

> [!NOTE]
> 卷组中的所有卷必须来自单个云服务提供商。
> 
> 

## <a name="integration-with-windows-volume-shadow-copy-service"></a>与 Windows 卷影复制服务集成
StorSimple Snapshot Manager 使用 Windows 卷影复制服务 (VSS) 来捕获应用程序一致性数据。 VSS 通过与识别 VSS 的应用程序通信来协调增量快照的创建，进而促进应用程序一致性。 VSS 确保拍摄快照时应用程序暂时处于非活动或静止状态。 

VSS 的 StorSimple Snapshot Manager 实现适用于 SQL Server 和通用 NTFS 卷。 过程如下： 

1. 请求程序通常是指数据管理和保护解决方案（例如 StorSimple Snapshot Manager）或备份应用程序，请求程序调用 VSS 并要求它从目标应用程序的编写器软件中收集信息。
2. VSS 联系编写器组件以检索数据的说明。 编写器返回要备份的数据的说明。 
3. VSS 向编写器发出指示，让应用程序做好备份准备。 编写器通过完成未完成的事务、更新事务日志等准备要备份的数据，然后通知 VSS。
4. VSS 指示编写器暂时停止应用程序的数据存储，并确保在创建卷影副本时没有将数据写入到卷中。 此步骤可确保数据一致性，并且不会超过 60 秒。
5. VSS 指示提供程序创建卷影副本。 提供程序可以基于软件或硬件，管理当前正在运行的卷并按需创建它们的卷影副本。 提供程序创建卷影副本，并在完成后通知 VSS。
6. VSS 联系编写器以通知应用程序 I/O 可以恢复，还会确认在卷影副本创建过程成功暂停了 I/O。 
7. 如果复制成功，VSS 将副本的位置返回给请求程序。 
8. 如果在创建卷影副本时写入数据，备份将不一致。 VSS 删除卷影副本并通知请求程序。 请求程序可以自动重复备份过程，或者通知管理员稍后重试。

请参阅下图。

![VSS 流程](./media/storsimple-what-is-snapshot-manager/HCS_SSM_VSS_process.png)

**Windows 卷影复制服务流程** 

## <a name="backup-types-and-backup-policies"></a>备份类型和备份策略
使用 StorSimple Snapshot Manager，可以备份数据并将其存储在本地和云中。 可以使用 StorSimple Snapshot Manager 立即备份数据，也可以使用备份策略创建用于自动执行备份的计划。 备份策略还能让你指定将保留的快照数。 

### <a name="backup-types"></a>备份类型
可以使用 StorSimple Snapshot Manager 创建以下类型的备份：

* **本地快照** — 本地快照是存储在 StorSimple 设备上的卷数据的时间点副本。 通常可以快速创建并还原此类型的备份。 可以像使用本地备份副本一样使用本地快照。
* **云快照** — 云快照是存储在云中的卷数据的时间点副本。 云快照与在不同的场外存储系统上复制的快照等效。 在灾难恢复方案中，云快照特别有用。

### <a name="on-demand-and-scheduled-backups"></a>按需和计划备份
使用 StorSimple Snapshot Manager，你可以立即开始创建一次性备份，也可以使用备份策略来计划定期备份操作。

备份策略是一组自动执行的规则，可用于计划定期备份。 通过备份策略，你可以定义创建特定卷组快照的频率和参数。 可以使用策略同时指定本地和云快照的开始和到期日期、时间、频率和保留要求。 定义后将立即应用策略。 

可以使用 StorSimple Snapshot Manager 根据需要随时配置或重新配置备份策略。 

为你创建的每个备份策略配置以下信息：

* **名称** — 选定备份策略的唯一名称。
* **类型** — 备份策略的类型；本地快照或云快照。
* **卷组** — 将选定备份策略分配到的卷组。
* **保留** — 要保留的备份副本数。 如果你选中“**全部**”框，将会保留所有的备份副本，直到达到每个卷的最大备份副本数，此时策略将失败并生成一条错误消息。 或者，可以指定要保留的备份数（1 到 64 之间）。
* **日期** — 创建备份策略的日期。

有关配置备份策略的信息，请转到[使用 StorSimple Snapshot Manager 创建和管理备份策略](storsimple-snapshot-manager-manage-backup-policies.md)。

### <a name="backup-job-monitoring-and-management"></a>监视和管理备份作业
StorSimple Snapshot Manager 可用于监视和管理即将开始、已计划和已完成备份的作业。 此外，StorSimple Snapshot Manager 提供多达 64 个已完成备份的目录。 可以使用此目录来查找和还原卷或单个文件。 

有关监视备份作业的信息，请转到[使用 StorSimple Snapshot Manager 查看和管理备份作业](storsimple-snapshot-manager-manage-backup-jobs.md)。

## <a name="next-steps"></a>后续步骤
* 了解有关[使用 StorSimple Snapshot Manager 管理你的 StorSimple 解决方案](storsimple-snapshot-manager-admin.md)的详细信息。
* 下载 [StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220)。


