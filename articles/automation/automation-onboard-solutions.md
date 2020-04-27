---
title: 将更新、更改跟踪和清单解决方案加入 Azure 自动化
description: 了解如何将更新和更改跟踪解决方案加入 Azure 自动化。
services: automation
ms.topic: tutorial
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 721157c333e381799ef08930c667c51a51a4fd6a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457614"
---
# <a name="onboard-update-change-tracking-and-inventory-solutions-to-azure-automation"></a>将更新、更改跟踪和清单解决方案加入 Azure 自动化

本教程介绍如何自动将 VM 的更新、更改跟踪和清单解决方案加入 Azure 自动化：

> [!div class="checklist"]
> * 载入 Azure VM
> * 启用解决方案
> * 安装和更新模块
> * 导入载入 Runbook
> * 启动 Runbook

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可参阅[如何更新 Azure 自动化中的 Azure PowerShell 模块](automation-update-azure-modules.md)，将模块更新到最新版本。

## <a name="prerequisites"></a>先决条件

完成本教程需要以下各项：

* Azure 订阅。 如果还没有帐户，可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 用于管理计算机的[自动化帐户](automation-offering-get-started.md)。
* 要载入的[虚拟机](../virtual-machines/windows/quick-create-portal.md)。

## <a name="onboard-an-azure-vm"></a>载入 Azure VM

载入计算机的方式有多种，可以[通过虚拟机](automation-onboard-solutions-from-vm.md)、[通过浏览多个计算机](automation-onboard-solutions-from-browse.md)、[通过自动化帐户](automation-onboard-solutions-from-automation-account.md)或通过 runbook 载入解决方案。 本教程详细介绍了如何通过 Runbook 启用更新管理。 若要大规模载入 Azure 虚拟机，须使用更改跟踪或更新管理解决方案载入现有 VM。 在此步骤中，使用更新管理和更改跟踪载入虚拟机。

### <a name="enable-change-tracking-and-inventory"></a>启用更改跟踪和库存

使用更改跟踪和清单解决方案，可以在虚拟机上[跟踪更改](automation-vm-change-tracking.md)并[列出清单](automation-vm-inventory.md)。 在此步骤中，你将在虚拟机上启用这些解决方案。

1. 在 Azure 门户中，选择“自动化帐户”  ，然后在列表中选择你的自动化帐户。
1. 在“配置管理”  下选择“清单”  。
1. 选择现有的某个 Log Analytics 工作区，或者新建一个工作区。 
1. 单击 **“启用”** 。

    ![载入更新解决方案](media/automation-onboard-solutions/inventory-onboard.png)

### <a name="enable-update-management"></a>启用更新管理

使用更新管理解决方案可以管理 Azure Windows VM 的更新和修补程序。 可评估可用更新的状态、计划所需更新的安装以及查看部署结果，验证更新是否已成功应用到 VM。 在此步骤中，为 VM 启用解决方案。

1. 在你的自动化帐户的“更新管理”  部分中选择“更新管理”  。
1. 所选的 Log Analytics 工作区是前面步骤中使用的工作区。 单击“启用”  以载入更新管理解决方案。 更新管理解决方案安装期间会显示一个蓝色横幅。 

    ![载入更新解决方案](media/automation-onboard-solutions/update-onboard.png)

### <a name="select-azure-vm-to-be-managed"></a>选择要管理的 Azure VM

现已启用解决方案，可添加 Azure VM 以载入到这些解决方案中。

1. 在你的自动化帐户中的“配置管理”  下选择“更改跟踪”  。 
2. 在“更改跟踪”页上，单击“添加 Azure VM”以添加 VM。 

3. 从列表中选择你的 VM，然后单击“启用”  。 此操作为 VM 启用更改跟踪和清单解决方案。

   ![为虚拟机启用更新解决方案](media/automation-onboard-solutions/enable-change-tracking.png)

4. 在 VM 加入通知完成后，在“更新管理”  下选择“更新管理”  。

5. 选择“添加 Azure VM”以添加你的 VM。 

6. 从列表中选择 VM，然后选择“启用”  。 此操作为 VM 启用更新管理解决方案。

   ![为虚拟机启用更新解决方案](media/automation-onboard-solutions/enable-update.png)

