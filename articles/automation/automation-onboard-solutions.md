---
title: 将更新和更改跟踪解决方案加入 Azure 自动化
description: 了解如何将更新和更改跟踪解决方案加入 Azure 自动化。
services: automation
ms.service: automation
author: eamonoreilly
ms.author: eamono
manager: carmonm
ms.topic: tutorial
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 5d03ec7c5e0aaa68c1db34fedbd428f264e49e43
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "34830514"
---
# <a name="onboard-update-and-change-tracking-solutions-to-azure-automation"></a>将更新和更改跟踪解决方案加入 Azure 自动化

本教程介绍如何自动将 VM 的更新、更改跟踪和清单解决方案加入 Azure 自动化：

> [!div class="checklist"]
> * 载入 Azure VM
> * 启用解决方案
> * 安装和更新模块
> * 导入载入 Runbook
> * 启动 Runbook

## <a name="prerequisites"></a>先决条件

完成本教程需要以下各项：

* Azure 订阅。 如果还没有帐户，可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 用于管理计算机的[自动化帐户](automation-offering-get-started.md)。
* 要载入的[虚拟机](../virtual-machines/windows/quick-create-portal.md)。

## <a name="onboard-an-azure-vm"></a>载入 Azure VM

可以通过多种方式来载入计算机，可以[通过虚拟机](automation-onboard-solutions-from-vm.md)、[通过浏览多个计算机](automation-onboard-solutions-from-browse.md)、[通过自动化帐户](automation-onboard-solutions-from-automation-account.md)或通过 Runbook 载入解决方案。 本教程详细介绍了如何通过 Runbook 启用更新管理。 若要大规模载入 Azure 虚拟机，须使用更改跟踪或更新管理解决方案载入现有 VM。 在此步骤中，使用更新管理和更改跟踪载入虚拟机。

### <a name="enable-change-tracking-and-inventory"></a>启用更改跟踪和清单

更改跟踪和清单解决方案提供针对虚拟机的[跟踪更改](automation-vm-change-tracking.md)和[清单](automation-vm-inventory.md)功能。 在此步骤中，在虚拟机上启用解决方案。

1. 从左侧菜单中，选择“自动化帐户”，然后在列表中选择自动化帐户。
1. 选择“配置管理”下的“清单”。
1. 选择现有的或创建新的 Log Analytics 工作区。 单击“启用”按钮。

![载入更新解决方案](media/automation-onboard-solutions/inventory-onboard.png)

更改跟踪和清单解决方案载入通知完成后，请单击“配置管理”下的“更新管理”。

### <a name="enable-update-management"></a>启用更新管理

使用更新管理解决方案可以管理 Azure Windows VM 的更新和修补程序。 可评估可用更新的状态、计划所需更新的安装以及查看部署结果，验证更新是否已成功应用到 VM。 在此步骤中，为 VM 启用解决方案。

1. 从自动化帐户，选择“更新管理”下的“更新管理”。
1. 选定的日志分析工作区即为上一步骤中使用的工作区。 单击“启用”以载入更新管理解决方案。

![载入更新解决方案](media/automation-onboard-solutions/update-onboard.png)

更新管理解决方案安装期间会显示一个蓝色横幅。 启用解决方案后，选择“配置管理”下的“更改跟踪”以转到下一步。

### <a name="select-azure-vm-to-be-managed"></a>选择要管理的 Azure VM

现已启用解决方案，可添加 Azure VM 以载入到这些解决方案中。

1. 从自动化帐户中，在“更改跟踪”页上，选择“+ 添加 Azure VM”以添加虚拟机。

1. 从列表中选择 VM，然后选择“启用”。 此操作为虚拟机启用更改跟踪和清单解决方案。

   ![为虚拟机启用更新解决方案](media/automation-onboard-solutions/enable-change-tracking.png)

1. VM 载入通知完成后，从自动化帐户，选择“更新管理”下的“更新管理”。

1. 选择“+ 添加 Azure VM”以添加虚拟机。

