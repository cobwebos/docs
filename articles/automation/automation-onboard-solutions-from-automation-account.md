---
title: 了解如何在 Azure 自动化中载入更新管理、更改跟踪和清单解决方案
description: 了解如何载入包含属于 Azure 自动化的一部分的更新管理、更改跟踪和清单解决方案的 Azure 虚拟机
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 10/16/2018
ms.topic: conceptual
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: d274642c8e35374dee2d584e6285165a15cd3128
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2019
ms.locfileid: "55747027"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions"></a>载入更新管理、更改跟踪和清单解决方案

Azure 自动化提供了解决方案来管理操作系统安全性更新、跟踪更改以及列出计算机上所安装项的清单。 载入计算机的方式有多种，可以[通过虚拟机](automation-onboard-solutions-from-vm.md)、[通过浏览多个计算机](automation-onboard-solutions-from-browse.md)、通过自动化帐户或通过 [runbook](automation-onboard-solutions.md) 载入解决方案。 本文介绍了如何从自动化帐户载入这些解决方案。

## <a name="sign-in-to-azure"></a>登录 Azure

登录 Azure (https://portal.azure.com)

## <a name="enable-solutions"></a>启用解决方案

导航到你的自动化帐户，在“配置管理”下选择“清单”或“更改跟踪”。

选择 Log Analytics 工作区和自动化帐户，然后单击“启用”以启用此解决方案。 启用此解决方案最长需要 15 分钟的时间。

![载入清单解决方案](media/automation-onboard-solutions-from-automation-account/onboardsolutions.png)

在启用解决方案时，只有某些区域支持链接 Log Analytics 工作区和自动化帐户。

下表显示了受支持的映射：

|**Log Analytics 工作区区域**|**Azure 自动化区域**|
|---|---|
|AustraliaSoutheast|AustraliaSoutheast|
|CanadaCentral|CanadaCentral|
|CentralIndia|CentralIndia|
|EastUS|EastUS2|
|JapanEast|JapanEast|
|SoutheastAsia|SoutheastAsia|
|WestCentralUS|WestCentralUS|
|西欧|西欧|
|UKSouth|UKSouth|
|USGovVirginia|USGovVirginia|
|EastUS2EUAP|CentralUSEUAP|
|美国西部 2|美国西部 2|

更改跟踪和清单解决方案提供针对虚拟机的[跟踪更改](automation-vm-change-tracking.md)和[清单](automation-vm-inventory.md)功能。 在此步骤中，在虚拟机上启用解决方案。

更改跟踪和清单解决方案载入通知完成后，请单击“配置管理”下的“更新管理”。

使用更新管理解决方案可以管理 Azure Windows VM 的更新和修补程序。 可评估可用更新的状态、计划所需更新的安装以及查看部署结果，验证更新是否已成功应用到 VM。 此操作已为你的 VM 启用了此解决方案。

在“更新管理”下选择“更新管理”。 选定的日志分析工作区即为上一步骤中使用的工作区。 单击“启用”以载入更新管理解决方案。 启用此解决方案最长需要 15 分钟的时间。

![载入更新解决方案](media/automation-onboard-solutions-from-automation-account/onboardsolutions2.png)

## <a name="scope-configuration"></a>作用域配置

每个解决方案在工作区中使用作用域配置来确定获取解决方案的计算机。 作用域配置是包含一个或多个已保存搜索的组，该组用来将解决方案的作用域限制为特定计算机。 若要访问作用域配置，在你的自动化帐户中，在“相关资源”下选择“工作区”。 然后，在工作区中，在“工作区数据源”下选择“作用域配置”。

如果所选工作区中没有“更新管理”或“更改跟踪”解决方案，将创建以下范围配置：

* **MicrosoftDefaultScopeConfig-ChangeTracking**

* **MicrosoftDefaultScopeConfig-Updates**

如果所选工作区已有解决方案，则不会重新部署解决方案，也不会添加作用域配置。

## <a name="saved-searches"></a>保存的搜索

向“更新管理”或“更改跟踪”和“清单”解决方案添加计算机时，会将其添加到工作区中的两个已保存搜索之一。 这些已保存搜索是包含这些解决方案所针对的目标计算机的查询。

导航到你的自动化帐户并在“常规”下选择“保存的搜索”。 可以在下表中看到这些解决方案使用的两个已保存搜索：

|Name     |类别  |别名  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | 更新        | Updates__MicrosoftDefaultComputerGroup         |

选择保存的任一搜索来查看用来填充组的查询。 下图显示了查询及其结果：

![保存的搜索](media/automation-onboard-solutions-from-automation-account/savedsearch.png)

## <a name="onboard-azure-vms"></a>载入 Azure VM

从你的自动化帐户中，在“配置管理”下选择“清单”或“更改跟踪”，或者在“更新管理”下选择“更新管理”。

单击“+ 添加 Azure VM”，从列表中选择一个或多个 VM。 无法启用的虚拟机为灰显，无法选择。 在“启用更新管理”页上，单击“启用”。 此操作会将选定 VM 添加到计算机组“为此解决方案保存的搜索结果”。

![启用 Azure VM](media/automation-onboard-solutions-from-automation-account/enable-azure-vms.png)

## <a name="onboard-a-non-azure-machine"></a>载入非 Azure 计算机

需要手动添加 Azure 中没有的计算机。 从你的自动化帐户中，在“配置管理”下选择“清单”或“更改跟踪”，或者在“更新管理”下选择“更新管理”。

单击“添加非 Azure 计算机”。 此操作打开新浏览器窗口，其中[介绍了如何在计算机上安装并配置 Microsoft Monitoring Agent](../azure-monitor/platform/log-analytics-agent.md)，让计算机能够开始向解决方案进行报告。 如果你载入当前由 System Center Operations Manager 管理的计算机，则不需要新代理，工作区信息将输入到现有代理中。

## <a name="onboard-machines-in-the-workspace"></a>在工作区中载入计算机

必须将手动安装的计算机或已向工作区进行报告的计算机添加到 Azure 自动化中，才能启用解决方案。 从你的自动化帐户中，在“配置管理”下选择“清单”或“更改跟踪”，或者在“更新管理”下选择“更新管理”。

选择“管理计算机”。 此操作将打开“管理计算机”页面。 此页面允许你在所选一组计算机上、所有可用的计算机上启用解决方案，或者为所有当前计算机启用解决方案并为所有将来的计算机启用解决方案。 如果之前选择了“在所有可用和将来的计算机上启用”选项，则“管理计算机”按钮可能灰显。

![保存的搜索](media/automation-onboard-solutions-from-automation-account/managemachines.png)

### <a name="all-available-machines"></a>所有可用计算机

若要为所有可用的计算机启用解决方案，请选择“在所有可用的计算机上启用”。 此操作禁用单独添加计算机的控件。 此任务会将向工作区进行报告的所有计算机的名称添加到计算机组保存的搜索查询。 选中此项后，此操作将禁用“管理计算机”按钮。

### <a name="all-available-and-future-machines"></a>所有可用的和将来的计算机

若要为所有可用的计算机和将来的计算机启用解决方案，请选择“在所有可用的和将来的计算机上启用”。 此选项会从工作区中删除已保存的搜索和作用域配置。 此操作将为向工作区进行报告的所有 Azure 和非 Azure 计算机打开解决方案。 选中此项后，此操作将永久禁用“管理计算机”按钮，因为没有剩余的范围配置。

### <a name="selected-machines"></a>所选计算机

若要为一台或多台计算机启用解决方案，请选择“在所选计算机上启用”并单击要添加到解决方案的每台计算机旁边的“添加”。 此任务会将所选计算机名称添加到计算机组为此解决方案保存的搜索查询。

## <a name="unlink-workspace"></a>取消链接工作区

以下解决方案依赖于 Log Analytics 工作区：

* [更新管理](automation-update-management.md)
* [更改跟踪](automation-change-tracking.md)
* [在非工作时间启动/停止 VM](automation-solution-vm-management.md)

如果决定不再想要会自动化帐户与 Log Analytics 集成，可以直接从 Azure 门户取消链接帐户。  在继续之前，首先需要删除前面所述的解决方案，否则此过程将无法继续。 查看已导入的特定解决方案的主题，了解删除该解决方案所需的步骤。

删除这些解决方案后，可以完成以下步骤取消链接自动化帐户。

> [!NOTE]
> 某些解决方案（包括早期版本的 Azure SQL 监视解决方案）可能已创建自动化资产并可能还需要在取消链接工作区之前删除。

1. 从 Azure 门户中打开自动化帐户，并在“自动化帐户”页左侧标有“相关资源”的部分下，选择“链接工作区”。

2. 在“取消链接工作区”页上，单击“取消链接工作区”。

   ![“取消链接工作区”页](media/automation-onboard-solutions-from-automation-account/automation-unlink-workspace-blade.png).

   系统会提示用户确认是否要继续。

3. 当 Azure 自动化尝试从 Log Analytics 工作区中取消链接该帐户时，可以在菜单中的“通知”下跟踪进度。

如果使用了“更新管理”解决方案，可能会选择要删除在删除该解决方案后不再需要的以下项。

* 更新计划 - 每个计划都将具有与所创建的更新部署匹配的名称

* 为解决方案创建的混合辅助角色组 - 每个混合辅助角色组的命名都将类似于 machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8。

如果使用了“在非工作时间启动和停止 VM”解决方案，可能会选择要删除在删除该解决方案后不再需要的以下项。

* 启动和停止 VM Runbook 计划
* 启动和停止 VM Runbook
* 变量

## <a name="next-steps"></a>后续步骤

继续学习有关解决方案的教程来了解如何使用它们。

* [教程 - 管理 VM 的更新](automation-tutorial-update-management.md)

* [教程 - 识别 VM 上的软件](automation-tutorial-installed-software.md)

* [教程 - 对 VM 上的更改进行故障排除](automation-tutorial-troubleshoot-changes.md)