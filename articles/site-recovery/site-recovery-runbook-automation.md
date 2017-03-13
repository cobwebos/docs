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
ms.date: 02/22/2017
ms.author: ruturajd@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: ac56273cf85aff550febecf0d75ec87d5c6dbbca
ms.openlocfilehash: 26547135548dde96e9da601f2e0ccfe96c626880
ms.lasthandoff: 02/23/2017


---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>将 Azure 自动化 Runbook 添加到恢复计划
本教程介绍 Azure Site Recovery 如何与 Azure 自动化集成以便为恢复计划提供可扩展性。 恢复计划可以协调使用 Azure Site Recovery 保护的虚拟机的恢复，以便复制到辅助云和 Azure 方案。 恢复计划还有助于实现恢复的**一致准确性**、**可重复性**和**自动化**。 如果你要将虚拟机故障转移到 Azure，则与 Azure 自动化集成可扩展恢复计划，并使你能够执行 Runbook，从而可以执行强大的自动化任务。

如果你还没有听说过 Azure 自动化，请单击[此处](https://azure.microsoft.com/services/automation/)进行注册，然后从[此处](https://azure.microsoft.com/documentation/scripts/)下载示例脚本。 若要详细了解 [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/)，以及如何使用恢复计划来安排 Azure 恢复，请单击[此处](https://azure.microsoft.com/blog/?p=166264)。

本教程介绍如何将 Azure 自动化 Runbook 集成到恢复计划。 我们将自动执行以前需要手动干预的简单任务，并了解如何将多步骤恢复转变成只需点击一下即可实现的恢复。

## <a name="customize-the-recovery-plan"></a>自定义恢复计划
1. 让我们从打开恢复计划的资源边栏选项卡开始。 你可以看到，恢复计划添加了两台虚拟机用于恢复。

    ![](media/site-recovery-runbook-automation-new/essentials-rp.PNG)
- - -
1. 单击自定义按钮，开始添加 Runbook。 

    ![](media/site-recovery-runbook-automation-new/customize-rp.PNG)


1. 右键单击开始组 1 并选择添加“添加后操作”。
2. 选择并在新的边栏选项卡中选择一个脚本。
3. 将脚本命名为“Hello World”。
4. 选择“自动化帐户”名称。 
    >[!NOTE]
    > 自动化帐户可以位于任意 Azure 地理位置，但必须与 Site Recovery 保管库采用相同的订阅。
    
5. 从自动化帐户中选择一个 runbook。 此 Runbook 在执行恢复第一组后的恢复计划期间运行。

    ![](media/site-recovery-runbook-automation-new/update-rp.PNG)
6. 选择“确定”保存脚本。 脚本随即添加到“组 1: 启动”的后操作。

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

                { "SubscriptionId":"7a1111111-c1d6-49c5-8c5d-111ce8dd183",
                
                "ResourceGroupName":"ContosoRG",
                
                "CloudServiceName":"pod02hrweb-Chicago-test",

                "RoleName":"Fabrikam-Hrweb-frontend-test",
                
                "RecoveryPointId":"TimeStamp"}

                }

        }


下表包含上下文中每个变量的名称和说明。

| **变量名称** | **说明** |
| --- | --- |
| RecoveryPlanName |正在运行的计划的名称。 此变量可帮助你根据名称重复使用脚本来执行不同的操作 |
| FailoverType |指定故障转移是测试、计划内还是计划外。 |
| FailoverDirection |指定恢复是恢复到主要站点还是辅助站点 |
| GroupID |标识计划运行时恢复计划内的组编号 |
| VmMap |组中所有虚拟机的阵列 |
| VMMap 键 |每个 VM 的唯一键 (GUID)。 与虚拟机的适用 VMM ID 相同。 |
| SubscriptionId |要在其中创建 VM 的 Azure 订阅 ID。 |
| RoleName |正在恢复的 Azure VM 的名称 |
| CloudServiceName |要在其下创建虚拟机的 Azure 云服务名称。 |
| ResourceGroupName|在其下创建虚拟机的 Azure 资源组名称。 |
| RecoveryPointId|要将 VM 恢复到的时间戳。 |

还需要确保在自动化帐户中添加以下模块。 所有模块应使用兼容的版本。 为了轻松做到一点，请确保所有模块使用最新可用版本。
* AzureRM.profile
* AzureRM.Resources
* AzureRM.Automation
* AzureRM.Network
* AzureRM.Compute


### <a name="accessing-all-vms-of-the-vmmap-in-a-loop"></a>循环访问 VmMap 中的所有 VM
使用以下代码片段循环访问 VmMap 中的所有 VM。

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
> 如果脚本是启动组的前操作，“资源组名称”和“角色名称”值为空。 仅当该组中的 VM 成功完成故障转移并且该脚本是启动组的后操作时，才填充这些值。

## <a name="using-the-same-automation-runbook-with-multiple-recovery-plans"></a>结合多个恢复计划使用相同的自动化 Runbook

可以使用外部变量跨多个恢复计划使用单个脚本。 可以使用 [Azure 自动化变量](../automation/automation-variables.md)来存储可在执行恢复计划时传递的参数。 通过使用恢复计划的名称预先修复变量，可为每个恢复计划创建各个变量并将其用作参数。 可以在不更改脚本的情况下更改参数，使脚本以不同的方式工作。

### <a name="using-simple-string-variables-in-runbook-script"></a>在 Runbook 脚本中使用简单字符串变量

考虑在脚本中使用 NSG 的输入，并将其应用到恢复计划的 VM。

为了让脚本知道要执行哪个恢复计划，可以使用恢复计划上下文。

