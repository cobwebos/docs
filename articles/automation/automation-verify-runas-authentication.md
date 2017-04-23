---
title: "验证 Azure 自动化帐户配置 | Microsoft Docs"
description: "本文介绍如何确认自动化帐户的配置设置是正确的。"
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/14/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 5bed2616e15a2e5f52e79d0c28159b568e7a1de3
ms.lasthandoff: 04/15/2017

---

# <a name="test-azure-automation-run-as-account-authentication"></a>测试 Azure 自动化运行方式身份验证
成功创建自动化帐户以后，即可执行一个简单的测试，确认你能够成功地在 Azure Resource Manager 部署或 Azure 经典部署中使用新创建的或更新的自动化运行方式帐户进行身份验证。    

## <a name="automation-run-as-authentication"></a>自动化运行方式身份验证

1. 在 Azure 门户中，打开之前创建的自动化帐户。  
2. 单击“Runbook”  磁贴打开 Runbook 的列表。
3. 选择 **AzureAutomationTutorialScript** Runbook，然后单击“启动”以启动该 Runbook。  系统会提示用户确认要启动该 Runbook。
4. 将创建一个 [Runbook 作业](automation-runbook-execution.md)并显示“作业”边栏选项卡，“作业摘要”磁贴中显示作业状态。  
5. 作业状态最初为“排队”，表示它正在等待云中的 Runbook 辅助角色变为可用  。 在某个辅助角色认领该作业后，该作业状态将变为“正在启动”，然后当 Runbook 实际开始运行时，该作业状态将变为“正在运行”。  
6. Runbook 作业完成时，应会看到状态变为“已完成”。<br> ![安全主体 Runbook 测试](media/automation-verify-runas-authentication/job-summary-automationtutorialscript.png)<br>
7. 若要查看 Runbook 的详细结果，请单击“输出”磁贴。
8. 在“输出”边栏选项卡中，应会看到帐户已成功完成身份验证并返回资源组中所有可用资源的列表。
9. 关闭“输出”边栏选项卡返回“作业摘要”边栏选项卡。
10. 关闭“作业摘要”和相应的 **AzureAutomationTutorialScript** Runbook 边栏选项卡。

## <a name="classic-run-as-authentication"></a>经典运行方式身份验证
若要在经典部署模型中管理资源，请执行以下步骤，确认你能够使用新的经典运行方式帐户成功地进行身份验证。     

1. 在 Azure 门户中，打开之前创建的自动化帐户。  
2. 单击“Runbook”  磁贴打开 Runbook 的列表。
3. 选择 **AzureClassicAutomationTutorialScript** Runbook，然后单击“启动”以启动该 Runbook。  系统会提示用户确认要启动该 Runbook。
4. 将创建一个 [Runbook 作业](automation-runbook-execution.md)并显示“作业”边栏选项卡，“作业摘要”磁贴中显示作业状态。  
5. 作业状态最初为“排队”，表示它正在等待云中的 Runbook 辅助角色变为可用  。 在某个辅助角色认领该作业后，该作业状态将变为“正在启动”，然后当 Runbook 实际开始运行时，该作业状态将变为“正在运行”。  
6. Runbook 作业完成时，应会看到状态变为“已完成”。<br><br> ![安全主体 Runbook 测试](media/automation-verify-runas-authentication/job-summary-automationclassictutorialscript.png)<br>  
7. 若要查看 Runbook 的详细结果，请单击“输出”磁贴。
8. 在“输出”边栏选项卡中，应会看到帐户已成功完成身份验证并返回订阅中所有经典 VM 的列表。
9. 关闭“输出”边栏选项卡返回“作业摘要”边栏选项卡。
10. 关闭“作业摘要”和相应的 **AzureClassicAutomationTutorialScript** Runbook 边栏选项卡。

## <a name="next-steps"></a>后续步骤
* 若要开始使用 PowerShell Runbook，请参阅[我的第一个 PowerShell Runbook](automation-first-runbook-textual-powershell.md)。
* 若要了解有关图形创作的详细信息，请参阅 [Azure 自动化中的图形创作](automation-graphical-authoring-intro.md)。

