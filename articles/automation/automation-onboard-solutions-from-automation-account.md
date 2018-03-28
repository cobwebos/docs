---
title: 了解如何在 Azure 自动化中载入更新管理、更改跟踪和清单解决方案
description: 了解如何载入包含属于 Azure 自动化的一部分的更新管理、更改跟踪和清单解决方案的 Azure 虚拟机
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.devlang: na
ms.tgt_pltfrm: na
ms.custom: mvc
ms.openlocfilehash: 65bf0d98da8111e986d5dbdfd58f1692d40ee286
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2018
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions"></a>载入更新管理、更改跟踪和清单解决方案

Azure 自动化提供了解决方案来管理操作系统安全性更新、跟踪更改以及列出计算机上所安装项的清单。 可以通过多种方式来载入计算机，可以通过[虚拟机](automation-onboard-solutions-from-vm.md)、自动化帐户或 [Runbook](automation-onboard-solutions.md) 载入解决方案。 本文介绍了如何从自动化帐户载入这些解决方案。

## <a name="log-in-to-azure"></a>登录 Azure

在 https://portal.azure.com 中登录 Azure

## <a name="enable-solutions"></a>启用解决方案

导航到你的自动化帐户，在“配置管理”下选择“清单”或“更改跟踪”。

选择 Log Analytics 工作区和自动化帐户，然后单击“启用”以启用此解决方案。 启用此解决方案最长需要 15 分钟的时间。

![载入清单解决方案](media/automation-onboard-solutions-from-automation-account/onboardsolutions.png)

更改跟踪和清单解决方案提供针对虚拟机的[跟踪更改](automation-vm-change-tracking.md)和[清单](automation-vm-inventory.md)功能。 在此步骤中，在虚拟机上启用解决方案。

更改跟踪和清单解决方案载入通知完成后，请单击“配置管理”下的“更新管理”。

使用更新管理解决方案可以管理 Azure Windows VM 的更新和修补程序。 可评估可用更新的状态、计划所需更新的安装以及查看部署结果，验证更新是否已成功应用到 VM。 此操作已为你的 VM 启用了此解决方案。

在“更新管理”下选择“更新管理”。 选定的日志分析工作区即为上一步骤中使用的工作区。 单击“启用”以载入更新管理解决方案。 启用此解决方案最长需要 15 分钟的时间。

![载入更新解决方案](media/automation-onboard-solutions-from-automation-account/onboardsolutions2.png)

## <a name="scope-configuration"></a>作用域配置

每个解决方案在工作区中使用作用域配置来确定获取解决方案的计算机。 作用域配置是包含一个或多个已保存搜索的组，该组用来将解决方案的作用域限制为特定计算机。 若要访问作用域配置，在你的自动化帐户中，在“相关资源”下选择“工作区”。 然后，在工作区中，在“工作区数据源”下选择“作用域配置”。

默认情况下创建的两个作用域配置是 **MicrosoftDefaultScopeConfig-ChangeTracking** 和 **MicrosoftDefaultScopeConfig-Updates**。

## <a name="saved-searches"></a>保存的搜索

向“更新管理”或“更改跟踪”和“清单”解决方案添加计算机时，会将其添加到工作区中的两个已保存搜索之一。 这些已保存搜索是包含这些解决方案所针对的目标计算机的查询。

导航到你的自动化帐户并在“常规”下选择“保存的搜索”。 可以在下表中看到这些解决方案使用的两个已保存搜索：

|名称     |类别  |别名  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | 更新        | Updates__MicrosoftDefaultComputerGroup         |

选择保存的任一搜索来查看用来填充组的查询。 下图显示了查询及其结果：

![保存的搜索](media/automation-onboard-solutions-from-automation-account/savedsearch.png)

## <a name="onboard-an-azure-machine"></a>载入 Azure 计算机

从你的自动化帐户中，在“配置管理”下选择“清单”或“更改跟踪”，或者在“更新管理”下选择“更新管理”。

单击“+ 添加 Azure VM”，从列表中选择一个 VM。 在“更新管理”页面上，单击“启用”。 这会将当前 VM 添加到计算机组为此解决方案保存的搜索。

## <a name="onboard-a-non-azure-machine"></a>载入非 Azure 计算机

从你的自动化帐户中，在“配置管理”下选择“清单”或“更改跟踪”，或者在“更新管理”下选择“更新管理”。

单击“添加非 Azure 计算机”。 这将打开一个新的浏览器窗口，其中说明了如何在计算机上安装 Microsoft Monitoring Agent 以使计算机可以开始向解决方案进行报告。 如果你载入当前由 System Center Operations Manager 管理的计算机，则不需要新代理，工作区信息将输入到现有代理中。

## <a name="onboard-machines-in-the-workspace"></a>在工作区中载入计算机

从你的自动化帐户中，在“配置管理”下选择“清单”或“更改跟踪”，或者在“更新管理”下选择“更新管理”。

选择“管理计算机”。 这将打开“管理计算机”页面。 此页面允许你在所选一组计算机上、所有可用的计算机上启用解决方案，或者为所有当前计算机启用解决方案并为所有将来的计算机启用解决方案。

![保存的搜索](media/automation-onboard-solutions-from-automation-account/managemachines.png)

### <a name="selected-machines"></a>所选计算机

若要为一台或多台计算机启用解决方案，请选择“在所选计算机上启用”并单击要添加到解决方案的每台计算机旁边的“添加”。 此任务会将所选计算机名称添加到计算机组为此解决方案保存的搜索查询。

### <a name="all-available-machines"></a>所有可用计算机

若要为所有可用的计算机启用解决方案，请选择“在所有可用的计算机上启用”。 这会禁用分别添加计算机的控件。 此任务会将向工作区进行报告的所有计算机的名称添加到计算机组保存的搜索查询。

### <a name="all-available-and-future-machines"></a>所有可用的和将来的计算机

若要为所有可用的计算机和所有将来的计算机启用解决方案，请选择“在所有可用的和将来的计算机上启用”。 此选项会从工作区中删除已保存的搜索和作用域配置。 这将为向工作区进行报告的所有 Azure 和非 Azure 计算机打开解决方案。

## <a name="next-steps"></a>后续步骤

继续学习有关解决方案的教程来了解如何使用它们。

* [教程 - 管理 VM 的更新](automation-tutorial-update-management.md)

* [教程 - 识别 VM 上的软件](automation-tutorial-installed-software.md)

* [教程 - 对 VM 上的更改进行故障排除](automation-tutorial-troubleshoot-changes.md)