1. 从列表中选择 VM，然后选择“启用”。 此操作为虚拟机启用更新管理解决方案。

   ![为虚拟机启用更新解决方案](media/automation-onboard-solutions/enable-update.png)

> [!NOTE]
> 如果不等待至另一个解决方案完成，启用下一个解决方案时会收到一条消息：*此虚拟机或另一台虚拟机上正在安装另一个解决方案。待该解决方案安装完成后，“启用”按钮处于启用状态，可请求在此虚拟机上安装下一个解决方案。*

## <a name="install-and-update-modules"></a>安装和更新模块

必须更新到最新的 Azure 模块并导入 `AzureRM.OperationalInsights` 才能成功完成解决方案的自动载入。

## <a name="update-azure-modules"></a>更新 Azure 模块

从自动化帐户中，选择“共享资源”下的“模块”。 选择“更新 Azure 模块”以更新到最新版本的 Azure 模块。 在出现的提示上选择“是”，将所有现有 Azure 模块更新到最新版本。

![更新模块](media/automation-onboard-solutions/update-modules.png)

### <a name="install-azurermoperationalinsights-module"></a>安装 AzureRM.OperationalInsights 模块

从“模块”页上，选择“浏览库”以打开模块库。 搜索 AzureRM.OperationalInsights 并将此模块导入到自动化帐户。

![导入 OperationalInsights 模块](media/automation-onboard-solutions/import-operational-insights-module.png)

## <a name="import-the-onboarding-runbook"></a>导入载入 Runbook

1. 从自动化帐户，选择“流程自动化”下的“Runbook”。
1. 选择“浏览库”。
1. 搜索“更新和更改跟踪”，单击 runbook，然后选择“查看源”页上的“导入”。 选择“确定”，将 runbook 导入自动化帐户。

  ![导入载入 Runbook](media/automation-onboard-solutions/import-from-gallery.png)

1. 在“Runbook”页上，选择“编辑”，然后选择“发布”。 在“发布 Runbook”对话框中，选择“是”以发布 runbook。

## <a name="start-the-runbook"></a>启动 Runbook

若要启动此 Runbook，必须事先将更改跟踪或更新解决方案加入 Azure VM。 此 Runbook 需要使用已加入解决方案的现有虚拟机和资源组作为参数。

1. 打开 Enable-MultipleSolution Runbook。

   ![多个解决方案 Runbook](media/automation-onboard-solutions/runbook-overview.png)

1. 单击“启动”按钮并输入以下参数值。

   * **数据库** - 保留空白。 已加入更新或更改跟踪解决方案的现有 VM 的名称。 通过将此值留空，可载入资源组中的所有 VM。
   * **VMRESOURCEGROUP** - 要载入的 VM 的资源组的名称。
   * **SUBSCRIPTIONID** -保留为空。 要加入的新 VM 的订阅 ID。 如果留空，将使用工作区的订阅。 如果指定不同的订阅 ID，则还应该添加此自动化帐户的运行方式帐户作为此订阅的参与者。
   * **ALREADYONBOARDEDVM** - 已手动加入更新或更改跟踪解决方案的 VM 的名称。
   * **ALREADYONBOARDEDVMRESOURCEGROUP** - VM 所属的资源组的名称。
   * **SOLUTIONTYPE** - 输入“更新”或“更改跟踪”

   ![Enable-MultipleSolution Runbook 参数](media/automation-onboard-solutions/runbook-parameters.png)

1. 选择“确定”启动 Runbook 作业。
1. 在 Runbook 的“作业”页上监视进度和任何错误。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 手动加入 Azure 虚拟机。
> * 安装和更新所需的 Azure 模块。
> * 导入用于加入 Azure VM 的 Runbook。
> * 启动用于自动加入 Azure VM 的 Runbook。

请单击以下链接了解有关计划 runbook 的详细信息。

> [!div class="nextstepaction"]
> [计划 Runbook](automation-schedules.md)。