> [!NOTE]
> 如果不等待其他解决方案完成，则在启用下一个解决方案时，你会收到以下消息：`Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="install-and-update-modules"></a>安装和更新模块

必须更新到最新的 Azure 模块并导入 [Az.OperationalInsights](https://docs.microsoft.com/powershell/module/az.operationalinsights/?view=azps-3.7.0) 模块才能成功完成解决方案的自动加入。

## <a name="update-azure-modules"></a>更新 Azure 模块

1. 在你的自动化帐户中的“共享资源”下选择“模块”   。 
2. 选择“更新 Azure 模块”  以更新到最新版本的 Azure 模块。 
3. 单击“是”  ，将所有现有 Azure 模块更新到最新版本。

![更新模块](media/automation-onboard-solutions/update-modules.png)

### <a name="install-azoperationalinsights-module"></a>安装 Az.OperationalInsights 模块

1. 在自动化帐户中的“共享资源”下选择“模块”   。 
2. 选择“浏览库”  以打开模块库。 
3. 搜索 Az.OperationalInsights 并将此模块导入到自动化帐户中。

![导入 OperationalInsights 模块](media/automation-onboard-solutions/import-operational-insights-module.png)

## <a name="import-the-onboarding-runbook"></a>导入载入 Runbook

1. 在你的自动化帐户中的“流程自动化”  下选择“Runbook”  。
1. 选择“浏览库”  。
1. 搜索 `update and change tracking`。
3. 选择 Runbook，然后在“查看源”页上单击“导入”  。 
4. 单击“确定”  ，将 Runbook 导入到自动化帐户中。

   ![导入载入 Runbook](media/automation-onboard-solutions/import-from-gallery.png)

6. 在“Runbook”页上单击“编辑”  ，然后选择“发布”  。 
7. 在“发布 Runbook”窗格中，单击“是”  以发布 Runbook。

## <a name="start-the-runbook"></a>启动 Runbook

若要启动此 Runbook，必须事先将更改跟踪或更新解决方案加入 Azure VM。 此 Runbook 需要使用已加入解决方案的现有虚拟机和资源组作为参数。

1. 打开“Enable-MultipleSolution”  Runbook。

   ![多个解决方案 Runbook](media/automation-onboard-solutions/runbook-overview.png)

1. 单击“启动”按钮并输入以下参数值。

   * **数据库** - 保留空白。 已加入更新或更改跟踪解决方案的现有 VM 的名称。 通过将此值留空，可载入资源组中的所有 VM。
   * **VMRESOURCEGROUP** - 要载入的 VM 的资源组的名称。
   * **SUBSCRIPTIONID** -保留为空。 要加入的新 VM 的订阅 ID。 如果留空，将使用工作区的订阅。 如果指定不同的订阅 ID，则还应该添加此自动化帐户的运行方式帐户作为此订阅的参与者。
   * **ALREADYONBOARDEDVM** - 已手动加入更新或更改跟踪解决方案的 VM 的名称。
   * **ALREADYONBOARDEDVMRESOURCEGROUP** - VM 所属的资源组的名称。
   * **SOLUTIONTYPE** - 输入“更新”  或“更改跟踪”  。

   ![Enable-MultipleSolution Runbook 参数](media/automation-onboard-solutions/runbook-parameters.png)

1. 选择“确定”  启动 Runbook 作业。
1. 在 Runbook 的“作业”页上监视进度和任何错误。

## <a name="clean-up-resources"></a>清理资源

从更新管理中删除 VM：

1. 在 Log Analytics 工作区中，从范围配置 `MicrosoftDefaultScopeConfig-Updates` 的已保存搜索中删除 VM。 已保存的搜索位于工作区的“常规”下  。
2. 删除[适用于 Windows 的 Log Analytics 代理](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources)或[适用于 Linux 的 Log Analytics 代理](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources)。

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
> * 手动加入 Azure 虚拟机。
> * 安装和更新所需的 Azure 模块。
> * 导入用于加入 Azure VM 的 Runbook。
> * 启动用于自动加入 Azure VM 的 Runbook。

请单击以下链接了解有关计划 runbook 的详细信息。

> [!div class="nextstepaction"]
> [计划 Runbook](automation-schedules.md)。
