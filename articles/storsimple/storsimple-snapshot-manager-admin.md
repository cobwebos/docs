---
title: "StorSimple Snapshot Manager 管理 | Microsoft Docs"
description: "提供了有关 StorSimple Snapshot Manager 解决方案管理任务和工作流详细信息的概述和链接。"
services: storsimple
documentationcenter: NA
author: SharS
manager: carolz
editor: 
ms.assetid: 1cdbb61d-bd16-4be4-ade2-ceab11508acb
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/18/2016
ms.author: v-sharos
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5e3528d490cd11fab3fb10126713f81694d7e2d0


---
# <a name="use-storsimple-snapshot-manager-to-administer-your-storsimple-solution"></a>使用 StorSimple Snapshot Manager 管理 StorSimple 解决方案
## <a name="overview"></a>概述
StorSimple Snapshot Manager 是一种 Microsoft 管理控制台 (MMC) 管理单元，可以简化 Microsoft Azure StorSimple 环境中的保护和备份管理。 借助 StorSimple Snapshot Manager，可以将数据中心和云中的 Microsoft Azure StorSimple 数据作为单一集成的存储解决方案进行管理，这样可以简化备份流程并降低成本。

StorSimple Snapshot Manager 中心管理控制台可用于创建本地数据和云数据的一致时间点备份副本。 例如，可以将控制台用于：

* 配置、备份和删除卷。
* 配置卷组以确保备份的数据是应用程序一致的。
* 管理备份策略，以便在预先确定的计划上备份数据。
* 创建数据的独立副本，它可以存储在云中并用于灾难恢复。

本文提供了介绍 StorSimple Snapshot Manager 以及如何使用它来完成系统管理任务和工作流的教程链接。

* 有关 StorSimple Snapshot Manager 组件和体系结构的详细信息，请参阅 [什么是 StorSimple Snapshot Manager？](storsimple-what-is-snapshot-manager.md) 
* 若要下载 StorSimple Snapshot Manager，请转到 [StorSimple Snapshot Manager 下载页](https://www.microsoft.com/download/details.aspx?id=44220)。
* 有关 StorSimple Snapshot Manager 部署过程，请转到[部署 StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md)。

> [!NOTE]
> 不能使用 StorSimple Snapshot Manager 管理 Microsoft Azure StorSimple 虚拟数组（也称为 StorSimple 本地虚拟设备）。
> 
> 

## <a name="storsimple-snapshot-manager-tasks-and-workflows"></a>StorSimple Snapshot Manager 任务和工作流
StorSimple Snapshot Manager 可用于监视和管理当前、 计划和已完成备份的作业。 此外，StorSimple Snapshot Manager 提供多达 64 个已完成备份的目录。 可以使用此目录来查找和还原卷或单个文件。 

| 目标... | 使用本教程... |
|:--- |:--- |
| 了解有关 StorSimple Snapshot Manager 的详细信息 |[什么是 StorSimple Snapshot Manager？ ](storsimple-what-is-snapshot-manager.md) |
| 安装 StorSimple Snapshot Manager<br>重新安装 StorSimple Snapshot Manager<br>删除 StorSimple Snapshot Manager |[部署 StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md) |
| 使用 StorSimple Snapshot Manager 菜单和功能：<ul><li>菜单栏</li><li>工具栏</li><li>作用域窗格</li><li>“结果”窗格</li><li>“操作”窗格</li><li>键盘导航和快捷键</li></ul> |[StorSimple Snapshot Manager 用户界面](storsimple-use-snapshot-manager.md) |
| 使用包含在 StorSimple Snapshot Manager 中的常见 MMC 功能：<ul><li>查看</li><li>从此处新建窗口</li><li>刷新</li><li>导出列表</li><li>帮助</li></ul> |[使用 StorSimple Snapshot Manager 中的 MMC 菜单操作](storsimple-snapshot-manager-mmc-menu.md) |
| 添加或替换设备<br>连接一个设备<br>验证导入的卷组<br>刷新连接的设备<br>对设备进行身份验证<br>查看设备详细信息<br>删除设备配置<br>更改设备密码<br>替换发生故障的设备<br> |[使用 StorSimple Snapshot Manager 连接和管理 StorSimple 设备](storsimple-snapshot-manager-manage-devices.md) |
| 装载卷<br>查看有关卷的信息<br>删除卷<br>重新扫描卷<br>配置和备份基本卷<br>配置和备份动态镜像卷 |[使用 StorSimple Snapshot Manager 查看和管理卷](storsimple-snapshot-manager-manage-volumes.md) |
| 查看卷组<br>创建卷组<br>备份卷组<br>编辑卷组<br>删除卷组 |[使用 StorSimple Snapshot Manager 创建和管理卷组](storsimple-snapshot-manager-manage-volume-groups.md) |
| 创建备份策略 <br>编辑备份策略<br>删除备份策略 |[使用 StorSimple Snapshot Manager 创建和管理备份策略](storsimple-snapshot-manager-manage-backup-policies.md) |
| 查看和管理计划的备份作业<br>查看和管理最近的备份作业<br>查看和管理当前正在运行的备份作业 |[使用 StorSimple Snapshot Manager 查看和管理备份作业](storsimple-snapshot-manager-manage-backup-jobs.md) |
| 还原卷<br>克隆卷或卷组<br>删除备份<br>恢复文件<br>还原 StorSimple Snapshot Manager 数据库 |[使用 StorSimple Snapshot Manager 管理备份目录](storsimple-snapshot-manager-manage-backup-catalog.md) |

## <a name="next-steps"></a>后续步骤
[下载 StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220)。




<!--HONumber=Nov16_HO3-->


