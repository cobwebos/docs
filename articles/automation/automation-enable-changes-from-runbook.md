---
title: 从 Runbook 启用 Azure 自动化更改跟踪和库存
description: 本文介绍如何从 Runbook 启用更改跟踪和库存。
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 92149289c6c422179ddb3562274020acf779f10b
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2020
ms.locfileid: "86186276"
---
# <a name="enable-change-tracking-and-inventory-from-a-runbook"></a>从 runbook“启用更改跟踪和清单”

本文介绍如何使用 Runbook 为环境中的虚拟机 (VM) 启用[更改跟踪和库存](change-tracking.md)功能。 若要大规模启用 Azure VM，必须使用更改跟踪和库存启用现有 VM。 

> [!NOTE]
> 在启用更改跟踪和库存时，只有某些区域支持链接 Log Analytics 工作区和自动化帐户。 有关支持的映射对的列表，请参阅[自动化帐户和 Log Analytics 工作区的区域映射](how-to/region-mappings.md)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果还没有帐户，可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 用于管理计算机的[自动化帐户](./index.yml)。
* [虚拟机](../virtual-machines/windows/quick-create-portal.md)。

## <a name="enable-change-tracking-and-inventory"></a>启用更改跟踪和库存 

1. 在 Azure 门户中，选择“自动化帐户”，然后在列表中选择你的自动化帐户。
1. 在“配置管理”下选择“清单”。
1. 选择现有的某个 Log Analytics 工作区，或者新建一个工作区。 
1. 单击 **“启用”** 。

    ![启用更改跟踪和库存](media/automation-enable-changes-from-runbook/inventory-onboard.png)

## <a name="select-azure-vm-to-manage"></a>选择要管理的 Azure VM

启用更改跟踪和库存后，可添加 Azure VM，便于按功能进行管理。

1. 在自动化帐户中，选择“配置管理”下的“更改跟踪”或“库存”  。

2. 单击“添加 Azure VM”，添加你的 VM。

3. 从列表中选择你的 VM，然后单击“启用”。 此操作为 VM 启用更改跟踪和库存。

   ![为 VM 启用更改跟踪和库存](media/automation-enable-changes-from-runbook/enable-change-tracking.png)

    > [!NOTE]
    > 如果在完成“更改跟踪和库存”设置之前尝试启用其他功能，会收到以下消息：`Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="install-and-update-modules"></a>安装和更新模块

必须更新到最新的 Azure 模块并导入 [Az.OperationalInsights](/powershell/module/az.operationalinsights/?view=azps-3.7.0) 模块才能成功为 VM 启用更改跟踪和库存。

1. 在你的自动化帐户中的“共享资源”下选择“模块” 。 
2. 选择“更新 Azure 模块”以更新到最新版本的 Azure 模块。 
3. 单击“是”，将所有现有 Azure 模块更新到最新版本。

    ![更新模块](media/automation-enable-changes-from-runbook/update-modules.png)

4. 返回“共享资源”下的“模块” 。 
5. 选择“浏览库”以打开模块库。 
6. 搜索 Az.OperationalInsights 并将此模块导入到自动化帐户中。

    ![导入 OperationalInsights 模块](media/automation-enable-changes-from-runbook/import-operational-insights-module.png)

## <a name="import-a-runbook-to-enable-change-tracking-and-inventory"></a>导入 Runbook 以启用更改跟踪和库存

1. 在你的自动化帐户中的“流程自动化”下选择“Runbook”。
2. 选择“浏览库”。
3. 搜索 `update and change tracking`。
4. 选择 Runbook，然后在“查看源”页上单击“导入”。 
5. 单击“确定”，将 Runbook 导入到自动化帐户中。

   ![导入 Runbook 进行设置](media/automation-enable-changes-from-runbook/import-from-gallery.png)

6. 在“Runbook”页上单击“编辑”，然后选择“发布”。 
7. 在“发布 Runbook”窗格中，单击“是”以发布 Runbook。

## <a name="start-the-runbook"></a>启动 Runbook

必须已为 Azure VM 启用了更改跟踪和库存，才能启动此 Runbook。 它需要为参数启用了该功能的现有 VM 和资源组。

1. 打开“Enable-MultipleSolution”Runbook。

   ![多个解决方案 Runbook](media/automation-enable-changes-from-runbook/runbook-overview.png)

1. 单击“启动”按钮，在以下字段中输入参数值：

   * **VMNAME** - 要添加到更改跟踪和库存中的现有 VM 的名称。 将此字段留空可添加资源组中的所有 VM。
   * **VMRESOURCEGROUP** - 要启用的 VM 的资源组的名称。
   * **SUBSCRIPTIONID** - 要启用的新 VM 的订阅 ID。 将此字段留空可使用工作区的订阅。 使用不同的订阅 ID 时，请添加自动化帐户的运行方式帐户作为订阅的参与者。
   * **ALREADYONBOARDEDVM** - 已为更改手动启用的 VM 的名称。
   * **ALREADYONBOARDEDVMRESOURCEGROUP** - VM 所属的资源组的名称。
   * **SOLUTIONTYPE** - 输入 ChangeTracking。

   ![Enable-MultipleSolution Runbook 参数](media/automation-enable-changes-from-runbook/runbook-parameters.png)

1. 选择“确定”启动 Runbook 作业。
1. 在 Runbook 的“作业”页上监视进度和任何错误。

## <a name="next-steps"></a>后续步骤

* 若要计划 Runbook，请参阅[在 Azure 自动化中管理计划](shared-resources/schedules.md)。
* 有关使用此功能的详细信息，请参阅[管理更改跟踪和库存](change-tracking-file-contents.md)。
* 若要排查该功能的常见问题，请参阅[排查更改跟踪和清单问题](troubleshoot/change-tracking.md)。
