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
ms.date: 06/13/2017
ms.author: magoedte
ms.translationtype: Human Translation
ms.sourcegitcommit: fc27849f3309f8a780925e3ceec12f318971872c
ms.openlocfilehash: ed8c97b642d406a05817ec6c67f31a1b4bce93b0
ms.contentlocale: zh-cn
ms.lasthandoff: 06/14/2017


---

# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>如何在 Azure 自动化中更新 Azure PowerShell 模块

默认情况下，每个自动化帐户中都提供最常见的 Azure PowerShell 模块。  Azure 团队会定期更新 Azure 模块，因此在自动化帐户中，我们提供了一种方法，用于在门户中有新版本时更新帐户中的模块。  

由于模块由产品组定期更新，所包含的 cmdlet 可能会发生更改，这可能会对 runbook 产生负面影响，具体要取决于更改类型（如重命名参数或完全弃用 cmdlet）。 为了避免影响你的 runbook 及其自动化过程，强烈建议在继续操作之前进行测试和验证。  如果没有用于此目的专用自动化帐户，请考虑创建一个自动化帐户，以便可以在 runbook 开发期间测试许多不同的方案和排列，以及更新 PowerShell 模块等迭代更改。  在验证结果并且应用了所需的任何更改之后，请继续协调需要修改的任何 runbook 的迁移，然后按照以下生产环境中的描述执行更新。     

## <a name="updating-azure-modules"></a>更新 Azure 模块

1. 在自动化帐户的“模块”边栏选项卡中有一个名为“更新 Azure 模块”的选项。  该选项始终处于启用状态。<br><br> ![“模块”边栏选项卡中的“更新 Azure 模块”选项](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

2. 单击“更新 Azure 模块”，会出现一条询问是否要继续的确认通知。<br><br> ![更新 Azure 模块通知](media/automation-update-azure-modules/automation-update-azure-modules-popup.png)

3. 单击“是”，模块更新过程开始。  更新过程大约需要 15-20 分钟来更新以下模块：

  * Azure
  * Azure.Storage
  * AzureRm.Automation
  * AzureRm.Compute
  * AzureRm.Profile
  * AzureRm.Resources
  * AzureRm.Sql
  * AzureRm.Storage

    如果模块已经是最新的，则该过程只需几秒钟即可完成。  更新过程完成后将收到通知。<br><br> ![更新 Azure 模块更新状态](media/automation-update-azure-modules/automation-update-azure-modules-updatestatus.png)

> [!NOTE]
> 当运行新的计划作业时，Azure 自动化将使用自动化帐户中的最新模块。    

如果在 Runbook 中使用这些 Azure PowerShell 模块中的 cmdlet 来管理 Azure 资源，则需要大约每月执行此更新过程一次，以确保拥有最新的模块。

## <a name="next-steps"></a>后续步骤

* 若要详细了解集成模块以及如何创建自定义模块以进一步将自动化与其他系统、服务或解决方案集成，请参阅[集成模块](automation-integration-modules.md)。

* 考虑使用 [GitHub Enterprise](automation-scenario-source-control-integration-with-github-ent.md) 或 [Visual Studio Team Services](automation-scenario-source-control-integration-with-vsts.md) 进行源控件集成，以集中管理和控制自动化 runbook 和配置组合的发布。  
