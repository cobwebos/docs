---
title: Azure 自动化中的凭据资产
description: Azure 自动化中的凭据资产包含可用于向 Runbook 或 DSC 配置访问的资源进行身份验证的安全凭据。 本文介绍如何创建凭据资产并在 Runbook 或 DSC 配置中使用它们。
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 05/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e9bf671ab6f14f95d5d707c3c3521b1d1c6e15ca
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34598926"
---
# <a name="credential-assets-in-azure-automation"></a>Azure 自动化中的凭据资产

自动化凭据资产包含一个对象，该对象包含用户名和密码等安全凭据。 Runbook 和 DSC 配置可能会使用在身份验证时接受 PSCredential 对象的 cmdlet，也可能会提取 PSCredential 对象的用户名和密码，以便提供给需要进行身份验证的某些应用程序或服务。 在 Azure 自动化中安全地存储凭据的属性，并可以在 Runbook 或 DSC 配置中通过 [Get-AutomationPSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential.aspx) 活动访问这些属性。

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../includes/gdpr-dsr-and-stp-note.md)]

> [!NOTE]
> Azure 自动化中的安全资产包括凭据、证书、连接和加密的变量。 这些资产已使用针对每个自动化帐户生成的唯一密钥加密并存储在 Azure 自动化中。 此密钥存储在密钥保管库中。 在存储安全资产之前，从密钥保管库加载密钥，然后使用该密钥加密资产。

## <a name="azure-classic-powershell-cmdlets"></a>Azure 经典 PowerShell cmdlet

下表中的 cmdlet 用于通过 Windows PowerShell 创建和管理自动化凭据资产。  可在自动化 Runbook 和 DSC 配置中使用的 [Azure PowerShell 模块](/powershell/azure/overview)已随附了这些 cmdlet。

| Cmdlet | 说明 |
|:--- |:--- |
| [Get-AzureAutomationCredential](/powershell/module/azure/get-azureautomationcredential?view=azuresmps-3.7.0) |检索有关凭据资产的信息。 只能从 **Get-AutomationPSCredential** 活动中检索凭据本身。 |
| [New-AzureAutomationCredential](/powershell/module/azure/new-azureautomationcredential?view=azuresmps-3.7.0) |创建新的自动化凭据。 |
| [Remove- AzureAutomationCredential](/powershell/module/azure/new-azureautomationcredential?view=azuresmps-3.7.0) |删除自动化凭据。 |
| [Set- AzureAutomationCredential](/powershell/module/azure/new-azureautomationcredential?view=azuresmps-3.7.0) |设置现有自动化凭据的属性。 |

## <a name="azurerm-powershell-cmdlets"></a>AzureRM PowerShell cmdlet

对于 AzureRM，下表中的 cmdlet 用于通过 Windows PowerShell 创建和管理自动化凭据资产。  可在自动化 Runbook 和 DSC 配置中使用的 [AzureRM.Automation 模块](/powershell/azure/overview)已随附了这些 cmdlet。

| Cmdlet | 说明 |
|:--- |:--- |
| [Get-AzureRmAutomationCredential](/powershell/module/azurerm.automation/get-azurermautomationcredential?view=azurermps-4.4.0) |检索有关凭据资产的信息。  |
| [New-AzureRmAutomationCredential](/powershell/module/azurerm.automation/new-azurermautomationcredential?view=azurermps-4.4.0) |创建新的自动化凭据。 |
| [Remove-AzureRmAutomationCredential](/powershell/module/azurerm.automation/remove-azurermautomationcredential?view=azurermps-4.4.0) |删除自动化凭据。 |
| [Set-AzureRmAutomationCredential](/powershell/module/azurerm.automation/set-azurermautomationcredential?view=azurermps-4.4.0) |设置现有自动化凭据的属性。 |

## <a name="activities"></a>活动

下表中的活动用于在 Runbook 和 DSC 配置中访问凭据。

| 活动 | 说明 |
|:--- |:--- |
| Get-AutomationPSCredential |在 Runbook 或 DSC 配置中获取要使用的凭据。 返回 [System.Management.Automation.PSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential) 对象。 |

> [!NOTE]
> 应避免在 Get-AutomationPSCredential 的 -Name 参数中使用变量，因为这可能会使设计时发现 Runbook 或 DSC 配置与凭据资产之间的依赖关系变得复杂化。

## <a name="python2-functions"></a>Python2 函数

