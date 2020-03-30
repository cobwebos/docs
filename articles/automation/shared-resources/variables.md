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
ms.openlocfilehash: d4a4a92feb3e1b400c0f40076148f7898c4bdef1
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365826"
---
# <a name="variable-assets-in-azure-automation"></a>Azure 自动化中的变量资产

可变资产是可用于自动化帐户中的所有 Runbook 和 DSC 配置的值。 可以从 Azure 门户、PowerShell、Runbook 或 DSC 配置中管理它们。

自动化变量可用于以下方案：

- 在多个 Runbook 或 DSC 配置之间共享值。

- 在同一 Runbook 或 DSC 配置中的多个作业之间共享值。

- 从门户或 PowerShell 命令行管理 Runbook 或 DSC 配置使用的值。 例如一组常见的配置项，例如 VM 名称的特定列表、特定资源组、AD 域名等。  

Azure 自动化会保留变量，即使 Runbook 或 DSC 配置失败，它们也能可用。 此行为允许一个 Runbook 或 DSC 配置设置一个值，然后由另一个 Runbook 或下次运行时由同一 Runbook 或 DSC 配置使用。

Azure 自动化安全地存储每个加密变量。 创建变量时，可以将 Azure 自动化的加密和存储指定为安全资产。 其他安全资产包括凭据、证书和连接。 Azure 自动化对这些资产进行加密，并使用为每个自动化帐户生成的唯一密钥存储它们。 密钥存储在系统管理的关键保管库中。 在存储安全资产之前，Azure 自动化会从密钥保管库加载密钥，然后使用它对资产进行加密。 

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可以使用["如何更新 Azure 自动化 中的 Azure PowerShell"模块](../automation-update-azure-modules.md)将模块更新到最新版本。

## <a name="variable-types"></a>变量类型

使用 Azure 门户创建变量时，必须从下拉列表中指定数据类型，以便门户可以显示用于输入变量值的适当控件。 以下是 Azure 自动化中可用的变量类型：

* String
* Integer
* DateTime
* Boolean
* Null

该变量不局限于指定的数据类型。 如果想要指定不同类型的值，则必须使用 Windows PowerShell 设置该变量。 如果指示`Not defined`，变量的值设置为 Null，则必须使用[Set-AzAutomationvariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0) cmdlet 或`Set-AutomationVariable`活动设置该值。

不能使用 Azure 门户创建或更改复杂变量类型的值。 但是，您可以使用 Windows PowerShell 提供任何类型的值。 复杂类型作为[PSCustomObject](/dotnet/api/system.management.automation.pscustomobject)检索。

可以通过创建一个数组或哈希表并将其保存到变量，来将多个值存储到单一变量。

## <a name="powershell-cmdlets-that-create-and-manage-variable-assets"></a>创建和管理可变资产的 PowerShell cmdlet

对于 Az 模块，下表中的 cmdlet 用于使用 Windows PowerShell 创建和管理自动化变量资产。 它们作为[Az.自动化模块](/powershell/azure/overview)的一部分发货，可用于自动化手册和 DSC 配置。

| Cmdlet | 描述 |
|:---|:---|
|[获取-阿兹自动化变量](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0) | 检索现有变量的值。 不能使用此 cmdlet 检索加密变量的值。 执行此操作的唯一方法是在 Runbook 或`Get-AutomationVariable`DSC 配置中使用活动。 |
|[新-阿兹自动化变量](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationvariable?view=azps-3.5.0) | 创建新变量并设置变量值。|
|[删除-阿兹自动化变量](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationvariable?view=azps-3.5.0)| 删除现有变量。|
|[设置-Az自动化变量](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0)| 设置现有变量的值。 |

## <a name="activities-to-access-variables-in-runbooks-and-dsc-configurations"></a>访问 Runbook 和 DSC 配置中的变量的活动

下表中的活动用于访问 Runbook 和 DSC 配置中的变量。 这些活动的 cmdlet 随全局模块`Orchestrator.AssetManagement.Cmdlets`一起提供。

| 活动 | 描述 |
|:---|:---|
|`Get-AutomationVariable`|检索现有变量的值。|
|`Set-AutomationVariable`|设置现有变量的值。|

> [!NOTE]
> 避免在`Name`runbook 或`Get-AutomationVariable`DSC 配置中使用 参数中的变量。 使用此参数会使在设计时 Runbook 或 DSC 配置和自动化变量之间的依赖项发现复杂化。

请注意，`Get-AutomationVariable`在 PowerShell 中不起作用，但仅在 Runbook 或 DSC 配置中工作。 例如，要查看加密变量的值，您可以创建 Runbook 来获取该变量，然后将其写入输出流：
 
