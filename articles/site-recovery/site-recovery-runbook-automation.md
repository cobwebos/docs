---
title: "在 Site Recovery 中将 Azure 自动化 Runbook 添加到恢复计划 | Microsoft 文档"
description: "本指南介绍了如何借助 Azure Site Recovery，在恢复到 Azure 期间使用 Azure 自动化完成复杂任务，从而扩展恢复计划"
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: ecece14d-5f92-4596-bbaf-5204addb95c2
ms.service: site-recovery
ms.devlang: powershell
ms.tgt_pltfrm: na
ms.topic: article
ms.workload: required
ms.date: 02/06/2017
ms.author: ruturajd@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: 44b6ff6e588d529fd833a4a7fdd61df7e933ddd8
ms.openlocfilehash: b88974ef713211a40b52aafab1b079ed8dbfec49


---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>将 Azure 自动化 Runbook 添加到恢复计划
本教程介绍 Azure Site Recovery 如何与 Azure 自动化集成以便为恢复计划提供可扩展性。 恢复计划可以协调使用 Azure Site Recovery 保护的虚拟机的恢复，以便复制到辅助云和 Azure 方案。 恢复计划还有助于实现恢复的**一致准确性**、**可重复性**和**自动化**。 如果你要将虚拟机故障转移到 Azure，则与 Azure 自动化集成可扩展恢复计划，并使你能够执行 Runbook，从而可以执行强大的自动化任务。

如果你还没有听说过 Azure 自动化，请单击[此处](https://azure.microsoft.com/services/automation/)进行注册，然后从[此处](https://azure.microsoft.com/documentation/scripts/)下载示例脚本。 若要详细了解 [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/)，以及如何使用恢复计划来安排 Azure 恢复，请单击[此处](https://azure.microsoft.com/blog/?p=166264)。

在本教程中，我们将了解如何将 Azure 自动化 Runbook 集成到恢复计划。 我们将自动执行以前需要手动干预的简单任务，并了解如何将多步骤恢复转换成单击恢复操作。 我们还将了解如何解决已出错的简单脚本。

## <a name="customize-the-recovery-plan"></a>自定义恢复计划
1. 让我们从打开恢复计划的资源边栏选项卡开始。 你可以看到，恢复计划添加了两台虚拟机用于恢复。

    ![](media/site-recovery-runbook-automation-new/essentials-rp.PNG)
- - -
1. 单击自定义按钮，开始添加 runbook。 这会打开恢复计划的自定义边栏选项卡。

    ![](media/site-recovery-runbook-automation-new/customize-rp.PNG)


1. 右键单击开始组 1 并选择添加“添加后操作”。
2. 选择并在新的边栏选项卡中选择一个脚本。
3. 将脚本命名为“Hello World”。
4. 选择“自动化帐户”名称。 这是 Azure 自动化帐户。 请注意，此帐户可以位于任意 Azure 地理位置，但必须与 Site Recovery 保管库采用相同的订阅。
5. 从自动化帐户中选择一个 runbook。 此脚本将在执行恢复第一组后的恢复计划期间运行。

    ![](media/site-recovery-runbook-automation-new/update-rp.PNG)
6. 选择“确定”保存脚本。 这会将脚本添加到“组 1: 启动”的后操作。

    ![](media/site-recovery-runbook-automation-new/addedscript-rp.PNG)


## <a name="salient-points-of-adding-a-script"></a>添加脚本突出点
1. 可以右键单击脚本，并选择“删除步骤”或“更新脚本”。
2. 当从本地到 Azure 的进行故障转移时，脚本可以在 Azure 上运行，并且，在进行从 Azure 到本地的故障回复期间，脚本可以在关闭之前作为主端脚本在 Azure 上运行。
3. 当脚本运行时，它将注入恢复计划上下文。

下面是上下文变量形式的示例。

        {"RecoveryPlanName":"hrweb-recovery",

        "FailoverType":"Test",

        "FailoverDirection":"PrimaryToSecondary",

        "GroupId":"1",

        "VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":

                {"CloudServiceName":"pod02hrweb-Chicago-test",

                "RoleName":"Fabrikam-Hrweb-frontend-test"}

                }

        }


下表包含上下文中每个变量的名称和说明。

