---
title: 从 Azure VM 载入“更新管理”、“更改跟踪”和“清单”解决方案
description: 了解如何载入包含属于 Azure 自动化的“更新管理”、“更改跟踪”和“清单”解决方案的 Azure 虚拟机。
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/06/2018
ms.topic: conceptual
ms.service: automation
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: f270b2ccea51e83bc6475051b8667bf73d7fd717
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36221506"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions-from-an-azure-virtual-machine"></a>从 Azure 虚拟机载入“更新管理”、“更改跟踪”和“清单”解决方案

Azure 自动化提供了解决方案来帮助管理操作系统安全更新、跟踪更改以及列出计算机上所安装项的清单。 可以通过多种方式来载入计算机。 可以通过虚拟机、[通过自动化帐户](automation-onboard-solutions-from-automation-account.md)、[通过浏览多个计算机](automation-onboard-solutions-from-browse.md)或通过使用 [Runbook](automation-onboard-solutions.md) 载入解决方案。 本文介绍了如何从 Azure 虚拟机载入这些解决方案。

## <a name="sign-in-to-azure"></a>登录 Azure

通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="enable-the-solutions"></a>启用解决方案

转到现有虚拟机。 在“操作”下，选择“更新管理”、“清单”或“更改跟踪”。

若要仅为 VM 启用解决方案，请确保已选择“为此 VM 启用”。 若要将多台计算机加入到解决方案中，请选择“为此订阅中的 VM 启用”，然后选择“单击以选择要启用的计算机”。 若要了解如何一次加入多台计算机，请参阅[载入“更新管理”、“更改跟踪”和“清单”解决方案](automation-onboard-solutions-from-automation-account.md)。

选择 Azure Log Analytics 工作区和自动化帐户，然后选择“启用”以启用此解决方案。 启用此解决方案最长需要 15 分钟的时间。

![载入“更新管理”解决方案](media/automation-onboard-solutions-from-vm/onboard-solution.png)

请转到其他解决方案，然后选择“启用”。 “Log Analytics 和自动化帐户”下拉列表处于禁用状态，因为这些解决方案与之前启用的解决方案使用相同的工作区和自动化帐户。

> [!NOTE]
> **更改跟踪**和**清单**使用同一解决方案。 启用其中一个解决方案时，另一个解决方案也会被启用。

## <a name="scope-configuration"></a>作用域配置

每个解决方案在工作区中使用作用域配置来确定获取解决方案的计算机。 作用域配置是包含一个或多个已保存搜索的组，这些搜索用来将解决方案的作用域限制为特定计算机。 若要访问作用域配置，请在自动化帐户中的“相关资源”下选择“工作区”。 在工作区中的“工作区数据源”下选择“作用域配置”。

如果所选工作区中还没有“更新管理”或“更改跟踪”解决方案，则将创建以下作用域配置：

* **MicrosoftDefaultScopeConfig-ChangeTracking**

* **MicrosoftDefaultScopeConfig-Updates**

如果所选工作区已有解决方案，则不会重新部署解决方案，也不会添加作用域配置。

选择任何配置上的省略号 (...) 并选择“编辑”。 在“编辑作用域配置”窗格中，选择“选择计算机组”。 “计算机组”窗格将显示用来创建作用域配置的已保存搜索。

## <a name="saved-searches"></a>保存的搜索

向“更新管理”、“更改跟踪”和“清单”解决方案添加计算机时，计算机会添加到工作区中的两个已保存搜索之一。 这些已保存搜索是包含这些解决方案所针对的目标计算机的查询。

转到你的工作区。 在“常规”下，选择“保存的搜索”。 下表中将显示这些解决方案使用的两个已保存搜索：

|名称     |类别  |别名  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | 更新        | Updates__MicrosoftDefaultComputerGroup         |

选择任一已保存的搜索以查看用来填充组的查询。 下图显示了查询及其结果：

![保存的搜索](media/automation-onboard-solutions-from-vm/logsearch.png)

## <a name="next-steps"></a>后续步骤

继续学习有关解决方案的教程来了解如何使用它们：

* [教程 - 管理 VM 的更新](automation-tutorial-update-management.md)
* [教程 - 识别 VM 上的软件](automation-tutorial-installed-software.md)
* [教程 - 对 VM 上的更改进行故障排除](automation-tutorial-troubleshoot-changes.md)
