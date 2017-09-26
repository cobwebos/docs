---
title: "StorSimple Snapshot Manager 用户界面 | Microsoft Docs"
description: "介绍 StorSimple Snapshot Manager 用户界面，并说明如何使用它来管理备份作业和备份目录。"
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: 
ms.assetid: c7d91892-2881-41a2-a7a2-908dc3646493
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.custom: 
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: b48c507e38eb7cadff56259f617e336e4efe5708
ms.contentlocale: zh-cn
ms.lasthandoff: 07/08/2017

---
# <a name="use-storsimple-snapshot-manager-user-interface-to-manage-backup-jobs-and-backup-catalog"></a>使用 StorSimple Snapshot Manager 用户界面管理备份作业和备份目录

## <a name="overview"></a>概述
StorSimple Snapshot Manager 具有直观的用户界面，可用于执行和管理备份。 本教程将介绍用户界面，然后说明如何使用每个组件。 有关 StorSimple Snapshot Manager 的详细说明，请参阅[什么是 StorSimple Snapshot Manager？](storsimple-what-is-snapshot-manager.md)

### <a name="console-description"></a>控制台说明
要查看用户界面，请单击桌面上的 StorSimple Snapshot Manager 图标。 此时会出现控制台窗口，如下图所示。

![StorSimple Snapshot Manager 窗格](./media/storsimple-use-snapshot-manager/HCS_SSM_gui_panes.png)

控制台窗口包含五个主要元素。 单击相应链接可查看每个元素的完整说明。

