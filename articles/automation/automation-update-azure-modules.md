---
title: "更新 Azure 自动化中的 Azure 模块 | Microsoft Docs"
description: "本文介绍如何更新 Azure 自动化中默认提供的常见 Azure PowerShell 模块。"
services: automation
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/20/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: 6765ea93dd4e4e2594fb147dd19120aec058a2f5
ms.lasthandoff: 04/21/2017


---

# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>如何在 Azure 自动化中更新 Azure PowerShell 模块

默认情况下，每个自动化帐户中都提供最常见的 Azure PowerShell 模块。  Azure 团队会定期更新 Azure 模块，因此在自动化帐户中，我们提供了一种方法，用于在门户中有新版本时更新帐户中的模块。  

## <a name="updating-azure-modules"></a>更新 Azure 模块

1. 在自动化帐户的“模块”边栏选项卡中有一个名为“更新 Azure 模块”的选项。  该选项始终处于启用状态。<br><br> ![“模块”边栏选项卡中的“更新 Azure 模块”选项](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

2. 单击“更新 Azure 模块”，会出现一条询问是否要继续的确认通知。<br><br> ![更新 Azure 模块通知](media/automation-update-azure-modules/automation-update-azure-modules-popup.png)

3. 单击“是”，模块更新过程开始。  更新过程大约需要 15-20 分钟来更新以下模块：

  * Azure
  *    Azure.Storage
  *    AzureRm.Automation
  *    AzureRm.Compute
  *    AzureRm.Profile
  *    AzureRm.Resources
  *    AzureRm.Sql
  * AzureRm.Storage

    如果模块已经是最新的，则该过程只需几秒钟即可完成。  更新过程完成后将收到通知。<br><br> ![更新 Azure 模块更新状态](media/automation-update-azure-modules/automation-update-azure-modules-updatestatus.png)

每当创建计划时，按该计划运行的任何后续作业都将使用创建该计划时自动化帐户中的模块。  若要开始对计划 Runbook 使用更新的模块，需要对该 Runbook 取消链接然后重新链接计划。   

如果在 Runbook 中使用这些 Azure PowerShell 模块中的 cmdlet 来管理 Azure 资源，则需要大约每月执行此更新过程一次，以确保拥有最新的模块。

## <a name="next-steps"></a>后续步骤

若要详细了解集成模块以及如何创建自定义模块以进一步将自动化与其他系统、服务或解决方案集成，请参阅[集成模块](automation-integration-modules.md)。
