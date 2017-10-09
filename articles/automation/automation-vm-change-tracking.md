---
title: "跟踪 Azure 虚拟机上的更改 | Microsoft 文档"
description: "使用更改跟踪来跟踪虚拟机上的文件和注册表中的更改"
services: automation
documentationcenter: automation
author: eslesar
manager: carmonm
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: automation
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: eslesar
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: ba92390b684573818ec9d69fd8c7be6d064316c7
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

# <a name="track-changes-in-your-azure-virtual-machines"></a>跟踪 Azure 虚拟机上的更改

通过启用“更改跟踪”，可跟踪虚拟机上对文件和 Windows 注册表项的更改 。 标识配置更改可帮助你查明操作问题。

可以直接从 Azure 虚拟机启用“更改跟踪”。

如果没有 Azure 虚拟机，可以使用 [Windows 快速入门](../virtual-machines/windows/quick-create-portal.md)或 [Linux 快速入门](../virtual-machines/linux/quick-create-portal.md)创建一个

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="enable-change-tracking-for-an-azure-virtual-machine"></a>为 Azure 虚拟机启用“更改跟踪”

1. 在屏幕的左侧，选择“虚拟机”。
1. 从列表中选择虚拟机。
1. 在虚拟机屏幕上的“操作”部分中，单击“更改跟踪”。 “启用更新管理”屏幕随即打开。

   ![更改跟踪板载虚拟机](./media/automation-vm-change-tracking/change-onboard-vm-blade.png)

执行验证，确定是否为该虚拟机启用了更改跟踪。 如果未启用更改跟踪，则会显示一个横幅，让你选择启用该解决方案。

   ![更改跟踪板载配置横幅](./media/automation-vm-change-tracking/change-onboard-banner.png)

单击横幅以启用该解决方案。 如果没有以下项，则会自动添加：

* [Log Analytics](../log-analytics/log-analytics-overview.md) 工作区
* [自动化](../automation/automation-offering-get-started.md)帐户

选择 Log Analytics 工作区用以存储更改跟踪中的数据记录，而自动化帐户用以跟踪更改，然后单击“启用”。

状态栏会通知你正在启用解决方案。 此过程最多可能需要 15 分钟。

## <a name="configure-change-tracking"></a>配置更改跟踪

启用更改跟踪后，将出现“更改跟踪”屏幕。 单击“编辑设置”，选择要跟踪的文件和注册表项。

![更改跟踪编辑设置](./media/automation-vm-change-tracking/change-edit-settings.png)

“工作区配置”屏幕随即打开。 在相应选项卡上单击“添加”，添加要跟踪的 Windows 注册表项、Windows 文件或 Linux 文件。

## <a name="add-a-windows-registry-key"></a>添加 Windows 注册表项

1. 在“工作区配置”屏幕的“Windows 注册表”选项卡上，单击“添加”。 “添加 Windows 注册表以跟踪更改”屏幕随即打开。

   ![更改跟踪添加注册表](./media/automation-vm-change-tracking/change-add-registry.png)

1. 在“已启用”下，选择“True”。
1. 在“项名称”字段中添加友好名称。
1. 在“组”字段中，输入组名称（可选）。
1. 在“Windows 注册表项”字段中，添加要跟踪的注册表项的名称。
1. 单击“保存” 。

## <a name="add-a-windows-file"></a>添加 Windows 文件

1. 在“工作区配置”屏幕的“Windows 文件”选项卡上，单击“添加”，“添加 Windows 文件以跟踪更改”屏幕随即打开。

   ![更改跟踪添加 Windows 文件](./media/automation-vm-change-tracking/change-add-win-file.png)

1. 在“已启用”下，选择“True”。
1. 在“项名称”字段中添加友好名称。
1. 在“组”字段中，输入组名称（可选）。
1. 在“输入路径”字段中，添加要跟踪的文件的完整路径和文件名。
1. 单击“保存”。

## <a name="add-a-linux-file"></a>添加 Linux 文件

1. 在“工作区配置”屏幕的“Linux 文件”选项卡上，单击“添加”，“添加 Linux 文件以跟踪更改”屏幕随即打开。

   ![更改跟踪添加 Linux 文件](./media/automation-vm-change-tracking/change-add-linux-file.png)

1. 在“已启用”下，选择“True”。
1. 在“项名称”字段中添加友好名称。
1. 在“组”字段中，输入组名称（可选）。
1. 在“输入路径”字段中，添加要跟踪的文件的完整路径和文件名。
1. 在“路径类型”字段中，选择“文件”或“目录”。
1. 在“递归”下，选择“打开”来跟踪指定路径以及其下所有文件和路径的更改。 若要仅跟踪指定路径或文件，选择“关闭”。
1. 在“Sudo”下，选择“打开”来跟踪需要 `sudo` 命令进行访问的文件。 否则，选择“关闭。
1. 单击“保存”。

## <a name="view-changes"></a>查看更改

在“更改跟踪”屏幕上，你可以看到虚拟机各种类别随时间变化的更改。

   ![更改跟踪添加查看更改](./media/automation-vm-change-tracking/change-view-changes.png)

你可以选择要查看的更改的类别和时间范围。 在屏幕顶部，你可以查看随时间变化各个更改的图形视图。
在屏幕底部会看到最近更改的列表。

## <a name="view-change-tracking-log-data"></a>查看更改跟踪日志数据

更改跟踪生成发送到 Log Analytics 的日志数据。 若要通过运行查询搜索日志，请单击“更改跟踪”屏幕顶部的“Log Analytics”。

   ![更改跟踪 Log Analytics](./media/automation-vm-change-tracking/change-log-analytics.png)

要了解有关在 Log Analytics 中运行搜索日志文件的详细信息，请参阅 [Log Analytics](../log-analytics/log-analytics-overview.md)。

## <a name="next-steps"></a>后续步骤

* 要了解有关更改跟踪的详细信息，请参阅[更改跟踪](../log-analytics/log-analytics-change-tracking.md)
* 要了解有关管理虚拟机更新的详细信息，请参阅[更新管理](../operations-management-suite/oms-solution-update-management.md)。