下表中的函数用于在 Python2 Runbook 中访问凭据。

| 函数 | 说明 |
|:---|:---|
| automationassets.get_automation_credential | 检索有关凭据资产的信息。 |

> [!NOTE]
> 必须在 Python Runbook 顶部导入“automationassets”模块才能访问资产函数。

## <a name="creating-a-new-credential-asset"></a>创建新凭据资产

### <a name="to-create-a-new-credential-asset-with-the-azure-portal"></a>使用 Azure 门户新建凭据资产

1. 在自动化帐户中，单击“资产”部分以打开“资产”边栏选项卡。
2. 单击“凭据”部分以打开“凭据”边栏选项卡。
3. 单击边栏选项卡顶部的“添加凭据”。
4. 完成表单，并单击“创建”以保存新凭据。

### <a name="to-create-a-new-credential-asset-with-windows-powershell"></a>使用 Windows PowerShell 创建新的凭据资产

以下示例命令演示了如何创建新的自动化凭据。 首先创建了一个具有名称和密码的 PSCredential 对象，然后使用该对象创建凭据资产。 或者，可以使用 **Get-Credential** cmdlet，会提示键入名称和密码。

```azurepowershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>使用 PowerShell 凭据

在 Runbook 或 DSC 配置中使用 **Get-AutomationPSCredential** 活动检索凭据资产。 此操作将返回 [PSCredential 对象](http://msdn.microsoft.com/library/system.management.automation.pscredential.aspx)，可将其用于需要 PSCredential 参数的活动或 cmdlet。 还可以检索要单独使用的凭据对象的属性。 该对象具有一个用于用户名和安全密码的属性，或者可以使用 **GetNetworkCredential** 方法返回 [NetworkCredential](http://msdn.microsoft.com/library/system.net.networkcredential.aspx) 对象，该对象将提供该密码的不安全版本。

### <a name="textual-runbook-sample"></a>文本 Runbook 示例

下面的示例命令演示如何在 Runbook 中使用 PowerShell 凭据。 在此示例中，检索了凭据并将其用户名和密码分配到变量。

```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

### <a name="graphical-runbook-sample"></a>图形 Runbook 示例

通过在图形编辑器的“库”窗格中右键单击凭据并选择“添加到画布”，将 **Get-AutomationPSCredential** 活动添加到图形 Runbook。

![将凭据添加到画布](media/automation-credentials/credential-add-canvas.png)

下图显示了在图形 Runbook 中使用凭据的示例。  在这种情况下，它被该 Runbook 用来对 Azure 资源提供身份验证，如[使用 Azure AD 用户帐户进行 Runbook 身份验证](automation-create-aduser-account.md)中所述。  第一个活动检索有权访问 Azure 订阅的凭据。  然后，Add-AzureAccount 活动使用此凭据对它之后的任何活动提供身份验证。  此处是一个[管道链接](automation-graphical-authoring-intro.md#links-and-workflow)，因为 **Get-AutomationPSCredential** 要求是单个对象。  

![将凭据添加到画布](media/automation-credentials/get-credential.png)

## <a name="using-a-powershell-credential-in-dsc"></a>在 DSC 中使用 PowerShell 凭据

尽管 Azure 自动化中的 DSC 配置可以使用 **Get-AutomationPSCredential** 引用凭据资产，但如果需要，也可以通过参数传入凭据资产。 有关详细信息，请参阅[在 Azure 自动化 DSC 中编译配置](automation-dsc-compile.md#credential-assets)。

## <a name="using-credentials-in-python2"></a>在 Python2 中使用凭据

以下示例演示了如何在 Python2 Runbook 中访问凭据。

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>后续步骤

* 若要详细了解图形创作中的链接，请参阅[图形创作中的链接](automation-graphical-authoring-intro.md#links-and-workflow)
* 若要了解使用自动化的不同身份验证方法，请参阅 [Azure 自动化安全性](automation-security-overview.md)
* 若要开始使用图形 Runbook，请参阅 [我的第一个图形 Runbook](automation-first-runbook-graphical.md)
* 若要开始使用 PowerShell 工作流 Runbook，请参阅 [我的第一个 PowerShell 工作流 Runbook](automation-first-runbook-textual.md) 
* 若要开始使用 Python2 Runbook，请参阅[第一个 Python2 Runbook](automation-first-runbook-textual-python2.md) 

