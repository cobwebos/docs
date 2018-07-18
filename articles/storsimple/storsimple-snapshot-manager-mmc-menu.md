---
title: StorSimple Snapshot Manager MMC 菜单操作 | Microsoft Docs
description: 介绍了如何使用 StorSimple Snapshot Manager 中的标准 Microsoft 管理控制台 (MMC) 菜单操作。
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 78ef81af-0d3a-4802-be54-ad192f9ac8a6
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: 48f439a566a8067e153aab4fb789937d2f91268d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
ms.locfileid: "23109965"
---
# <a name="use-the-mmc-menu-actions-in-storsimple-snapshot-manager"></a>使用 StorSimple Snapshot Manager 中的 MMC 菜单操作

## <a name="overview"></a>概述
在 StorSimple Snapshot Manager 中，将看到以下操作列于所有的操作菜单和“**操作**”窗格的所有变体上。

* 查看
* 从此处新建窗口 
* 刷新 
* 导出列表 
* 帮助 

这些操作是 Microsoft 管理控制台 (MMC) 的组成部分，而非特定于 StorSimple Snapshot Manager。 本教程介绍了这些操作，并阐述了如何在 StorSimple Snapshot Manager 中使用其中的每一个操作。

## <a name="view"></a>查看
可以使用“**视图**”选项更改“**结果**”窗格视图，以更改控制台窗口视图。 

#### <a name="to-change-the-results-pane-view"></a>更改“结果”窗格视图
1. 单击桌面图标启动 StorSimple Snapshot Manager。
2. 在“**作用域**”窗格中，右键单击任意节点或者展开该节点，右键单击“**结果**”窗格中的任意一项，并单击“**视图**”选项。 
3. 若要添加或删除“**结果**”窗格中出现的列，单击“**添加/删除列**”。 “**添加/删除列**”对话框出现。
   
    ![添加或删除“结果”窗格中的列](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Add_remove_columns.png) 
4. 如下所示填写表单：
   
   * 从“**可用**”列列表中选择项，然后单击“**添加**”以将选择的项添加到“**显示的列**”列表中。 
   * 单击“**显示的列**”列表中的项，然后单击“**删除**”将对应的项从列表中删除。 
   * 在“**显示的列**”列表中选择一项，然后单击“**上移**”或“**下移**”将对应的项在列表中向上或向下移动。 
   * 单击“**还原默认设置**”可恢复为默认的“**结果**”窗格配置。 
5. 选择完之后，单击“**确定**”。 

#### <a name="to-change-the-console-window-view"></a>更改控制台窗口视图
1. 单击桌面图标启动 StorSimple Snapshot Manager。
2. 在“**作用域**”窗格中，右键单击任意节点，单击“**视图**”，并单击“**自定义**”。 “**自定义**”对话框出现。
   
    ![自定义控制台窗口](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Customize.png) 
3. 选中或清除复选框以显示或隐藏控制台窗口中的项。 选择完之后，单击“**确定**”。

## <a name="new-window-from-here"></a>从此处新建窗口
可以使用“**从此处新建窗口**”选项打开新的控制台窗口。

#### <a name="to-open-a-new-console-window"></a>打开新的控制台窗口
1. 单击桌面图标启动 StorSimple Snapshot Manager。
2. 在“**作用域**”窗格中，右键单击任意节点，并单击“**从此处新建窗口**”。 
   
    新的窗口出现，并仅显示已选择的作用域。 例如，如果右键单击“**备份策略**”节点，新的窗口将仅在“**作用域**”窗格中显示“**备份策略**”节点并仅在“**结果**”窗格中显示指定的备份策略列表。 请参阅以下示例。
   
    ![从此处新建窗口](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_NewWindow.png) 

## <a name="refresh"></a>刷新
可以使用“**刷新**”操作更新控制台窗口。

#### <a name="to-update-the-console-window"></a>更新控制台窗口
1. 单击桌面图标启动 StorSimple Snapshot Manager。
2. 在“**作用域**”窗格中，右键单击任意节点或者展开该节点，右键单击“**结果**”窗格中的任意一项，并单击“**刷新**”选项。 

## <a name="export-list"></a>导出列表
可以使用“**导出列表**”操作将列表保存为逗号分隔值 (CSV) 文件。 例如，可以导出备份策略列表或备份目录。 然后可以将 CSV 文件导入到电子表格应用程序进行分析。

#### <a name="to-save-a-list-in-a-comma-separated-value-csv-file"></a>将列表保存为逗号分隔值 (CSV) 文件
1. 单击桌面图标启动 StorSimple Snapshot Manager。 
2. 在“**作用域**”窗格中，右键单击任意节点或者展开该节点，右键单击“**结果**”窗格中的任意一项，并单击“**导出列表**”。 
3. “**导出列表**”对话框出现。 如下所示填写表单： 
   
   1. 在“**文件名**”框中，键入 CSV 文件的名称或者单击箭头在下拉列表中选择。
   2. 在“**保存类型**”框中，单击箭头在下拉列表中选择一种文件类型。
   3. 要只保存已选定的项，则选定相应的行并单击“**只保存选定行**”复选框。 若要保存导出的所有列表，则清除“**只保存选定行**”复选框。
   4. 单击“保存” 。
      
      ![将列表导出为逗号分隔值文件](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Export_List.png) 

## <a name="help"></a>帮助
可以使用“**帮助**”菜单查看针对 StorSimple Snapshot Manager 和 MMC 的可用联机帮助。

#### <a name="to-view-available-online-help"></a>查看可用的联机帮助
1. 单击桌面图标启动 StorSimple Snapshot Manager。
2. 在“**作用域**”窗格中，右键单击任意节点或者展开该节点，并右键单击“**结果**”窗格中的任意一项，然后单击“**帮助**”。 

## <a name="next-steps"></a>后续步骤
* 了解有关 [StorSimple Snapshot Manager 用户界面](storsimple-use-snapshot-manager.md)的详细信息。
* 了解有关[使用 StorSimple Snapshot Manager 管理 StorSimple 解决方案](storsimple-snapshot-manager-admin.md)的详细信息。