```
    workflow AddPublicIPAndNSG {
        param (
              [parameter(Mandatory=$false)]
              [Object]$RecoveryPlanContext
        )

        $RPName = $RecoveryPlanContext.RecoveryPlanName
```

若要应用现有 NSG，需要提供 NSG 名称和 NSG 资源组。 我们将以输入的形式为恢复计划脚本提供这些变量。 为此，请在自动化帐户资产中创建两个变量，并使用要为其创建参数的恢复计划的名称作为这些变量的前缀。

1. 创建一个用于存储 NSG 名称的变量。 使用恢复计划的名称作为该变量的前缀。
    ![创建 NSG 名称变量](media/site-recovery-runbook-automation-new/var1.png)

2. 创建一个用于存储 NSG RG 名称的变量。 使用恢复计划的名称作为该变量的前缀。
    ![创建 NSG RG 名称](media/site-recovery-runbook-automation-new/var2.png)


在脚本中，使用以下引用代码获取变量的值：

```
    $NSGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSG"
    $NSGRGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSGRG"

    $NSGnameVar = Get-AutomationVariable -Name $NSGValue 
    $RGnameVar = Get-AutomationVariable -Name $NSGRGValue
```

接下来，可以在 Runbook 中使用这些变量，并将 NSG 应用到已故障转移的虚拟机的网络接口。

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

对于每个恢复计划，请创建独立的变量，以便可以重复使用脚本，同时请使用恢复计划名称作为变量的前缀。 [此处提供](https://gallery.technet.microsoft.com/Add-Public-IP-and-NSG-to-a6bb8fee)了本示例中的完整端到端脚本。


### <a name="using-complex-variable-to-store-more-information"></a>使用复杂变量来存储更多信息

假设你只想要使用一个脚本在特定的 VM 上启用公共 IP，或者要在不同的虚拟机（不是所有）上应用不同的 NSG。 此脚本应该可以针对其他任何恢复计划重复使用。 每个恢复计划可以包含可变数量的虚拟机（例如，SharePoint 恢复计划有两个前端，而简单的 LOB 应用程序只有一个前端）。 每个恢复计划创建不同的变量无法实现此结果。 在此情况下，我们可以采用一种新的方法，在 Azure 自动化帐户资产中通过指定多个值来创建[复杂变量](https://msdn.microsoft.com/library/dn913767.aspx?f=255&MSPPError=-2147217396)。 需要使用 Azure PowerShell 执行以下步骤。

1. 在 Azure PowerShell 中登录到你的订阅。

    ```
        login-azurermaccount
        $sub = Get-AzureRmSubscription -Name <SubscriptionName>
        $sub | Select-AzureRmSubscription
    ```

2. 若要存储参数，请使用与恢复计划相同的名称创建复杂变量。

    ```
        $VMDetails = @{"VMGUID"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"};"VMGUID2"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"}}
        New-AzureRmAutomationVariable -ResourceGroupName <RG of Automation Account> -AutomationAccountName <AA Name> -Name <RecoveryPlanName> -Value $VMDetails -Encrypted $false
    ```

    在此复杂变量中，**VMDetails* 是受保护虚拟机的 VM ID。 可在门户上的虚拟机属性中找到此值。 此处我们创建了一个变量来存储两个虚拟机的详细信息。

    ![用作 GUID 的 VM ID](media/site-recovery-runbook-automation-new/vmguid.png)

3. 在 Runbook 中使用此变量，如果在恢复计划上下文中找到任何给定的 VMGUID，则在虚拟机上应用 NSG。

    ```
        $VMDetailsObj = Get-AutomationVariable -Name $RecoveryPlanContext.RecoveryPlanName 
    ```

4. 在 Runbook 中循环访问恢复计划上下文的 VM，并检查 VM 是否也在**$VMDetailsObj** 中存在。 如果存在，请通过访问变量的属性来应用 NSG。
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

可以针对不同的恢复计划使用相同的脚本，并通过在不同的变量中存储对应于不同恢复计划的值，来提供不同的参数。

## <a name="sample-scripts"></a>示例脚本
有关可直接导入自动化帐户中的脚本存储库，请参阅 [Kristian Nese 的 OMS 脚本存储库](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/Solutions/asrautomation)

此处的脚本是 Azure Resource Manager 模板，将部署以下所有脚本

* NSG

NSG Runbook 向恢复计划中的每个 VM 分配公共 IP 地址，并将虚拟网络适配器附加到允许默认通信的网络安全组

* PublicIP

公共 IP runbook 向恢复计划中的每个 VM 分配公共 IP 地址。 对计算机和应用程序的访问将取决于每个来宾的防火墙设置

* CustomScript

CustomScript runbook 向恢复计划中的每个 VM 分配公共 IP 地址并安装自定义脚本扩展，此扩展将在部署模板期间拉取你引用的脚本

* NSGwithCustomScript

NSGwithCustomScript runbook 向恢复计划中的每个 VM 分配公共 IP 地址，使用扩展安装自定义脚本，并将虚拟网络适配器连接到允许进行远程访问的默认入站和出站通信的 NSG

## <a name="additional-resources"></a>其他资源
[Azure 自动化服务作为帐户运行](../automation/automation-sec-configure-azure-runas-account.md)

[Azure 自动化概述](http://msdn.microsoft.com/library/azure/dn643629.aspx "Azure 自动化概述")

[Azure 自动化脚本示例](http://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=User&f\[0\].Value=SC%20Automation%20Product%20Team&f\[0\].Text=SC%20Automation%20Product%20Team "Azure 自动化脚本示例")