| **变量名称** | **说明** |
| --- | --- |
| RecoveryPlanName |正在运行的计划的名称。 帮助你根据名称使用相同的脚本执行操作 |
| FailoverType |指定故障转移是测试、计划内还是计划外。 |
| FailoverDirection |指定恢复是恢复到主要站点还是辅助站点 |
| GroupID |标识计划运行时恢复计划内的组编号 |
| VmMap |组中所有虚拟机的阵列 |
| VMMap 键 |每个 VM 的唯一键 (GUID)。 与虚拟机的适用 VMM ID 相同。 |
| RoleName |正在恢复的 Azure VM 的名称 |
| CloudServiceName |要在其下创建虚拟机的 Azure 云服务名称。 |
| CloudServiceName（在 Resource Manager 部署模型中） |在其下创建虚拟机的 Azure 资源组名称。 |

## <a name="using-complex-variables-per-recovery-plan"></a>按恢复计划使用复杂变量
有时，runbook 需要比 RecoveryPlanContext 更详细的信息。 没有头等机制可以将参数传递给 runbook。 但是，如果想通过多个恢复计划使用相同的脚本，则可以使用恢复计划上下文变量“RecoveryPlanName”，并使用下面的实验技术以在 runbook 中使用 Azure 自动化复杂变量。 下面的示例显示了如何创建三个不同的复杂变量资产，并基于恢复计划的名称在 runbook 中使用。

请考虑你想要在 runbook 中使用的 3 个其他参数。 让我们将其编码为 JSON 形式 {"Var1":"testautomation","Var2":"Unplanned","Var3":"PrimaryToSecondary"}

使用 [AA 复杂变量](../automation/automation-variables.md#variable-types)创建新的自动化资产。
将变量命名为 <RecoveryPlanName>-params。
你可以使用此处的引用创建[复杂变量](https://msdn.microsoft.com/library/dn913767.aspx?f=255&MSPPError=-2147217396)。

对于不同的恢复计划，将变量命名为

1. recoveryPlanName1>-params
2. recoveryPlanName2>-params
3. recoveryPlanName3>-params

现在，在脚本中将参数引用为

1. 从 $rpname = $Recoveryplancontext 变量中获取 RP 名称
2. 获取 $paramValue = "$($rpname)-params" 的资产
3. 通过调用 Get-AzureAutomationVariable [-AutomationAccountName] <String> -Name $paramValue，将其用作恢复计划的复杂变量。

例如，要获取 SharepointApp 恢复计划的复杂变量/参数，请创建名为“SharepointApp-params”的 Azure 自动化复杂变量。

通过使用 Get-AzureAutomationVariable [-AutomationAccountName] <String> [-Name] $paramValue 语句从资产中提取变量，将其用于恢复计划。 [引用以获取更多详细信息](https://msdn.microsoft.com/library/dn913772.aspx)

这样就可以通过存储资产中特定于计划的复杂变量，将同一脚本用于不同的恢复计划。

## <a name="sample-scripts"></a>示例脚本
对于可以直接导入到自动化帐户中的脚本存储库，请参阅 [Kristian Nese 的 OMS 脚本存储库](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/Solutions/asrautomation)

此处的脚本是 Azure Resource Manager 模板，将部署下面的所有脚本

* NSG

NSG runbook 将向恢复计划中的每个 VM 分配公共 IP 地址，并将虚拟网络适配器附加到允许默认通信的网络安全组

* PublicIP

公共 IP runbook 将向恢复计划中的每个 VM 分配公共 IP 地址。 对计算机和应用程序的访问将取决于每个来宾的防火墙设置

* CustomScript

CustomScript runbook 将向恢复计划中的每个 VM 分配公共 IP 地址并安装自定义脚本扩展，此扩展将在部署模板期间拉取你引用的脚本

* NSGwithCustomScript

NSGwithCustomScript runbook 将向恢复计划中的每个 VM 分配公共 IP 地址，使用扩展安装自定义脚本，并将虚拟网络适配器连接到允许进行远程访问的默认入站和出站通信的 NSG

## <a name="additional-resources"></a>其他资源
[Azure 自动化服务作为帐户运行](../automation/automation-sec-configure-azure-runas-account.md)

[Azure 自动化概述](http://msdn.microsoft.com/library/azure/dn643629.aspx "Azure 自动化概述")

[Azure 自动化脚本示例](http://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=User&f\[0\].Value=SC%20Automation%20Product%20Team&f\[0\].Text=SC%20Automation%20Product%20Team "Azure 自动化脚本示例")



<!--HONumber=Jan17_HO5-->


