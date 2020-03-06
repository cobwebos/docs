---
title: Azure 自动化中的变量资产
description: 变量资产是可供 Azure 自动化中的所有 Runbook 和 DSC 配置使用的值。  本文介绍了变量的详细信息，以及如何在文本和图形创作中使用变量。
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 05/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4ce56b64502904308f45c74a5471447d93419452
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303046"
---
# <a name="variable-assets-in-azure-automation"></a>Azure 自动化中的变量资产

变量资产是可供自动化帐户中的所有 runbook 和 DSC 配置使用的值。 你可以从 "Azure 门户"、"PowerShell"、runbook 内或 DSC 配置中管理它们。

自动化变量可用于以下方案：

- 在多个 runbook 或 DSC 配置之间共享值。

- 在同一 runbook 或 DSC 配置中的多个作业之间共享值。

- 通过门户或 PowerShell 命令行管理 runbook 或 DSC 配置使用的值。 例如，一组常用配置项，例如 VM 名称的特定列表、特定资源组、AD 域名等。  

由于自动化变量是持久的，因此即使 runbook 或 DSC 配置失败，它们也可用。 此行为允许一个 runbook 或 DSC 配置设置一个值，该值随后由另一个 runbook 使用，或由同一 runbook 或 DSC 配置在下一次运行时使用。

创建变量时，可以将 Azure 自动化中的加密和存储指定为安全资产。 其他安全资产包括凭据、证书和连接。 Azure 自动化将加密这些资产，并使用为每个自动化帐户生成的唯一密钥来存储这些资产。 密钥存储在系统管理的 Key Vault 中。 在存储安全资产之前，Azure 自动化会从 Key Vault 加载密钥，然后使用该密钥来加密资产。 

Azure Automation 安全地存储每个加密变量。 不能使用 Azure PowerShell 模块随附的[AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0) cmdlet 检索其值。 检索加密值的唯一方法是使用 runbook 或 DSC 配置中的**get-automationvariable**活动。

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az module 安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于你的自动化帐户，可使用[如何在 Azure 自动化中更新 Azure PowerShell 模块](../automation-update-azure-modules.md)，将模块更新到最新版本。

## <a name="variable-types"></a>变量类型

使用 Azure 门户创建变量时，必须从下拉列表中指定数据类型，以便门户可以显示用于输入变量值的相应控件。 下面是 Azure Automation 中的可用变量类型：

* String
* 整数
* DateTime
* Boolean
* Null

此变量不限于指定的数据类型。 如果要指定不同类型的值，则必须使用 Windows PowerShell 设置该变量。 如果指定 "**未定义**"，则变量的值将设置为**null**，并且必须将值设置为[AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0) cmdlet 或**get-automationvariable**活动。

不能使用该门户来创建或更改复杂变量类型的值。 但是，可以使用 Windows PowerShell 提供任何类型的值。 复杂类型作为[PSCustomObject](/dotnet/api/system.management.automation.pscustomobject)检索。

可以通过创建一个数组或哈希表并将其保存到变量，来将多个值存储到单个变量中。

## <a name="powershell-cmdlets-that-create-and-manage-variable-assets"></a>用于创建和管理变量资产的 PowerShell cmdlet

对于 Az 模块，下表中的 cmdlet 用于通过 Windows PowerShell 创建和管理自动化变量资产。 它们作为[Az 模块](/powershell/azure/overview)的一部分提供，可在自动化 RUNBOOK 和 DSC 配置中使用。

| Cmdlet | 说明 |
|:---|:---|
|[AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0) | 检索现有变量的值。|
|[新-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationvariable?view=azps-3.5.0) | 创建新变量并设置变量值。|
|[AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationvariable?view=azps-3.5.0)| 删除现有变量。|
|[AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0)| 设置现有变量的值。|

## <a name="activities-to-access-variables"></a>用于访问变量的活动

下表中的活动用于在 runbook 和 DSC 配置中访问变量。 本文开头的加密变量介绍了**AzAutomationVariable**和**get-automationvariable**之间的区别。

| 活动 | 说明 |
|:---|:---|
|**Get-automationvariable**|检索现有变量的值。|
|**Get-automationvariable**|设置现有变量的值。|

> [!NOTE]
> 避免在 runbook 或 DSC 配置中使用**get-automationvariable**的*Name*参数中的变量。 使用此参数可以在设计时使 runbook 或 DSC 配置和自动化变量之间依赖项的发现复杂化。

下表中的函数用于在 Python2 Runbook 中访问和检索变量。

|Python2 函数|说明|
|:---|:---|
|automationassets.get_automation_variable|检索现有变量的值。 |
|automationassets.set_automation_variable|设置现有变量的值。 |

