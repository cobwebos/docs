---
title: "Azure 自动化中的变量资产 | Microsoft Docs"
description: "变量资产是可供 Azure 自动化中的所有 Runbook 和 DSC 配置使用的值。  本文介绍了变量的详细信息，以及如何在文本和图形创作中使用变量。"
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: tysonn
ms.assetid: b880c15f-46f5-4881-8e98-e034cc5a66ec
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/14/2016
ms.author: magoedte;bwren
translationtype: Human Translation
ms.sourcegitcommit: 109ca4a4672d21969096af26a094390673de25d9
ms.openlocfilehash: 299b419c0271bbe7355a491cddf32dc922090621


---
# <a name="variable-assets-in-azure-automation"></a>Azure 自动化中的变量资产

变量资产是可供自动化帐户中的所有 Runbook 和 DSC 配置使用的值。 可以从 Azure 门户、Windows PowerShell 和 Runbook 或 DSC 配置中创建、修改和检索变量资产。 自动化变量可用于以下方案：

- 在多个 Runbook 或 DSC 配置之间共享某个值。

- 在同一 Runbook 或 DSC 配置中的多个作业之间共享某个值。

- 从门户或 Windows PowerShell 命令行管理由 Runbook 或 DSC 配置使用的值，例如一组常用配置项，如特定的 VM 名称列表、特定资源组、AD 域名等。  

自动化变量将保留，以便在 Runbook 或 DSC 配置失败时它们仍然继续可用。  这也允许一个 Runbook 设置的值随后由另一个 Runbook 使用，或由同一 Runbook 或 DSC 配置在下次运行时使用。

