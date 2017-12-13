---
title: "将更新和更改跟踪解决方案加入 Azure 自动化 | Microsoft Docs"
description: "了解如何将更新和更改跟踪解决方案加入 Azure 自动化。"
services: automation
documentationcenter: 
author: eamonoreilly
manager: 
editor: 
ms.assetid: edae1156-2dc7-4dab-9e5c-bf253d3971d0
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2017
ms.author: eamono
ms.openlocfilehash: 9ae5e9ebcbcf3af61318eac98defc6b249417f41
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2017
---
# <a name="onboard-update-and-change-tracking-solutions-to-azure-automation"></a>将更新和更改跟踪解决方案加入 Azure 自动化

本教程介绍如何自动将 VM 的更新、更改跟踪和清单解决方案加入 Azure 自动化：

> [!div class="checklist"]
> * 手动加入 Azure 虚拟机。
> * 安装和更新所需的 Azure 模块。
> * 导入用于加入 Azure VM 的 Runbook。
> * 启动用于自动加入 Azure VM 的 Runbook。

## <a name="prerequisites"></a>先决条件

完成本教程需要以下各项。
+ Azure 订阅。 如果还没有帐户，可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
+ 用于管理计算机的[自动化帐户](automation-offering-get-started.md)。

## <a name="onboard-an-azure-virtual-machine-manually"></a>手动加入 Azure 虚拟机

1.  打开自动化帐户。
2.  在“库存”页上单击。
![加入清单解决方案](media/automation-onboard-solutions/inventory-onboard.png)
3.  选择现有的或创建新的 Log Analytics 工作区。 单击“启用”按钮。
4.  更改跟踪和清单解决方案加入通知完成后，请在“更新管理”页上单击。
![加入更新解决方案](media/automation-onboard-solutions/update-onboard.png)
4.  单击用于加入更新解决方案的“启用”按钮。
5.  更新解决方案加入通知完成后，请在“更改跟踪”页上单击。
![加入更改跟踪](media/automation-onboard-solutions/change-tracking-onboard-vm.png)
6.  单击“添加 Azure VM”按钮。
![选择更改跟踪的 VM](media/automation-onboard-solutions/enable-change-tracking.png)
7.  选择一个 Azure VM，单击用于加入更改跟踪和清单解决方案的“启用”按钮。
8.  VM 加入通知完成后，请在“更新管理”页上单击。
![加入用于更新管理的 VM](media/automation-onboard-solutions/update-onboard-vm.png)
9.  单击“添加 Azure VM”按钮。
![选择用于更新管理的 VM](media/automation-onboard-solutions/enable-update.png)
10.  选择一个 Azure VM，单击用于加入更新管理解决方案的“启用”按钮。

## <a name="install-and-update-required-azure-modules"></a>安装和更新所需的 Azure 模块

必须更新到最新的 Azure 模块并导入 AzureRM.OperationalInsights 才能成功完成解决方案的自动加入。
1.  在“模块”页上单击。
![更新模块](media/automation-onboard-solutions/update-modules.png)
2.  单击用于更新到最新版本的“更新 Azure 模块”按钮。 等待更新完成。
3.  单击“浏览库”按钮打开模块库。
![导入 OperationalInsights 模块](media/automation-onboard-solutions/import-operational-insights-module.png)
4.  搜索 AzureRM.OperationalInsights 并将此模块导入到自动化帐户。

## <a name="import-a-runbook-that-onboards-solutions-to-azure-vms"></a>导入用于将解决方案加入 Azure VM 的 Runbook

1.  单击“流程自动化”类别下的“Runbook”页。
2.  单击“浏览库”按钮。
3.  搜索“更新和更改跟踪”，并将该 Runbook 导入到自动化帐户。
![导入加入 Runbook](media/automation-onboard-solutions/import-from-gallery.png)
4.  单击“编辑”查看 Runbook 源，然后单击“发布”按钮。
![导入加入 Runbook](media/automation-onboard-solutions/publish-runbook.png)

## <a name="start-the-runbook-that-onboards-azure-vms-automatically"></a>启动用于自动加入 Azure VM 的 Runbook

若要启动此 Runbook，必须事先将更改跟踪或更新解决方案加入 Azure VM。 此 Runbook 需要使用已加入解决方案的现有虚拟机和资源组作为参数。
1.  打开 Enable-MultipleSolution Runbook。
![多个解决方案 Runbook](media/automation-onboard-solutions/runbook-overview.png)
2.  单击“启动”按钮并输入以下参数值。
    *   VMNAME。 已加入更新或更改跟踪解决方案的现有 VM 的名称。 如果留空，则会加入资源组中的所有 VM。
    *   VMRESOURCEGROUP。 VM 所属的资源组的名称。
    *   SUBSCRIPTIONID。 要加入的新 VM 所在的订阅 ID。 如果留空，将使用工作区的订阅。 如果指定不同的订阅 ID，则还应该添加此自动化帐户的运行方式帐户作为此订阅的参与者。
    *   ALREADYONBOARDEDVM。 已手动加入更新或更改跟踪解决方案的 VM 的名称。
    *   ALREADYONBOARDEDVMRESOURCEGROUP。 VM 所属的资源组的名称。
    *   SOLUTIONTYPE。 输入“更新”或“更改跟踪”
    
    ![多个解决方案 Runbook 参数](media/automation-onboard-solutions/runbook-parameters.png)
3.  单击“确定”启动 Runbook 作业。
4.  在 Runbook 的“作业”页上监视进度和任何错误。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅[计划 Runbook](automation-schedules.md)。