> [!NOTE]
> 必须将**automationassets**模块导入到 Python runbook 的顶部，才能访问资产功能。

## <a name="creating-a-new-automation-variable"></a>创建新的自动化变量

### <a name="create-a-new-variable-using-the-azure-portal"></a>使用 Azure 门户创建新变量

1. 在自动化帐户中，单击“资产”磁贴，然后在“资产”边栏选项卡中选择“变量”。
2. 在“变量”磁贴中，选择“添加变量”。
3. 完成 "**新建变量**" 边栏选项卡上的选项，然后单击 "**创建**" 以保存新变量。

>[!NOTE]
>如果要删除变量的加密，则必须删除该变量，并将其重新创建为未加密。

### <a name="create-a-new-variable-with-windows-powershell"></a>使用 Windows PowerShell 创建新变量

此脚本使用**AzAutomationVariable** cmdlet 创建新变量并设置其初始值。 然后，它可以使用**AzAutomationVariable**检索值。 如果该值为简单类型，则检索同一类型。 如果它是复杂类型，则检索**PSCustomObject**类型。

下面的示例演示如何创建字符串类型的变量，然后返回其值。

```powershell
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

下面的示例演示如何创建复杂类型的变量，然后检索其属性。 在这种情况下，将使用[new-azvm](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0)中的虚拟机对象。

```powershell
$vm = Get-AzVM -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

## <a name="using-a-variable-in-a-runbook-or-dsc-configuration"></a>使用 Runbook 或 DSC 配置中的变量

使用 Set-AutomationVariable 活动设置 PowerShell Runbook 或 DSC 配置中自动化变量的值，并使用 Get-AutomationVariable 来检索该值。 不应在 runbook 或 DSC 配置中使用**AzAutomationVariable**和**AzAutomationVariable** Cmdlet 或它们的 AzureRM 模块等效项，因为它们的效率低于工作流活动。 

请注意，不能使用**AzAutomationVariable**或其 AzureRM 模块等效项检索安全变量的值。 

从 runbook 或 DSC 配置中创建新变量的唯一方法是使用**AzAutomationVariable** cmdlet。

### <a name="textual-runbook-samples"></a>文本 Runbook 示例

#### <a name="set-and-retrieve-a-simple-value-from-a-variable"></a>设置和检索变量中的一个简单值

下面的示例命令演示如何设置和检索文本 Runbook 中的变量。 此示例假设创建名为*NumberOfIterations*和*NumberOfRunnings*的整数变量和一个名为*samplemessage.txt*的字符串变量。

```powershell
$NumberOfIterations = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfIterations'
$NumberOfRunnings = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfRunnings'
$SampleMessage = Get-AutomationVariable -Name 'SampleMessage'

Write-Output "Runbook has been run $NumberOfRunnings times."

for ($i = 1; $i -le $NumberOfIterations; $i++) {
    Write-Output "$i`: $SampleMessage"
}
Set-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" –Name NumberOfRunnings –Value ($NumberOfRunnings += 1)
```

#### <a name="set-and-retrieve-a-variable-in-a-python2-runbook"></a>在 Python2 runbook 中设置和检索变量

下面的示例演示如何使用变量，设置变量，并处理 Python2 runbook 中不存在的变量的异常。

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a variable
value = automationassets.get_automation_variable("test-variable")
print value

# set a variable (value can be int/bool/string)
automationassets.set_automation_variable("test-variable", True)
automationassets.set_automation_variable("test-variable", 4)
automationassets.set_automation_variable("test-variable", "test-string")

# handle a non-existent variable exception
try:
    value = automationassets.get_automation_variable("nonexisting variable")
except AutomationAssetNotFound:
    print "variable not found"
```

### <a name="graphical-runbook-samples"></a>图形 Runbook 示例

在图形 runbook 中，可以添加**get-automationvariable**或**get-automationvariable**活动。 只需在图形编辑器的 "库" 窗格中右键单击该变量，然后选择所需的活动即可。

![将变量添加到画布](../media/variables/runbook-variable-add-canvas.png)

#### <a name="set-values-in-a-variable"></a>设置变量中的值

下图显示了在图形 Runbook 中用于更新具有简单值的一个变量的示例活动。 在此示例中， **new-azvm**检索单个 Azure 虚拟机，并将计算机名称保存到类型为字符串的现有自动化变量。 [链接是管道还是序列](../automation-graphical-authoring-intro.md#links-and-workflow)并不重要，因为代码只需要输出中的单个对象。

![设置简单变量](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>后续步骤

- 若要详细了解如何以图形创作方式连接活动，请参阅[图形创作中的链接](../automation-graphical-authoring-intro.md#links-and-workflow)。
- 若要开始处理图形 runbook，请参阅[我的第一个图形 runbook](../automation-first-runbook-graphical.md)。
