---
title: 在 Azure Site Recovery 中将 Azure 自动化 Runbook 添加到恢复计划 | Microsoft Docs
description: 了解 Azure Site Recovery 如何帮助用户使用 Azure 自动化扩展恢复计划。 了解如何在恢复到 Azure 期间完成复杂任务。
services: site-recovery
documentationcenter: ''
author: ruturaj
manager: gauravd
editor: ''
ms.assetid: ecece14d-5f92-4596-bbaf-5204addb95c2
ms.service: site-recovery
ms.devlang: powershell
ms.tgt_pltfrm: na
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 07/06/2018
ms.author: ruturajd@microsoft.com
ms.openlocfilehash: 55160f3c43d8cbfc5f8b3e6aaf26bcb911387c52
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2018
ms.locfileid: "39578759"
---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>将 Azure 自动化 Runbook 添加到恢复计划
本文将介绍 Azure Site Recovery 如何与 Azure 自动化集成，以便扩展恢复计划。 恢复计划可以安排恢复受 Site Recovery 保护的 VM。 恢复计划支持复制到辅助云和复制到 Azure。 恢复计划还有助于实现恢复的一致准确性、可重复性和自动化。 如果从 VM 故障转移到 Azure，与 Azure 自动化集成可以扩展恢复计划。 可用于执行 Runbook，从而提供功能强大的自动化任务。

