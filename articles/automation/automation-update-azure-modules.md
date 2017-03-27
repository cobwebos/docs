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
ms.date: 02/13/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 7a2999b3b1a54668f6ef45433efabd5a495418fe
ms.openlocfilehash: ec84df70d4a77e3b81a88aa286fc492d92e3e753


---

# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>如何在 Azure 自动化中更新 Azure PowerShell 模块

默认情况下，每个自动化帐户中都提供最常见的 Azure PowerShell 模块。  Azure 团队会定期更新 Azure 模块，因此在自动化帐户中，我们提供了一种方法，用于在有新版本时更新帐户中的模块。

## <a name="updating-azure-modules"></a>更新 Azure 模块

1. 在自动化帐户的“模块”边栏选项卡中有一个名为“更新 Azure 模块”的选项。  该选项始终处于启用状态。<br><br> ![“模块”边栏选项卡中的“更新 Azure 模块”选项](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

2. 单击“更新 Azure 模块”，会出现一条询问是否要继续的确认通知。<br><br> ![更新 Azure 模块通知](media/automation-update-azure-modules/automation-update-azure-modules-notification.png)

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

作为更新过程的一部分，任何计划 Runbook 的计划将更新为使用最新的模块版本。

如果在 Runbook 中使用这些 Azure PowerShell 模块中的 cmdlet 来管理 Azure 资源，则需要大约每月执行此更新过程一次，以确保拥有最新的模块。

## <a name="next-steps"></a>后续步骤

若要详细了解集成模块以及如何创建自定义模块以进一步将自动化与其他系统、服务或解决方案集成，请参阅[集成模块](automation-integration-modules.md)。


<!--HONumber=Feb17_HO2-->


