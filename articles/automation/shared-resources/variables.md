---
title: 在 Azure 自动化中管理变量
description: 变量资产是可供 Azure 自动化中的所有 Runbook 和 DSC 配置使用的值。  本文介绍了变量的详细信息，以及如何在文本和图形创作中使用变量。
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 05/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: bf7840daad02f679cad4c3b798d2add02c863a15
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82651955"
---
# <a name="manage-variables-in-azure-automation"></a>在 Azure 自动化中管理变量

变量资产是可供自动化帐户中的所有 Runbook 和 DSC 配置使用的值。 可以在 Azure 门户、PowerShell、Runbook 内部或 DSC 配置中管理这些资产。

自动化变量可用于以下方案：

- 在多个 Runbook 或 DSC 配置之间共享某个值。

- 在同一 Runbook 或 DSC 配置中的多个作业之间共享某个值。

- 通过门户或 PowerShell 命令行管理 Runbook 或 DSC 配置使用的值。 例如一组常用配置项，包括特定的 VM 名称列表、特定资源组、AD 域名，等等。  

即使 runbook 或 DSC 配置失败，Azure 自动化仍会保留变量并使其可用。 此行为允许一个 Runbook 或 DSC 配置设置的值随后由另一个 Runbook 使用，或由同一 Runbook 或 DSC 配置在下次运行时使用。

Azure Automation 安全地存储每个加密变量。 创建变量时，可以将 Azure 自动化中的加密和存储指定为安全资产。 

>[!NOTE]
>Azure 自动化中的安全资产包括凭据、证书、连接和加密的变量。 这些资产使用为每个自动化帐户生成的唯一密钥加密并存储在 Azure 自动化中。 Azure 自动化将密钥存储在系统管理的 Key Vault 中。 在存储安全资产之前，自动化会从 Key Vault 加载密钥，然后使用该密钥来加密资产。 

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可参阅[如何更新 Azure 自动化中的 Azure PowerShell 模块](../automation-update-azure-modules.md)，将模块更新到最新版本。

## <a name="variable-types"></a>变量类型

使用 Azure 门户创建变量时，必须从下拉列表中指定数据类型，以便门户可以显示用于输入变量值的相应控件。 下面是 Azure Automation 中的可用变量类型：

* String
* 整数
* DateTime
* Boolean
* Null

不能将变量限制为指定的数据类型。 如果想要指定不同类型的值，则必须使用 Windows PowerShell 设置该变量。 如果指示`Not defined`，则将变量的值设置为 Null。 必须用[AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0) cmdlet 或内部`Set-AutomationVariable` cmdlet 设置该值。

不能使用 Azure 门户来创建或更改复杂变量类型的值。 但是，可以使用 Windows PowerShell 提供任何类型的值。 复杂类型作为[PSCustomObject](/dotnet/api/system.management.automation.pscustomobject)检索。

可以通过创建一个数组或哈希表并将其保存到变量，来将多个值存储到单一变量。

>[!NOTE]
>VM 名称变量最多可以有80个字符。 资源组变量最多可以有90个字符。 请参阅[Azure 资源的命名规则和限制](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-name-rules)。

## <a name="powershell-cmdlets-to-access-variables"></a>用于访问变量的 PowerShell cmdlet