如果刚开始接触 Azure 自动化，可以[注册](https://azure.microsoft.com/services/automation/)和[下载示例脚本](https://azure.microsoft.com/documentation/scripts/)。 有关详细信息，以及若要了解如何使用[恢复计划](./site-recovery-create-recovery-plans.md)来安排恢复到 Azure，请参阅 [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/)。

本文将介绍如何将 Azure 自动化 Runbook 集成到恢复计划中。 我们使用示例，自动执行以前需要手动干预的基本任务。 本文还将介绍如何将多步骤恢复过程转换为一键式恢复操作。

## <a name="customize-the-recovery-plan"></a>自定义恢复计划
1. 转到“Site Recovery”恢复计划资源边栏选项卡。 对于此示例，恢复计划包含两个要恢复的 VM。 若要开始添加 Runbook，请单击“自定义”选项卡。

    ![单击“自定义”按钮](media/site-recovery-runbook-automation-new/essentials-rp.png)


2. 右键单击“组 1：启动”，再选择“添加后操作”。

    ![右键单击“组 1：启动”，并添加后操作](media/site-recovery-runbook-automation-new/customize-rp.png)

3. 单击“选择脚本”。

4. 在“更新操作”边栏选项卡中，将脚本命名为“Hello World”。

    ![“更新操作”边栏选项卡](media/site-recovery-runbook-automation-new/update-rp.png)

5. 输入自动化帐户名称。
    >[!NOTE]
    > 自动化帐户可位于任意 Azure 区域。 自动化帐户必须位于 Azure Site Recovery 保管库所在的订阅中。

6. 在自动化帐户中，选择一个 Runbook。 此 Runbook 是在恢复第一组后执行恢复计划期间运行的脚本。

7. 选择“确定”，保存脚本。 此时，脚本添加到“组 1：后步骤”。

    ![“组 1：后步骤”](media/site-recovery-runbook-automation-new/addedscript-rp.PNG)


## <a name="considerations-for-adding-a-script"></a>添加脚本时的注意事项

* 若要删除步骤或更新脚本，请右键单击脚本。
* 从本地计算机故障转移到 Azure 期间，可以在 Azure 上运行脚本。 从 Azure 故障回复到本地计算机期间，它还可以在关闭前，在 Azure 上作为主站点脚本运行。
* 当脚本运行时，它将注入恢复计划上下文。 下面的示例展示了上下文变量：

    ```
            {"RecoveryPlanName":"hrweb-recovery",

            "FailoverType":"Test",

            "FailoverDirection":"PrimaryToSecondary",

            "GroupId":"1",

            "VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":

                    { "SubscriptionId":"7a1111111-c1d6-49c5-8c5d-111ce8dd183",

                    "ResourceGroupName":"ContosoRG",

                    "CloudServiceName":"pod02hrweb-Chicago-test",

                    "RoleName":"Fabrikam-Hrweb-frontend-test",

                    "RecoveryPointId":"TimeStamp"}

                    }

            }
    ```

    下表列出了上下文中每个变量的名称和说明。

    | **变量名称** | **说明** |
    | --- | --- |
    | RecoveryPlanName |正在运行的计划的名称。 此变量有助于用户根据恢复计划名称执行不同的操作。 还可以重用脚本。 |
    | FailoverType |指定故障转移是测试、计划内还是计划外。 |
    | FailoverDirection |指定是恢复到主站点，还是恢复到辅助站点。 |
    | GroupID |计划运行时，在恢复计划内标识组编号。 |
    | VmMap |组中所有 VM 的数组。 |
    | VMMap 键 |每个 VM 的唯一键 (GUID)。 与 VM 的 Azure Virtual Machine Manager (VMM) ID（若有）相同。 |
    | SubscriptionId |其中创建了 VM 的 Azure 订阅的 ID。 |
    | RoleName |正在恢复的 Azure VM 的名称。 |
    | CloudServiceName |以其名义创建了 VM 的 Azure 云服务名称。 |
    | ResourceGroupName|以其名义创建了 VM 的 Azure 资源组名称。 |
    | RecoveryPointId|VM 的恢复时间戳。 |

* 确保自动化帐户包含以下模块：
    * AzureRM.profile
    * AzureRM.Resources
    * AzureRM.Automation
    * AzureRM.Network
    * AzureRM.Compute

所有模块的版本都应兼容。 若要确保所有模块都兼容，只需使用所有模块的最新版本即可。

### <a name="access-all-vms-of-the-vmmap-in-a-loop"></a>循环访问 VMMap 中的所有 VM
运行以下代码，循环访问 Microsoft VMMap 中的所有 VM：

```
$VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
$vmMap = $RecoveryPlanContext.VmMap
 foreach($VMID in $VMinfo)
 {
     $VM = $vmMap.$VMID                
             if( !(($VM -eq $Null) -Or ($VM.ResourceGroupName -eq $Null) -Or ($VM.RoleName -eq $Null))) {
         #this check is to ensure that we skip when some data is not available else it will fail
 Write-output "Resource group name ", $VM.ResourceGroupName
 Write-output "Rolename " = $VM.RoleName
     }
 }

```

> [!NOTE]
> 如果脚本是用于启动组的前操作，那么资源组名称和角色名称值为空。 只有当此组的 VM 成功进行故障转移后，才会填充这些值。 脚本是用于启动组的后操作。

## <a name="use-the-same-automation-runbook-in-multiple-recovery-plans"></a>在多个恢复计划中使用相同的自动化 Runbook

可以使用外部变量，在多个恢复计划中使用同一个脚本。 可以使用 [Azure 自动化变量](../automation/automation-variables.md)，存储可在执行恢复计划时传递的参数。 通过将恢复计划名称作为前缀添加到变量，可以为每个恢复计划单独创建变量。 然后，将变量用作参数。 可以在不更改脚本的情况下更改参数，但仍会改变脚本的工作方式。

### <a name="use-a-simple-string-variable-in-a-runbook-script"></a>在 Runbook 脚本中使用简单字符串变量

在以下示例中，脚本使用网络安全组 (NSG) 的输入，并将其应用于恢复计划的 VM。

为了让脚本能够检测出正在运行的恢复计划，可以使用恢复计划上下文：

```
workflow AddPublicIPAndNSG {
    param (
          [parameter(Mandatory=$false)]
          [Object]$RecoveryPlanContext
    )

    $RPName = $RecoveryPlanContext.RecoveryPlanName
```

若要应用现有 NSG，必须知道 NSG 名称和 NSG 资源组名称。 将这些变量用作恢复计划脚本的输入。 为此，请在自动化帐户资产中创建两个变量。 添加要为其创建参数的恢复计划的名称，作为变量名称的前缀。

1. 创建一个用于存储 NSG 名称的变量。 使用恢复计划的名称，将前缀添加到变量名称中。

    ![创建 NSG 名称变量](media/site-recovery-runbook-automation-new/var1.png)

2. 创建用于存储 NSG 资源组名称的变量。 使用恢复计划的名称，将前缀添加到变量名称中。

    ![创建 NSG 资源组名称](media/site-recovery-runbook-automation-new/var2.png)


3.  在脚本中，使用以下参考代码获取变量值：

    ```
    $NSGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSG"
    $NSGRGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSGRG"

    $NSGnameVar = Get-AutomationVariable -Name $NSGValue
    $RGnameVar = Get-AutomationVariable -Name $NSGRGValue
    ```

4.  在 Runbook 中使用这些变量，将 NSG 应用到已故障转移的 VM 的网络接口：

    ```
    InlineScript {
    if (($Using:NSGname -ne $Null) -And ($Using:NSGRGname -ne $Null)) {
            $NSG = Get-AzureRmNetworkSecurityGroup -Name $Using:NSGname -ResourceGroupName $Using:NSGRGname
            Write-output $NSG.Id
            #Apply the NSG to a network interface
            #$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
            #Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd `
            #  -AddressPrefix 192.168.1.0/24 -NetworkSecurityGroup $NSG
        }
    }
    ```

对于每个恢复计划，请创建独立变量，以便能够重用脚本。 使用恢复计划名称添加前缀。 有关此方案的端到端完整脚本，请参阅[在 Site Recovery 恢复计划的测试故障转移期间向 VM 添加公共 IP 和 NSG](https://gallery.technet.microsoft.com/Add-Public-IP-and-NSG-to-a6bb8fee)。


### <a name="use-a-complex-variable-to-store-more-information"></a>使用复杂变量存储更多信息

假设情况为，需要使用一个脚本在特定 VM 上启用公共 IP。 在另一种情况下，需要在不同的 VM 上（而不是在所有 VM 上）应用其他 NSG。 可以创建可对任何恢复计划重用的脚本。 每个恢复计划包含的 VM 数量不定。 例如，SharePoint 恢复有两个前端。 基本业务线 (LOB) 应用程序只有一个前端。 无法为每个恢复计划单独创建变量。

在下面的示例中，我们采用一种新方法，在 Azure 自动化帐户资产中创建[复杂变量](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureautomationvariable)。 为此，可以指定多个值。 必须使用 Azure PowerShell 完成以下步骤：

1. 在 PowerShell 中，登录 Azure 订阅：

    ```
    Connect-AzureRmAccount
    $sub = Get-AzureRmSubscription -Name <SubscriptionName>
    $sub | Select-AzureRmSubscription
    ```

2. 若要存储参数，请使用恢复计划名称创建复杂变量：

    ```
    $VMDetails = @{"VMGUID"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"};"VMGUID2"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"}}
        New-AzureRmAutomationVariable -ResourceGroupName <RG of Automation Account> -AutomationAccountName <AA Name> -Name <RecoveryPlanName> -Value $VMDetails -Encrypted $false
    ```

3. 在此复杂变量中，VMDetails 是受保护 VM 的 VM ID。 若要获取 VM ID，请在 Azure 门户中查看 VM 属性。 下面的屏幕截图展示了一个存储两个 VM 详细信息的变量：

    ![将 VM ID 用作 GUID](media/site-recovery-runbook-automation-new/vmguid.png)

4. 在 Runbook 中使用此变量。 如果在恢复计划上下文中找到了指定的 VM GUID，请在 VM 上应用 NSG：

    ```
    $VMDetailsObj = Get-AutomationVariable -Name $RecoveryPlanContext.RecoveryPlanName
    ```

4. 在 Runbook 中，循环访问恢复计划上下文的 VM。 检查 $VMDetailsObj 中是否有 VM。 如果有，请访问变量属性，以应用 NSG：

    ```
        $VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
        $vmMap = $RecoveryPlanContext.VmMap

        foreach($VMID in $VMinfo) {
            Write-output $VMDetailsObj.value.$VMID

            if ($VMDetailsObj.value.$VMID -ne $Null) { #If the VM exists in the context, this will not b Null
                $VM = $vmMap.$VMID
                # Access the properties of the variable
                $NSGname = $VMDetailsObj.value.$VMID.'NSGName'
                $NSGRGname = $VMDetailsObj.value.$VMID.'NSGResourceGroupName'

                # Add code to apply the NSG properties to the VM
            }
        }
    ```

可以对不同的恢复计划使用同一脚本。 通过存储不同变量中与恢复计划对应的值，输入不同的参数。

## <a name="sample-scripts"></a>示例脚本

单击“部署到 Azure”按钮，将示例脚本部署到自动化帐户。

[![部署到 Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

有关其他示例，请观看下面的视频。 它演示了如何将两层 WordPress 应用程序恢复到 Azure：


> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]


## <a name="additional-resources"></a>其他资源
* [Azure 自动化服务运行方式帐户](../automation/automation-create-runas-account.md)
* [Azure 自动化概述](http://msdn.microsoft.com/library/azure/dn643629.aspx "Azure 自动化概述")
* [Azure 自动化示例脚本](http://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=User&f\[0\].Value=SC%20Automation%20Product%20Team&f\[0\].Text=SC%20Automation%20Product%20Team "Azure 自动化示例脚本")

## <a name="next-steps"></a>后续步骤
[详细了解](site-recovery-failover.md)如何运行故障转移。