* [菜单栏](#menu-bar) 
* [工具栏](#tool-bar) 
* [作用域窗格](#scope-pane) 
* [结果窗格](#results-pane) 
* [操作窗格](#actions-pane) 

此外，StorSimple Snapshot Manager 还支持[键盘导航和许多快捷键](#keyboard-navigation-and-shortcuts)。

### <a name="console-accessibility"></a>控制台辅助功能
StorSimple Snapshot Manager 用户界面支持 Windows 操作系统和 Microsoft 管理控制台 (MMC) 提供的辅助功能，以及某些特定于 StorSimple Snapshot Manager 的键盘快捷键。 

* 有关 Windows 辅助功能的说明，请转到 [Windows 的键盘快捷键](https://support.microsoft.com/kb/126449)。 
* 有关 MMC 辅助功能的说明，请转到 [MMC 3.0 的辅助功能](https://technet.microsoft.com/library/cc766075.aspx)
* 有关 StorSimple Snapshot Manager 辅助功能的说明，请转到[键盘导航和快捷键](#keyboard-navigation-and-shortcuts)。

## <a name="menu-bar"></a>菜单栏
控制台窗口顶部的菜单栏包含“[文件](#file-menu)”、“[操作](#action-menu)”、“[视图](#view-menu)”、“[收藏夹](#favorites-menu)”、“[窗口](#window-menu)”和“[帮助](#help-menu)”菜单。

单击菜单栏上的任何项目，可查看该菜单上提供的命令列表。 以下示例显示了菜单栏上选定的“**视图**”菜单。

![选定的“视图”菜单](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png)

### <a name="file-menu"></a>“文件”菜单
“**文件**”菜单包含标准的 Microsoft 管理控制台 (MMC) 命令。

#### <a name="menu-access"></a>菜单访问
若要查看“**文件**”菜单，请单击菜单栏上的“**文件**”。 此时会出现以下菜单。

![StorSimple Snapshot Manager 的“文件”菜单](./media/storsimple-use-snapshot-manager/HCS_SSM_FileMenu.png) 

#### <a name="menu-description"></a>菜单说明
下表介绍了“**文件**”菜单上显示的项目。

| 菜单项 | 说明 |
|:--- |:--- |
| 新建 |单击“**新建**”可基于 StorSimple Snapshot Manager 新建控制台。 |
| 打开 |单击“**打开**”可打开现有的控制台。 |
| 保存 |单击“**保存**”可保存当前控制台。 |
| 另存为 |单击“**另存为**”可创建当前控制台的重命名新实例。 使用“**另存为**”选项可自定义并保存视图以供将来检索。 例如，可以创建指向特定服务器的 StorSimple Snapshot Manager 管理单元。 |
| 添加/删除管理单元 |单击“**添加/删除管理单元**”可以添加或删除管理单元并对“**作用域**”窗格中的节点进行排列。 有关详细信息，请转到[在 MMC 3.0 中添加、删除和组织管理单元和扩展](https://technet.microsoft.com/library/cc722035.aspx)。 |
| 选项 |单击“**选项**”可更改控制台图标，指定用户访问模式和权限，或者删除控制台文件以增加可用磁盘空间。 |
| 文件路径的列表 |单击编号列表中的路径可重新打开最近打开的文件。 |
| 退出 |单击“**退出**”可关闭“**文件**”菜单。 |

### <a name="action-menu"></a>“操作”菜单
使用“**操作**”菜单从可用操作中进行选择。 可供你使用的项取决于你在“**作用域**”窗格或“**结果**”窗格中所做的选择。

#### <a name="menu-access"></a>菜单访问
若要查看“**操作**”菜单，请执行以下操作之一：

* 右键单击“**作用域**”窗格或“**结果**”窗格中的项。
* 选择“**作用域**”窗格或“**结果**”窗格中的项，然后单击菜单栏上的“**操作**”。 

例如，如果你选择“**作用域**”窗格中的顶级节点，然后右键单击或单击菜单栏中的“**操作**”，即出现以下菜单。

![StorSimple Snapshot Manager 的“操作”菜单](./media/storsimple-use-snapshot-manager/HCS_SSM_Action_menu.png)

“**操作**”窗格（控制台右侧）包含和“**操作**”菜单相同的操作列表。 此外，“**操作**”窗格还包含“**视图**”菜单选项，使你能够创建“**结果**”窗格的自定义视图。

![打开带有“视图”菜单的“操作”窗格](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

#### <a name="menu-description"></a>菜单说明
下表包含按字母顺序排列的 StorSimple Snapshot Manager 操作列表。 

* “**操作**”列列出了可对节点和结果执行的操作。 
* “**导航**”列说明如何显示合适的“**操作**”菜单，以便你能够选择操作。 有些操作显示在多个“**操作**”菜单中。 对于这些操作，请从符号列表中选择一个“**导航**”选项。 
* “**说明**”列介绍如何使用“**操作**”菜单或“操作”窗格上的每个操作，并说明其用途。

> [!NOTE]
> “**操作**”窗格和“**操作**”菜单包含其他选项，例如“**视图**”、“**从此处新建窗口**”、“**刷新**”、“**导出列表**”和“**帮助**”。 这些选项作为 MMC 的组成部分提供，而非特定于 StorSimple Snapshot Manager。 此表包括这些选项的说明。
> 
> 

| 操作 | 导航 | 说明 |
|:--- |:--- |:--- |
| 身份验证 |单击“**设备**”节点，然后右键单击“**结果**”窗格中的设备。 |单击“**验证**”可输入为设备配置的密码。 |
| 克隆 |依次展开“**备份目录**”、“**云快照**”，单击带日期的备份，然后在“**结果**”窗格中选择卷。 |单击“**克隆**”可创建云快照的副本并将其存储在你指定的位置。 |
| 配置设备 |右键单击“**设备**”节点。 |单击“**配置设备**”可配置一个或多个连接到 Windows 主机的设备。 |
| 创建备份策略 |执行下列操作之一：<ul><li>右键单击“**备份策略**”。</li><li>单击或展开“**卷组**”，然后右键单击卷组。</li><li>单击或展开“**备份目录**”，然后右键单击卷组。</li></ul> |单击“**创建备份策略**”可配置卷组的计划备份。 |
| 创建卷组 |执行下列操作之一：<ul><li>单击“**卷**”节点，然后在“**结果**”窗格中右键单击卷。</li><li>右键单击“**卷组**”节点。</li></ul> |单击“**创建卷组**”可以将卷分配给卷组。 |
| 删除 |单击节点或结果（此项显示在许多“**操作**”菜单和“**操作**”窗格中。） |单击“**删除**”可删除所选的节点或结果。 出现确认对话框时，请确认或取消删除操作。 |
| 详细信息 |单击“**设备**”节点，然后在“**结果**”窗格中右键单击设备。 |单击“**详细信息**”可查看设备的配置详细信息。 |
| 编辑 |单击“**备份策略**”，然后在“**结果**”窗格中右键单击策略。 |单击“**编辑**”可更改卷组的备份计划。 |
| 导出列表 |单击任意节点或结果（此项显示在所有的“**操作**”菜单和“**操作**”窗格中。） |单击“**导出列表**”可以将列表保存在逗号分隔值 (CSV) 文件中。 然后可以将此文件导入到电子表格应用程序中进行分析。 |
| 帮助 |单击任意节点或结果。 （此项显示在所有的“**操作**”菜单和“**操作**”窗格中。） |单击“**帮助**”可在单独的浏览器窗口中打开联机帮助。 |
| 从此处新建窗口 |单击任意节点或结果（此项显示在所有的“**操作**”菜单和“**操作**”窗格中。） |单击“**从此处新建窗口**”可打开一个新的 StorSimple Snapshot Manager 窗口。 |
| 刷新 |单击任意节点或结果（此项显示在所有的“**操作**”菜单和“**操作**”窗格中。） |单击“**刷新**”可更新当前显示的 StorSimple Snapshot Manager 窗口。 |
| 刷新设备 |单击“**设备**”节点，然后右键单击“**结果**”窗格中的设备。 |单击“**刷新设备**”可以将特定的连接设备与 StorSimple Snapshot Manager 同步。 |
| 刷新设备 |右键单击“**设备**”节点。 |单击“**刷新设备**”可以将连接设备列表与 StorSimple Snapshot Manager 同步。 |
| 重新扫描卷 |右键单击“**卷**”节点。 |单击“**重新扫描卷**”可更新“**结果**”窗格中显示的卷列表。 |
| 还原 |展开“**备份目录**”，展开卷组，再展开“**本地快照**”或“**云快照**”，然后右键单击备份。 |单击“**还原**”可将当前卷组数据替换为所选备份中的数据。 |
| 执行备份 |执行下列操作之一：<ul><li>展开“**卷组**”，然后右键单击卷组。</li><li>展开“**备份目录**”，然后右键单击卷组。</li></ul> |单击“**执行备份**”可立即开始备份作业。 |
| 切换导入显示 |右键单击“**作用域**”窗格中的顶级节点（示例中的 **StorSimple Snapshot Manager** 节点）。 |单击“切换导入显示”可显示或隐藏从 StorSimple Device Manager 服务仪表板导入的卷组和关联的备份。 |

### <a name="view-menu"></a>“视图”菜单
使用“**视图**”菜单可创建“**结果**”窗格内容的自定义视图。 “**视图**”菜单包含“**添加/删除列**”和“**自定义**”选项。

#### <a name="menu-access"></a>菜单访问
可以访问菜单栏或“**操作**”窗格中的“**视图**”菜单。

![StorSimple Snapshot Manager 的“视图”菜单](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png) 

#### <a name="menu-description"></a>菜单说明
下表介绍了“**视图**”菜单上显示的项目。

| 菜单项 | 说明 |
|:--- |:--- |
| 添加/删除列 |单击“**添加/删除列**”可在“**结果**”窗格中添加或删除列。 |
| 自定义 |单击“**自定义**”可显示或隐藏 StorSimple Snapshot Manager 控制台窗口中的项。 |

### <a name="favorites-menu"></a>“收藏夹”菜单
使用“**收藏夹**”菜单可添加、删除和整理经常使用的页面视图以及任务。 

#### <a name="menu-access"></a>菜单访问
可以访问菜单栏中的“**收藏夹**”菜单。

![StorSimple Snapshot Manager 的“收藏夹”菜单](./media/storsimple-use-snapshot-manager/HCS_SSM_FavoritesMenu.png)

#### <a name="menu-description"></a>菜单说明
下表介绍了“**收藏夹**”菜单上显示的项目。

| 菜单项 | 说明 |
|:--- |:--- |
| 添加到收藏夹 |单击“**添加到收藏夹**”可将当前视图添加到收藏夹列表。 |
| 整理收藏夹 |单击“**整理收藏夹**”可整理收藏夹文件夹的内容。 |

### <a name="window-menu"></a>“窗口”菜单
使用“**窗口**”菜单可添加和重新排列 StorSimple Snapshot Manager 控制台窗口。

#### <a name="menu-access"></a>菜单访问
可以访问菜单栏中的“**窗口**”菜单。

![StorSimple Snapshot Manager 的“窗口”菜单](./media/storsimple-use-snapshot-manager/HCS_SSM_WindowMenu.png)

菜单底部的编号列表显示当前打开的窗口。 单击该列表中的任何窗口可以将该窗口调到前台运行。 

#### <a name="menu-description"></a>菜单说明
下表介绍了“窗口”菜单上显示的项目。

| 菜单项 | 说明 |
|:--- |:--- |
| 新建窗口 |单击“**新建窗口**”可打开新的控制台窗口（除现有窗口之外）。 |
| 级联 |单击“**级联**”可以级联方式显示打开的控制台窗口。 |
| 横向平铺 |单击“**横向平铺**”可以磁贴（或网格）方式显示打开的控制台窗口。 |
| 排列图标 |如果有多个控制台窗口打开并分散在桌面上，请将它们最小化，然后单击“**排列图标**”让它们横向排列在屏幕底部。 |

### <a name="help-menu"></a>帮助菜单
可以使用“**帮助**”菜单查看针对 StorSimple Snapshot Manager 和 MMC 的可用联机帮助。 此外，还可以查看当前安装在系统中的 MMC 和 StorSimple Snapshot Manager 软件版本的相关信息。 

可以访问菜单栏中的“**帮助**”菜单。 还可以从“**操作**”窗格中访问 StorSimple Snapshot Manager 的帮助主题。

![StorSimple Snapshot Manager 的“帮助”菜单](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpMenu.png)

#### <a name="menu-description"></a>菜单说明
下表介绍了“帮助”菜单上显示的项目。

| 菜单项 | 说明 |
|:--- |:--- |
| 有关 StorSimple Snapshot Manager 的帮助 |单击“**有关 StorSimple Snapshot Manager 的帮助**”可在一个单独的窗口中打开 StorSimple Snapshot Manager 帮助。 |
| 帮助主题 |单击“**帮助主题**”可在一个单独的窗口中打开 MMC 联机帮助。 |
| TechCenter 网站 |单击“**TechCenter 网站**”可在一个单独的窗口中打开 Microsoft TechNet 技术中心主页。 |
| 关于 Microsoft 管理控制台 |单击“**关于 Microsoft 管理控制台**”可查看系统上安装的 Microsoft 管理控制台版本。 |
| 关于 StorSimple Snapshot Manager |单击“**关于 StorSimple Snapshot Manager**”可查看系统上安装的管理单元版本。 |

## <a name="tool-bar"></a>工具栏
位于菜单栏下方的工具栏包含导航和任务图标。 每个图标都是特定任务的一种快捷方式。

### <a name="icon-descriptions"></a>图标说明
下表介绍了工具栏上显示的图标。 

| 图标 | 说明 |
|:--- |:--- |
| ![向左键](./media/storsimple-use-snapshot-manager/HCS_SSM_LeftArrow.png) |单击向左键图标可返回上一页。 |
| ![向右键](./media/storsimple-use-snapshot-manager/HCS_SSM_RightArrow.png) |单击向右键可转到下一页（如果箭头为灰色，则操作不可用）。 |
| ![向上图标](./media/storsimple-use-snapshot-manager/HCS_SSM_Up.png) |单击向上图标可转到控制台树中的上一级（“**作用域**”窗格）。 |
| ![显示/隐藏控制台树](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowConsoleTree.png) |单击显示/隐藏控制台树图标可显示或隐藏“**作用域**”窗格。 |
| ![导出列表](./media/storsimple-use-snapshot-manager/HCS_SSM_ExportListIcon.png) |单击导出列表图标可以将列表导出到指定的 CSV 文件。 |
| ![帮助图标](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpIcon.png) |单击帮助图标可打开 MMC 联机帮助主题。 |
| ![显示/隐藏操作窗格](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowAction.png) |单击显示/隐藏“**操作**”窗格图标可显示或隐藏“**操作**”窗格。 |

## <a name="scope-pane"></a>作用域窗格
“**作用域**”窗格是 StorSimple Snapshot Manager UI 中最左侧的窗格。 它包含控制台（或节点）树，是 StorSimple Snapshot Manager 的主要导航机制。 

### <a name="scope-pane-structure"></a>作用域窗格结构
“**作用域**”窗格包含按树形结构排列的一系列的可单击对象（节点）。 

![作用域窗格](./media/storsimple-use-snapshot-manager/HCS_SSM_Scope_pane.png) 

* 若要展开或折叠节点，请单击节点名称旁边的箭头图标。
* 若要查看节点的状态或内容，请单击节点名称。 这些信息将显示在“**结果**”窗格中。 

“**作用域**”窗格包含以下节点： 

* [设备节点](#devices-node) 
* [卷节点](#volumes-node) 
* [卷组节点](#volume-groups-node) 
* [备份策略节点](#backup-policies-node) 
* [备份目录节点](#backup-catalog-node) 
* [作业节点](#jobs-node) 

### <a name="scope-pane-tasks"></a>作用域窗格任务
可以使用“**作用域**”窗格完成针对特定节点的操作。 若要选择任务，请执行以下任一操作：

* 右键单击节点，然后从出现的菜单中选择任务。
* 单击节点，然后单击菜单栏上的“**操作**”。 从出现的菜单中选择任务。
* 单击节点，然后在“**操作**”窗格中选择操作。

选择节点并使用其中任一方法来查看任务列表时，仅显示可以对该节点执行的操作。

### <a name="devices-node"></a>设备节点
“**设备**”节点表示连接到 StorSimple Snapshot Manager 的 StorSimple 设备和 StorSimple 虚拟设备。 选择此节点可连接和配置设备，导入其关联的卷、卷组和现有的备份副本。 可将多个设备连接到一台主机。

* 若要展开此节点，请单击“**设备**”旁边的箭头图标。
* 若要查看可用操作的菜单，请右键单击“**卷**”节点或展开的视图中显示的任何节点。
* 若要查看配置设备的列表，请单击“**作用域**”窗格中的“**设备**”。 设备列表以及每个设备的相关信息都显示在“**结果**”窗格中。

### <a name="volumes-node"></a>卷节点
“**卷**”节点表示与主机装载的卷相对应的驱动器，其中包括通过 iSCSI 和设备发现的驱动器。 使用此节点以查看可用卷的列表，并将单独的卷分配给卷组。

* 若要展开此节点，请单击“**卷**”旁边的箭头图标。
* 若要查看可用操作的菜单，请右键单击“**卷**”节点或展开视图中显示的任何节点。
* 若要查看卷的列表，请单击“**作用域**”窗格中的“**卷**”。 卷列表以及每个卷的相关信息都显示在“**结果**”窗格中。

### <a name="volume-groups-node"></a>卷组节点
卷组也称为一致性组。 每个卷组都是一个与应用程序相关的卷的池，可用来帮助确保备份操作过程中的应用程序一致性。 使用“**卷组**”节点可配置这些组并执行交互备份或创建备份计划。 

* 若要展开此节点，请单击“**卷组**”旁边的箭头图标。
* 若要查看可用操作的菜单，请右键单击“**卷组**”节点或展开的视图中显示的任何节点。
* 若要查看卷组的列表，请单击“**作用域**”窗格中的“**卷组**”。 卷组列表以及每个卷组的相关信息都显示在“**结果**”窗格中。

### <a name="backup-policies-node"></a>备份策略节点
备份策略是本地快照和云快照的作业计划。 使用“**备份策略**”节点可指定备份的创建频率以及保留时间。 

* 若要展开此节点，请单击“**备份策略**”旁边的箭头图标。
* 若要查看可用操作的菜单，请右键单击“**备份目录**”节点或展开的视图中显示的任何节点。
* 若要查看备份策略列表，请单击“**作用域**”窗格中的“**备份策略**”。 备份策略列表以及每个策略的相关信息都显示在“**结果**”窗格中。

> [!NOTE]
> 最多可以保留 64 个备份。


### <a name="backup-catalog-node"></a>备份目录节点
“**备份目录**”节点包含 Azure StorSimple 卷的现场和非现场备份列表。 此节点按卷组排列，每个卷组容器包含单独的本地快照结构（“**本地快照**”节点）和云快照结构（“**云快照**”节点）。 展开时，每个卷组容器列出以交互方式或通过配置的策略成功执行的所有备份。

* 若要展开此节点，请单击“**备份目录**”旁边的箭头图标。
* 若要查看可用操作的菜单，请右键单击“**备份目录**”节点或展开视图中显示的任何节点。
* 若要查看备份快照列表，请单击“**作用域**”窗格中的“**备份目录**”。 快照列表以及每个快照的相关信息都显示在“**结果**”窗格中。

### <a name="local-snapshots-node"></a>本地快照节点
“**本地快照**”节点列出特定卷组的本地快照。 该节点位于“**作用域**”窗格中的“**备份目录**”节点下。 本地快照是存储在 Azure StorSimple 设备上的卷数据的时间点副本。 通常可以快速创建并还原此类型的备份。 可以像使用本地备份副本一样使用本地快照。

* 若要展开此节点，请单击“**本地快照**”旁边的箭头图标。
* 若要查看可用操作的菜单，请右键单击“**本地快照**”节点或展开视图中显示的任何节点。
* 若要查看本地快照列表，请单击“**作用域**”窗格中的“**本地快照**”。 快照列表以及每个快照的相关信息都显示在“**结果**”窗格中。

### <a name="cloud-snapshots-node"></a>云快照节点
“**云快照**”节点列出特定卷组的云快照。 该节点位于“**作用域**”窗格中的“**备份目录**”节点下。 云快照是存储在云中的卷数据的时间点副本。 云快照与在不同的场外存储系统上复制的快照等效。 在灾难恢复方案中，云快照特别有用。

* 若要展开此节点，请单击“**云快照**”旁边的箭头图标。
* 若要查看可用操作的菜单，请右键单击“**云快照**”节点或展开视图中显示的任何节点。
* 若要查看本地快照列表，请单击“**作用域**”窗格中的“**云快照**”。 快照列表以及每个快照的相关信息都显示在“**结果**”窗格中。

### <a name="jobs-node"></a>作业节点
“**作业**”节点包含有关已经计划、正在运行和最近完成的备份作业的信息。 

* 若要展开此节点，请单击“**作业**”旁边的箭头图标。
* 若要查看可用操作的菜单，请右键单击“**作业**”节点或展开视图中显示的任何节点。
* 若要查看已计划作业的列表，请展开“**作业**”节点，然后单击“**已计划**”。 此前配置的作业列表以及每个作业的相关信息都显示在“**结果**”窗格中。 
* 若要查看最近完成的作业列表，请展开“**作业**”节点，然后单击“**过去 24 小时**”。 过去 24 小时内完成的作业列表将显示在“**结果**”窗格中。 “**结果**”窗格还包含每个已完成作业的相关信息。
* 若要查看当前正在运行的作业列表，请展开“**作业**”节点，然后单击“**正在运行**”。 当前正在运行的作业列表以及每个作业的相关信息都显示在“**结果**”窗格中。

## <a name="results-pane"></a>“结果”窗格
“**结果**”窗格是 StorSimple Snapshot Manager UI 的中心窗格。 它包含在“**作用域**”窗格中选择的节点的列表和详细状态信息。

### <a name="example"></a>示例
若要查看以下示例，请单击“**作用域**”窗格中的“**卷组**”节点。 “**结果**”窗格显示卷组列表，其中包含每个组的详细信息。

![“结果”窗格](./media/storsimple-use-snapshot-manager/HCS_SSM_Results_pane.png) 

可以配置“**结果**”窗格中显示的详细信息：在“**作用域**”窗格中右键单击某个节点，单击“**视图**”，然后单击“**添加/删除列**”。

## <a name="actions-pane"></a>“操作”窗格
“**操作**”窗格是 StorSimple Snapshot Manager UI 中的右窗格。 它包含可以对在“**作用域**”或“**结果**”窗格中所选的节点、视图或数据执行的操作的菜单。 “**操作**”窗格包含与“**作用域**”和“**结果**”窗格中项目的“**操作**”菜单相同的命令。 有关每个操作的说明，请参阅“**操作**”菜单部分中的表。

### <a name="examples"></a>示例
若要查看以下示例，请在“**作用域**”窗格中展开“**作业**”节点，然后单击“**已计划**”。 “**操作**”窗格中显示“**已计划**”节点的可用操作。

![“操作”窗格中的计划作业示例](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane.png) 

若要查看更多选项，请在“**作用域**”窗格中展开“**作业**”节点，单击“**已计划**”，然后单击“**结果**”窗格中的计划作业。 “**操作**”窗格中显示计划作业的可用操作，如以下示例所示。

![“操作”窗格中的作业操作示例](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

## <a name="keyboard-navigation-and-shortcuts"></a>键盘导航和快捷键
StorSimple Snapshot Manager 支持 Windows 操作系统和 Microsoft 管理控制台 (MMC) 的辅助功能。 它还包括一些专用于 StorSimple Snapshot Manager 的键盘导航功能和快捷键，如以下各部分中所述。

* [键盘导航键](#keyboard-navigation-keys) 
* [菜单栏的快捷键](#menu-bar-shortcut-keys) 
* [作用域窗格的快捷键](#scope-pane-shortcut-keys) 

### <a name="keyboard-navigation-keys"></a>键盘导航键
下表介绍了可用于导航 StorSimple Snapshot Manager 用户界面的键。 

| 导航键 | 操作 |
|:--- |:--- |
| 下箭头键 |使用向下键可垂直移动到菜单或窗格中的下一项。 |
| Enter |按 Enter 键可完成某项操作，然后继续执行下一步。 例如，可以按下 Enter 来选择“**下一步**”、“**确定**”或“**创建**”，然后转到向导的下一步。 |
| Esc |按 Esc 键可关闭菜单或取消并关闭页面。 |
| F1 |按 F1 键可查看当前活动窗口的帮助主题。 |
| F5 |按 F5 键可刷新节点。 |
| F6 |按 F6 键可从“**作用域**”窗格移至“**结果**”窗格。 |
| F10 |按 F10 键可转到菜单栏。 |
| 向左键 |使用向左键可从一个菜单栏选项水平移至上一个选项。 移至菜单栏中的上一项时，将显示上一项的操作（或上下文）菜单。 |
| 向右键 |使用向右键可从一个菜单栏选项水平移至下一个选项。 移至菜单栏中的下一项时，将显示新项目的操作（或上下文）菜单。 |
| Tab 键 |使用 Tab 键移至控制台的下一个窗格或页面中的下一选定内容或文本框。 |
| 向上键 |使用向上键可垂直移至菜单或窗格中的上一项。 |

### <a name="menu-bar-shortcut-keys"></a>菜单栏的快捷键
下表介绍了菜单栏的快捷键组合。 按下快捷键且菜单打开后，可以使用菜单的快捷键（菜单上带下划线的键）。 有关菜单栏的详细信息，请转至[菜单栏](#menu-bar)。

| 快捷方式 | 结果 | 菜单的快捷键 | 结果 |
|:--- |:--- |:--- |:--- |
| ALT+F |可打开“**文件**”菜单。 |N |可打开一个新的控制台实例。 |
|  |O |可打开“**管理工具**”页。 | |
|  |S |保存 StorSimple Snapshot Manager 控制台。 | |
|  |A |可打开“**另存为**”页。 | |
|  |M |可打开“**添加/删除管理单元**”页。 | |
|  |P |可打开“**选项**”页。 | |
|  |H |可打开联机帮助。 | |
| ALT+A |可打开“**操作**”菜单。 |I |可打开和关闭导入显示选项。 |
|  |W |可打开新的 StorSimple Snapshot Manager 控制台。 | |
|  |F |可更新 StorSimple Snapshot Manager 控制台。 | |
|  |L |可打开“**导出列表**”页。 | |
|  |H |可打开联机帮助。 | |
| ALT+V |可打开“**视图**”菜单。 |A |可打开“**添加/删除列**”页。 |
|  |U |可打开“**自定义视图**”页。 | |
| ALT+O |可打开“**收藏夹**”菜单。 |A |可打开“**添加到收藏夹**”页。 |
|  |O |可打开“**整理收藏夹**”页。 | |
| ALT+W |可打开“**窗口**”菜单。 |N |可打开另一个 StorSimple Snapshot Manager 窗口。 |
|  |C |可显示以级联方式打开的所有控制台窗口。 | |
|  |T |可显示以网格方式打开的所有控制台窗口。 | |
|  |I |可将图标横向排列在屏幕底部。 | |
| ALT+H |可打开“**帮助**”菜单。 |H |可打开联机帮助。 |
|  |T |可打开 Microsoft TechNet 技术中心网页。 | |
|  |A |可打开“**关于 Microsoft 管理控制台**”页。 | |

### <a name="scope-pane-shortcut-keys"></a>作用域窗格的快捷键
下表显示了每个节点在“**作用域**”窗格中的快捷键组合。 

* [设备节点的快捷键](#devices-node-shortcut-keys)
* [卷节点的快捷键](#volumes-node-shortcut-keys)
* [卷组节点的快捷键](#volume-groups-node-shortcut-keys)
* [备份策略节点的快捷键](#backup-policies-node-shortcut-keys)
* [备份目录节点的快捷键](#backup-catalog-node-shortcut-keys)
* [作业节点的快捷键](#jobs-node-shortcut-keys)

#### <a name="devices-node-shortcut-keys"></a>设备节点的快捷键
| 菜单的快捷键 | 结果 |
|:--- |:--- |
| C |打开“**设备配置**”页。 |
| D |刷新设备和设备详细信息的列表。 |
| V |可打开“**视图**”菜单。 |
| W |打开以“**详细信息**”节点为重点的新的 StorSimple Snapshot Manager 制台。 |
| F |可更新 StorSimple Snapshot Manager 控制台。 |
| L |可打开“**导出列表**”页。 |
| H |可打开联机帮助。 |

#### <a name="volumes-node-shortcut-keys"></a>卷节点的快捷键
| 菜单的快捷键 | 结果 |
|:--- |:--- |
| V |更新卷列表。 |
| V（按两次） |可打开“**视图**”菜单。 |
| W |打开以“**卷**”节点为重点的新的 StorSimple Snapshot Manager 控制台。 |
| F |可更新 StorSimple Snapshot Manager 控制台。 |
| L |可打开“**导出列表**”页。 |
| H |可打开联机帮助。 |

#### <a name="volume-groups-node-shortcut-keys"></a>卷组节点的快捷键
| 菜单的快捷键 | 结果 |
|:--- |:--- |
| G |可打开“**创建卷组**”页。 |
| V |可打开“**视图**”菜单。 |
| W |可打开以“**卷组**”节点为重点的新的 StorSimple Snapshot Manager 控制台。 |
| F |可更新 StorSimple Snapshot Manager 控制台。 |
| L |可打开“**导出列表**”页。 |
| H |可打开联机帮助。 |

#### <a name="backup-policies-node-shortcut-keys"></a>备份策略节点的快捷键
| 菜单的快捷键 | 结果 |
|:--- |:--- |
| B |可打开“**创建策略**”页。 |
| V |可打开“**视图**”菜单。 |
| W |可打开以“**卷组**”节点为重点的新的 StorSimple Snapshot Manager 控制台。 |
| F |可更新 StorSimple Snapshot Manager 控制台。 |
| L |打开“导出列表”页。 |
| H |可打开联机帮助。 |

#### <a name="backup-catalog-node-shortcut-keys"></a>备份目录节点的快捷键
| 菜单的快捷键 | 结果 |
|:--- |:--- |
| W |可打开以“**卷组**”节点为重点的新的 StorSimple Snapshot Manager 控制台。 |
| F |可更新 StorSimple Snapshot Manager 控制台。 |
| H |可打开联机帮助。 |

#### <a name="jobs-node-shortcut-keys"></a>作业节点的快捷键
| 菜单的快捷键 | 结果 |
|:--- |:--- |
| V |可打开“**视图**”菜单。 |
| W |可打开以“**作业**”节点为重点的新的 StorSimple Snapshot Manager 控制台。 |
| F |可更新 StorSimple Snapshot Manager 控制台。 |
| L |可打开“**导出列表**”页。 |
| H |打开联机帮助 |

## <a name="next-steps"></a>后续步骤
* 了解如何[使用 StorSimple Snapshot Manager 管理你的 StorSimple 解决方案](storsimple-snapshot-manager-admin.md)。
* 了解如何[使用 StorSimple Snapshot Manager 连接和管理设备](storsimple-snapshot-manager-manage-devices.md)。