```powershell
$mytestencryptvar = Get-AutomationVariable -Name TestVariable
Write-output "The encrypted value of the variable is: $mytestencryptvar"
```

## <a name="functions-to-access-variables-in-python-2-runbooks"></a>访问 Python 2 Runbook 中的变量的函数

下表中的函数用于访问 Python 2 Runbook 中的变量。

|Python 2 功能|描述|
|:---|:---|
|`automationassets.get_automation_variable`|检索现有变量的值。 |
|`automationassets.set_automation_variable`|设置现有变量的值。 |

> [!NOTE]
> 您必须在`automationassets`Python runbook 顶部导入模块才能访问资产函数。

## <a name="working-with-automation-variables"></a>使用自动化变量

>[!NOTE]
>如果要删除变量的加密，则必须删除该变量并将其重新创建为未加密的变量。

### <a name="create-a-new-variable-using-the-azure-portal"></a>使用 Azure 门户创建新变量

1. 从自动化帐户中，单击 **"资产**"磁贴，然后单击 **"资产**"边栏选项卡，然后选择 **"变量**"。
2. 在“变量”**** 磁贴中，选择“添加变量”****。
3. 完成 **"新建变量"** 边栏选项卡上的选项，然后单击"**创建"** 以保存新变量。

> [!NOTE]
> 保存加密变量后，无法在门户中查看该变量。 它只能更新。

### <a name="create-and-use-a-variable-in-windows-powershell"></a>在 Windows PowerShell 中创建和使用变量

PowerShell 脚本使用`New-AzAutomationVariable`cmdlet 或其 AzureRM 模块等效项创建新变量并设置其初始值。 如果变量已加密，则调用应使用 参数`Encrypted`。

该脚本使用[Get-AzAutomationvariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0)检索变量的值。 如果值是一个简单的类型，cmdlet 将检索同一类型。 如果是复杂类型，`PSCustomObject`则检索类型。

>[!NOTE]
>PowerShell 脚本无法检索加密的值。 执行此操作的唯一`Get-AutomationVariable`方法是在 Runbook 或 DSC 配置中使用活动。

下面的示例演示如何创建 String 类型的变量，然后返回其值。

```powershell
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

下面的示例演示如何创建具有复杂类型的变量，然后检索其属性。 在这种情况下，将使用[Get-AzVM 中的](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0)虚拟机对象。

```powershell
$vm = Get-AzVM -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

### <a name="create-and-use-a-variable-in-a-runbook-or-dsc-configuration"></a>在 Runbook 或 DSC 配置中创建和使用变量

在 Runbook 或 DSC 配置中创建新变量的唯一方法是使用`New-AzAutomationVariable`cmdlet 或其 AzureRM 模块等效。 脚本使用此 cmdlet 设置变量的初始值。 然后，脚本可以使用 检索值`Get-AzAutomationVariable`。 如果值是一个简单的类型，则检索相同的类型。 如果是复杂类型，则检索`PSCustomObject`类型。

>[!NOTE]
>检索加密值的唯一方法是使用 Runbook 或`Get-AutomationVariable`DSC 配置中的活动。 

### <a name="textual-runbook-samples"></a>文本 Runbook 示例

#### <a name="set-and-retrieve-a-simple-value-from-a-variable"></a>从变量设置和检索简单值

下面的示例命令演示如何设置和检索文本 Runbook 中的变量。 此示例假定创建名为`NumberOfIterations`和 的整数变量和`NumberOfRunnings`名为 的`SampleMessage`字符串变量。

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

#### <a name="set-and-retrieve-a-variable-in-a-python-2-runbook"></a>在 Python 2 运行簿中设置和检索变量

下面的示例演示如何使用变量、设置变量和处理 Python 2 Runbook 中不存在的变量的异常。

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

在图形运行簿中，可以添加`Get-AutomationVariable`或`Set-AutomationVariable`活动。 只需单击图形编辑器的"库"窗格中的变量，然后选择所需的活动。

![将变量添加到画布](../media/variables/runbook-variable-add-canvas.png)

#### <a name="set-values-in-a-variable"></a>在变量中设置值

下图显示了在图形 Runbook 中用于更新具有简单值的一个变量的示例活动。 在此示例中，`Get-AzVM`检索单个 Azure 虚拟机并将计算机名称保存到现有的自动化字符串变量。 [链接是管道还是序列](../automation-graphical-authoring-intro.md#links-and-workflow)并不重要，因为代码只期望输出中的单个对象。

![设置简单变量](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>后续步骤

- 要了解有关在图形创作中连接活动有关，请参阅[图形创作中的链接](../automation-graphical-authoring-intro.md#links-and-workflow)。
- 要开始使用图形运行簿，请参阅[我的第一个图形运行簿](../automation-first-runbook-graphical.md)。