创建变量时，可以指定将其加密存储。  当变量加密后，它将安全地存储在 Azure 自动化中并且不能从 Azure PowerShell 模块随附的 [Get-AzureRmAutomationVariable](https://msdn.microsoft.com/library/mt603849.aspx) cmdlet 检索变量值。  可以检索加密值的唯一方法是从 Runbook 或 DSC 配置中的 **Get-AutomationVariable** 活动进行检索。

> [!NOTE]
> Azure 自动化中的安全资产包括凭据、证书、连接和加密的变量。 这些资产已使用针对每个自动化帐户生成的唯一密钥加密并存储在 Azure 自动化中。 此密钥由主证书加密，并存储在 Azure 自动化中。 在存储安全资产之前，会先使用主证书来解密自动化帐户的密钥，然后使用该密钥来加密资产。

## <a name="variable-types"></a>变量类型

当使用 Azure 门户创建变量时，必须通过下拉列表指定一个数据类型，以便门户可以显示用于输入变量值的相应控件。 该变量并不局限于此数据类型，但如果您想要指定不同类型的值，则必须使用 Windows PowerShell 设置该变量。 如果指定为“未定义”，则该变量的值将设置为 **$null**，并且必须使用 [Set-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913767.aspx) cmdlet 或 **Set-AutomationVariable** 活动来设置该值。  无法在该门户中创建或更改复杂变量类型的值，但您可以使用 Windows PowerShell 提供任何类型的值。 复杂类型将作为 [PSCustomObject](http://msdn.microsoft.com/library/system.management.automation.pscustomobject.aspx) 返回。

您可以通过创建一个数组或哈希表并将其保存到变量，来将多个值存储到单一变量。

以下列出自动化中的可用变量类型：

* String
* 整数
* DateTime
* 布尔
* Null

## <a name="cmdlets-and-workflow-activities"></a>Cmdlet 和工作流活动

下表中的 cmdlet 用于通过 Windows PowerShell 创建和管理自动化变量。 可在自动化 Runbook 和 DSC 配置中使用的 [Azure PowerShell 模块](/powershell/azureps-cmdlets-docs)已随附了这些 cmdlet。

|Cmdlet|说明|
|:---|:---|
|[Get-AzureRmAutomationVariable](https://msdn.microsoft.com/library/mt603849.aspx)|检索现有变量的值。|
|[New-AzureRmAutomationVariable](https://msdn.microsoft.com/library/mt603613.aspx)|创建新变量并设置变量值。|
|[Remove-AzureRmAutomationVariable](https://msdn.microsoft.com/library/mt619354.aspx)|删除现有变量。|
|[Set-AzureRmAutomationVariable](https://msdn.microsoft.com/library/mt603601.aspx)|设置现有变量的值。|

下表中的工作流活动用于在 Runbook 中访问自动化变量。 它们仅可在 Runbook 或 DSC 配置中使用，而不作为 Azure PowerShell 模块的一部分提供。

|工作流活动|说明|
|:---|:---|
|Get-AutomationVariable|检索现有变量的值。|
|Set-AutomationVariable|设置现有变量的值。|

> [!NOTE] 
> 应避免在 Runbook 或 DSC 配置中的 **Get-AutomationVariable** 的 –Name 参数中使用变量，因为这可能会使设计时发现 Runbook 或 DSC 配置与自动化变量之间的依赖关系变得复杂化。

## <a name="creating-a-new-automation-variable"></a>创建新的自动化变量

### <a name="to-create-a-new-variable-with-the-azure-portal"></a>使用 Azure 门户创建新变量

1. 在自动化帐户中，单击窗口顶部的“资产”。
1. 在窗口底部，单击“添加设置”。
1. 单击“添加变量”。
1. 完成向导并单击复选框以保存新变量。


### <a name="to-create-a-new-variable-with-the-azure-portal"></a>使用 Azure 门户创建新变量

1. 在自动化帐户中，单击“资产”部分以打开“资产”边栏选项卡。
1. 单击“变量”部分以打开“变量”边栏选项卡。
1. 单击边栏选项卡顶部的“添加变量”。
1. 完成表单，然后单击“创建”以保存新变量。


### <a name="to-create-a-new-variable-with-windows-powershell"></a>使用 Windows PowerShell 创建新变量

[New-AzureRmAutomationVariable](https://msdn.microsoft.com/library/mt603613.aspx) cmdlet 创建一个新的变量并设置其初始值。 可以使用 [Get-AzureRmAutomationVariable](https://msdn.microsoft.com/library/mt603849.aspx) 检索该值。 如果该值为简单类型，则返回相同的类型。 如果其为复杂类型，则返回 **PSCustomObject**。

下面的示例命令演示如何创建字符串类型的变量，然后返回其值。

    New-AzureRmAutomationVariable -ResourceGroupName "ResouceGroup01" 
    –AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
    –Encrypted $false –Value 'My String'
    $string = (Get-AzureRmAutomationVariable -ResourceGroupName "ResouceGroup01" `
    –AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value

下面的示例命令演示如何创建复杂类型的变量，然后返回其属性。 在这种情况下，会使用来自 **Get-AzureRmVm** 的虚拟机对象。

    $vm = Get-AzureRmVm -ResourceGroupName "ResourceGroup01" –Name "VM01"
    New-AzureRmAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm
    
    $vmValue = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
    $vmName = $vmValue.Name
    $vmIpAddress = $vmValue.IpAddress



## <a name="using-a-variable-in-a-runbook-or-dsc-configuration"></a>使用 Runbook 或 DSC 配置中的变量

使用 **Set-AutomationVariable** 活动设置 Runbook 或 DSC 配置中自动化变量的值，并使用 **Get-AutomationVariable** 来检索该值。  不应在 Runbook 或 DSC 配置中使用 **Set-AzureAutomationVariable** 或 **Get-AzureAutomationVariable** cmdlet，因为它们的效率低于工作流活动。  也不能使用 **Get-AzureAutomationVariable** 来检索安全变量的值。  从 Runbook 或 DSC 配置中创建新变量的唯一方法是使用 [New-AzureAutomationVariable](http://msdn.microsoft.com/library/dn913771.aspx) cmdlet。


### <a name="textual-runbook-samples"></a>文本 Runbook 示例

#### <a name="setting-and-retrieving-a-simple-value-from-a-variable"></a>设置和检索变量中的一个简单值

下面的示例命令演示如何设置和检索文本 Runbook 中的变量。 在此示例中，假定已经创建了名为 *NumberOfIterations* 和 *NumberOfRunnings* 的整数类型变量和名为 *SampleMessage* 的字符串类型变量。

    $NumberOfIterations = Get-AutomationVariable -Name 'NumberOfIterations'
    $NumberOfRunnings = Get-AutomationVariable -Name 'NumberOfRunnings'
    $SampleMessage = Get-AutomationVariable -Name 'SampleMessage'
    
    Write-Output "Runbook has been run $NumberOfRunnings times."
    
    for ($i = 1; $i -le $NumberOfIterations; $i++) {
       Write-Output "$i`: $SampleMessage"
    }
    Set-AutomationVariable –Name NumberOfRunnings –Value ($NumberOfRunnings += 1)

#### <a name="setting-and-retrieving-a-complex-object-in-a-variable"></a>设置和检索变量中的复杂对象

下面的示例代码演示如何更新文本 Runbook 中具有复杂值的变量。 在此示例中，使用 **Get-AzureVM** 检索到一个 Azure 虚拟机并将其保存到一个现有的自动化变量。  如[变量类型](#variable-types)中所述，该对象存储为一个 PSCustomObject。

    $vm = Get-AzureVM -ServiceName "MyVM" -Name "MyVM"
    Set-AutomationVariable -Name "MyComplexVariable" -Value $vm


在下面的代码中，从该变量检索值并将其用于启动虚拟机。

    $vmObject = Get-AutomationVariable -Name "MyComplexVariable"
    if ($vmObject.PowerState -eq 'Stopped') {
       Start-AzureVM -ServiceName $vmObject.ServiceName -Name $vmObject.Name
    }


#### <a name="setting-and-retrieving-a-collection-in-a-variable"></a>设置和检索变量中的集合

下面的示例代码演示如何使用文本 Runbook 中包含一组复杂值的变量。 在此示例中，使用 **Get-AzureVM** 检索到多个 Azure 虚拟机并将它们保存到一个现有的自动化变量。  如[变量类型](#variable-types)中所述，变量存储为一组 PSCustomObject。

    $vms = Get-AzureVM | Where -FilterScript {$_.Name -match "my"}     
    Set-AutomationVariable -Name 'MyComplexVariable' -Value $vms

在下面的代码中，从该变量检索此集合并用于启动每个虚拟机。

    $vmValues = Get-AutomationVariable -Name "MyComplexVariable"
    ForEach ($vmValue in $vmValues)
    {
       if ($vmValue.PowerState -eq 'Stopped') {
          Start-AzureVM -ServiceName $vmValue.ServiceName -Name $vmValue.Name
       }
    }


### <a name="graphical-runbook-samples"></a>图形 Runbook 示例

在图形 Runbook 中，通过在图形编辑器的“库”窗格中右键单击变量并选择所需的活动来添加 **Get-AutomationVariable** 或 **Set-AutomationVariable**。

![将变量添加到画布](media/automation-variables/runbook-variable-add-canvas.png)

#### <a name="setting-values-in-a-variable"></a>设置变量中的值
下图显示了在图形 Runbook 中用于更新具有简单值的一个变量的示例活动。 在此示例中，使用 **Get-AzureRmVM** 检索到一个 Azure 虚拟机并将计算机名称保存到一个字符串类型的现有自动化变量。  [链接是管道还是序列](automation-graphical-authoring-intro.md#links-and-workflow)并不重要，因为我们仅预期输出中的单个对象。

![设置简单变量](media/automation-variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>后续步骤

* 若要了解有关在图形创作中将活动连接在一起的详细信息，请参阅[图形创作中的链接](automation-graphical-authoring-intro.md#links-and-workflow)
* 若要开始使用图形 Runbook，请参阅 [我的第一个图形 Runbook](automation-first-runbook-graphical.md) 




<!--HONumber=Dec16_HO2-->


