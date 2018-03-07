---
title: "了解如何从 Azure 虚拟机载入更新管理、更改跟踪和清单解决方案。"
description: "了解如何载入包含属于 Azure 自动化的一部分的更新管理、更改跟踪和清单解决方案的 Azure 虚拟机"
services: automation
keywords: 
author: georgewallace
ms.author: gwallace
ms.date: 02/28/2018
ms.topic: article
ms.service: automation
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: a850189406b394e7935763206f9e3a191b415170
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions-from-an-azure-virtual-machine"></a>从 Azure 虚拟机载入更新管理、更改跟踪和清单解决方案

Azure 自动化提供了解决方案来管理操作系统安全性更新、跟踪更改以及列出计算机上所安装项的清单。 可以通过多种方式来载入计算机，可以通过虚拟机、[自动化帐户](automation-onboard-solutions-from-automation-account.md)或 [Runbook](automation-onboard-solutions.md) 载入解决方案。 本文介绍了如何从 Azure 虚拟机载入这些解决方案。

## <a name="log-in-to-azure"></a>登录 Azure

通过 https://portal.azure.com 登录 Azure

## <a name="enable-the-solutions"></a>启用解决方案

导航到现有的虚拟机，然后在“操作”下选择“更新管理”、“清单”或“更改跟踪”。

选择 Log analytics 工作区和自动化帐户，然后单击“启用”以启用此解决方案。 启用此解决方案最长需要 15 分钟的时间。

![载入更新解决方案](media/automation-onboard-solutions-from-vm/onboard-solution.png)

导航到其他解决方案并单击“启用”，Log Analytics 和自动化帐户下拉框处于禁用状态，因为它们与之前启用的解决方案使用相同的工作区和自动化帐户。

![载入更新解决方案](media/automation-onboard-solutions-from-vm/onboard-solutions2.png)

> [!NOTE]
> “更改跟踪”和“清单”使用相同的解决方案，启用其中一个后，另一个也会启用。

## <a name="scope-configuration"></a>作用域配置

每个解决方案在工作区中使用作用域配置来确定获取解决方案的计算机。 作用域配置是包含一个或多个已保存搜索的组，该组用来将解决方案的作用域限制为特定计算机。 若要访问作用域配置，在你的自动化帐户中，在“相关资源”下选择“工作区”，然后在工作区中，在“工作区数据源”下选择“作用域配置”。

默认情况下创建的两个作用域配置是 **MicrosoftDefaultScopeConfig-ChangeTracking** 和 **MicrosoftDefaultScopeConfig-Updates**。

单击任何配置上的省略号 (...) 并选择“编辑”。 在“编辑作用域配置”页面上，选择“选择计算机组”以打开“计算机组”页面。 此页面显示用来创建作用域配置的已保存搜索。

## <a name="saved-searches"></a>保存的搜索

向“更新管理”或“更改跟踪”和“清单”解决方案添加计算机时，会将其添加到工作区中的两个已保存搜索之一。 这些已保存搜索是包含这些解决方案所针对的目标计算机的查询。

导航到你的工作区并在“常规”下选择“保存的搜索”。 可以在下表中看到这些解决方案使用的两个已保存搜索：

|名称     |类别  |别名  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | 更新        | Updates__MicrosoftDefaultComputerGroup         |

选择保存的任一搜索来查看用来填充组的查询。 下图显示了查询及其结果。

![保存的搜索](media/automation-onboard-solutions-from-vm/logsearch.png)

## <a name="next-steps"></a>后续步骤

继续学习有关解决方案的教程来了解如何使用它们。

* [教程 - 管理 VM 的更新](automation-tutorial-update-management.md)

* [教程 - 识别 VM 上的软件](automation-tutorial-installed-software.md)

* [教程 - 对 VM 上的更改进行故障排除](automation-tutorial-troubleshoot-changes.md)