下表中的 cmdlet 通过 PowerShell 创建和管理自动化变量。 它们作为[Az 模块](modules.md#az-modules)的一部分提供。

| Cmdlet | 说明 |
|:---|:---|
|[AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0) | 检索现有变量的值。 如果该值为简单类型，则检索同一类型。 如果它是复杂类型，则检索`PSCustomObject`类型。 <br>**注意：** 不能使用此 cmdlet 检索已加密变量的值。 执行此操作的唯一方法是在 runbook 或 DSC `Get-AutomationVariable`配置中使用内部 cmdlet。 请参阅[内部 cmdlet 来访问变量](#internal-cmdlets-to-access-variables)。 |
|[新-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationvariable?view=azps-3.5.0) | 创建新变量并设置变量值。|
|[AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationvariable?view=azps-3.5.0)| 删除现有变量。|
|[AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0)| 设置现有变量的值。 |

## <a name="internal-cmdlets-to-access-variables"></a>用于访问变量的内部 cmdlet

下表中的内部 cmdlet 用于访问 runbook 和 DSC 配置中的变量。 这些 cmdlet 与全局模块`Orchestrator.AssetManagement.Cmdlets`一起提供。 有关详细信息，请参阅[内部 cmdlet](modules.md#internal-cmdlets)。

| 内部 Cmdlet | 说明 |
|:---|:---|
|`Get-AutomationVariable`|检索现有变量的值。|
|`Set-AutomationVariable`|设置现有变量的值。|

> [!NOTE]
> 避免在 runbook 或 DSC `Name`配置中`Get-AutomationVariable`使用的参数中的变量。 使用变量可以在设计时使 runbook 和自动化变量之间依赖项的发现复杂化。

`Get-AutomationVariable`在 PowerShell 中不起作用，只在 runbook 或 DSC 配置中工作。 例如，若要查看加密变量的值，你可以创建一个 runbook 来获取该变量，然后将其写入到输出流：
 
```powershell
$mytestencryptvar = Get-AutomationVariable -Name TestVariable
Write-output "The encrypted value of the variable is: $mytestencryptvar"
```

## <a name="python-2-functions-to-access-variables"></a>Python 2 用于访问变量的函数

下表中的函数用于访问 Python 2 runbook 中的变量。

|Python 2 功能|说明|
|:---|:---|
|`automationassets.get_automation_variable`|检索现有变量的值。 |
|`automationassets.set_automation_variable`|设置现有变量的值。 |

> [!NOTE]
> 必须将`automationassets`模块导入 Python runbook 的顶部，才能访问资产功能。

## <a name="create-and-get-a-variable"></a>创建并获取变量

>[!NOTE]
>如果要删除变量的加密，则必须删除该变量，并将其重新创建为未加密。

### <a name="create-and-get-a-variable-using-the-azure-portal"></a>使用 Azure 门户创建并获取变量

1. 在自动化帐户中，单击 "**资产**" 磁贴，然后单击 "**资产**" 边栏选项卡，然后选择 "**变量**"。
2. 在“变量”**** 磁贴中，选择“添加变量”****。
3. 完成 "**新建变量**" 边栏选项卡上的选项，然后单击 "**创建**" 以保存新变量。

> [!NOTE]
> 保存加密的变量后，就不能在门户中查看它。 它只能更新。

### <a name="create-and-get-a-variable-in-windows-powershell"></a>在 Windows PowerShell 中创建并获取变量

Runbook 或 DSC 配置使用`New-AzAutomationVariable` cmdlet 创建新变量并设置其初始值。 如果对变量进行了加密，则调用应使用`Encrypted`参数。 你的脚本可以使用`Get-AzAutomationVariable`检索变量的值。 

>[!NOTE]
>PowerShell 脚本无法检索加密的值。 执行此操作的唯一方法是使用内部`Get-AutomationVariable` cmdlet。

下面的示例演示如何创建一个字符串变量，然后返回其值。

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

## <a name="textual-runbook-examples"></a>文本 runbook 示例

### <a name="retrieve-and-set-a-simple-value-from-a-variable"></a>从变量中检索和设置简单的值

下面的示例演示如何设置和检索文本 runbook 中的变量。 此示例假设创建了名为`NumberOfIterations`和`NumberOfRunnings`的整数变量和一个名为`SampleMessage`的字符串变量。

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

### <a name="retrieve-and-set-a-variable-in-a-python-2-runbook"></a>在 Python 2 runbook 中检索和设置变量

下面的示例演示如何获取变量、设置变量并处理 Python 2 runbook 中不存在的变量的异常。

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

## <a name="graphical-runbook-examples"></a>图形 runbook 示例

在图形 runbook 中，可以为内部 cmdlet `Get-AutomationVariable`或`Set-AutomationVariable`添加活动。 只需在图形编辑器的 "库" 窗格中右键单击每个变量，然后选择所需的活动即可。

![将变量添加到画布](../media/variables/runbook-variable-add-canvas.png)

下图显示了使用图形 runbook 中的简单值更新变量的示例活动。 在此示例中，的活动`Get-AzVM`将检索单个 Azure 虚拟机，并将计算机名称保存到现有的自动化字符串变量中。 [链接是管道还是序列](../automation-graphical-authoring-intro.md#links-and-workflow)并不重要，因为代码只需要输出中的单个对象。

![设置简单变量](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>后续步骤

* 若要了解有关用于访问变量的 cmdlet 的详细信息，请参阅[在 Azure Automation 中管理模块](modules.md)。
* 有关 runbook 的常规信息，请参阅[在 Azure 自动化中执行 Runbook](../automation-runbook-execution.md)。
* 有关 DSC 配置的详细信息，请参阅[状态配置概述](../automation-dsc-overview.md)。
