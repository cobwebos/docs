---
title: "跟踪 Azure 虚拟机上的更改 | Microsoft 文档"
description: "使用更改跟踪来跟踪虚拟机上的文件和注册表中的更改。"
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
ms.openlocfilehash: 5c6e8390ec8533fc7ab281c212e47a6982b30f1a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="track-changes-in-your-azure-virtual-machines"></a>跟踪 Azure 虚拟机上的更改

通过启用“更改跟踪”，可跟踪虚拟机上对文件和 Windows 注册表项的更改 标识配置更改可帮助你查明操作问题。

可以直接从 Azure 虚拟机启用“更改跟踪”。

如果没有 Azure 虚拟机，可以根据 [Windows 快速入门](../virtual-machines/windows/quick-create-portal.md)或 [Linux 快速入门](../virtual-machines/linux/quick-create-portal.md)中的说明创建一个。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户
登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="enable-change-tracking-for-an-azure-virtual-machine"></a>为 Azure 虚拟机启用“更改跟踪”

1. 在 Azure 门户的左窗格中，选择“虚拟机”。
2. 从列表中选择一个虚拟机。
3. 在虚拟机窗口中，在“操作”下，选择“更改跟踪”。 

   ![更改跟踪板载 VM](./media/automation-vm-change-tracking/change-onboard-vm-blade.png)  
    “启用更新管理”窗口随即打开。

    执行验证，确定是否为该虚拟机启用了更改跟踪。 如果未启用更改跟踪，则会显示一个横幅，让你选择启用该解决方案。

   ![更改跟踪板载配置横幅](./media/automation-vm-change-tracking/change-onboard-banner.png)

4. 若要启用该解决方案，请选择横幅。 如果没有以下项，则会自动添加：

   * [Log Analytics](../log-analytics/log-analytics-overview.md) 工作区
   * [自动化](../automation/automation-offering-get-started.md)帐户

5. 选择一个 Log Analytics 工作区来存储更改跟踪中的数据记录，并选择一个自动化帐户来跟踪更改，然后单击“启用”。  
    状态栏会通知你正在启用解决方案。 此过程最多可能需要 15 分钟。

## <a name="configure-change-tracking"></a>配置更改跟踪

启用更改跟踪后，将出现“更改跟踪”窗口。 

若要选择要跟踪的文件和注册表项，请选择“编辑设置”。

   ![更改跟踪编辑设置](./media/automation-vm-change-tracking/change-edit-settings.png)

   “工作区配置”窗口随即打开。 

如接下来的三部分中所述，在“工作区配置”窗口中，添加要跟踪的 Windows 注册表项、Windows 文件或 Linux 文件。

### <a name="add-a-windows-registry-key"></a>添加 Windows 注册表项

1. 在“Windows 注册表”选项卡上，选择“添加”。  
    “添加 Windows 注册表以跟踪更改”窗口随即打开。

   ![更改跟踪添加注册表](./media/automation-vm-change-tracking/change-add-registry.png)

2. 在“已启用”下，选择“True”。
3. 在“项名称”框中，输入一个友好名称。
4. （可选）在“组”框中，输入一个组名称。
5. 在“Windows 注册表项”框中，输入要跟踪的注册表项的名称。
6. 选择“保存”。

### <a name="add-a-windows-file"></a>添加 Windows 文件

1. 在“Windows 文件”选项卡上，选择“添加”。  
    “添加 Windows 文件以跟踪更改”窗口随即打开。

   ![更改跟踪添加 Windows 文件](./media/automation-vm-change-tracking/change-add-win-file.png)

2. 在“已启用”下，选择“True”。
3. 在“项名称”框中，输入一个友好名称。
4. （可选）在“组”框中，输入一个组名称。
5. 在“输入路径”框中，输入要跟踪的文件的完整路径和文件名。
6. 选择“保存”。

### <a name="add-a-linux-file"></a>添加 Linux 文件

1. 在“Linux 文件”选项卡上，选择“添加”。  
    “添加 Linux 文件以跟踪更改”窗口随即打开。

   ![更改跟踪添加 Linux 文件](./media/automation-vm-change-tracking/change-add-linux-file.png)

2. 在“已启用”下，选择“True”。
3. 在“项名称”框中，输入一个友好名称。
4. （可选）在“组”框中，输入一个组名称。
5. 在“输入路径”框中，输入要跟踪的文件的完整路径和文件名。
6. 在“路径类型”框中，选择“文件”或“目录”。
7. 在“递归”下，若要跟踪指定路径以及其下所有文件和路径的更改，请选择“打开”。 若要仅跟踪指定路径或文件，选择“关闭”。
8. 在“使用 Sudo”下，若要跟踪需要 `sudo` 命令进行访问的文件，请选择“打开”。 否则，选择“关闭。
9. 选择“保存”。

## <a name="view-changes"></a>查看更改

在“更改跟踪”窗口中，可以在各种类别中查看虚拟机随时间的更改。

   ![更改跟踪添加查看更改](./media/automation-vm-change-tracking/change-view-changes.png)

可以选择要查看的更改的类别和时间范围。 在窗口顶部，可以查看在一段时间内的更改的图形表示形式。 在窗口底部，可以查看最近的更改的列表。

## <a name="view-change-tracking-log-data"></a>查看更改 - 跟踪日志数据

更改跟踪生成发送到 Log Analytics 的日志数据。 若要通过运行查询来搜索日志，请选择“更改跟踪”窗口顶部的“Log Analytics”。

   ![更改跟踪 Log Analytics](./media/automation-vm-change-tracking/change-log-analytics.png)

若要了解有关在 Log Analytics 中运行和搜索日志文件的详细信息，请参阅 [Log Analytics](../log-analytics/log-analytics-overview.md)。

## <a name="next-steps"></a>后续步骤

* 若要了解有关更改跟踪的详细信息，请参阅[使用更改跟踪解决方案跟踪环境中的软件更改](../log-analytics/log-analytics-change-tracking.md)。
* 若要了解有关管理虚拟机更新的详细信息，请参阅 [OMS 中的更新管理解决方案](../operations-management-suite/oms-solution-update-management.md)。
