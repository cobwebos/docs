---
title: 在 Azure 自动化中管理凭据
description: 本文介绍如何创建凭据资产并在 Runbook 或 DSC 配置中使用它们。
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 540ae25b22b2c134a47f91ad5b8b19089c7f2acb
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744998"
---
# <a name="manage-credentials-in-azure-automation"></a>在 Azure 自动化中管理凭据

自动化凭据资产包含一个对象，该对象包含用户名和密码等安全凭据。 Runbook 和 DSC 配置使用接受 [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) 对象的 cmdlet 进行身份验证。 或者，他们可以提取 `PSCredential` 对象的用户名和密码，以便提供给某些需要进行身份验证的应用程序或服务。 

>[!NOTE]
>Azure 自动化中的安全资产包括凭据、证书、连接和加密的变量。 这些资产已使用针对每个自动化帐户生成的唯一密钥进行加密并存储在 Azure 自动化中。 Azure 自动化将密钥存储在系统管理的 Key Vault 中。 在存储安全资产之前，自动化会从 Key Vault 加载密钥，然后使用该密钥加密资产。 

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="powershell-cmdlets-used-to-access-credentials"></a>用于访问凭据的 PowerShell cmdlet

下表中的 cmdlet 使用 PowerShell 创建和管理自动化凭据。 它们作为 [Az 模块](modules.md#az-modules)的一部分提供。

| Cmdlet | 说明 |
|:--- |:--- |
| [Get-AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential?view=azps-3.3.0) |检索包含有关凭据的元数据的 [CredentialInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.commands.automation.model.credentialinfo?view=azurerm-ps) 对象。 该 cmdlet 不检索 `PSCredential` 对象本身。  |
| [New-AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential?view=azps-3.3.0) |创建新的自动化凭据。 |
| [Remove-AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential?view=azps-3.3.0) |删除自动化凭据。 |
| [Set-AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential?view=azps-3.3.0) |设置现有自动化凭据的属性。 |

## <a name="other-cmdlets-used-to-access-credentials"></a>用于访问凭据的其他 cmdlet

下表中的 cmdlet 用于在 Runbook 和 DSC 配置中访问凭据。 

| Cmdlet | 说明 |
|:--- |:--- |
| `Get-AutomationPSCredential` |获取要在 Runbook 或 DSC 配置中使用的 `PSCredential` 对象。 大多数情况下，应使用此 [内部 cmdlet](modules.md#internal-cmdlets) 而不是 `Get-AzAutomationCredential` cmdlet，因为后者仅检索凭据信息。 此信息通常对传递到另一个 cmdlet 没有帮助。 |
| [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) |通过提示输入用户名和密码来获取凭据。 此 cmdlet 是默认 Microsoft.PowerShell.Security 模块的一部分。 请参阅[默认模块](modules.md#default-modules)。|
| [New-AzureAutomationCredential](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) | 创建凭据资产。 此 cmdlet 是默认 Azure 模块的一部分。 请参阅[默认模块](modules.md#default-modules)。|

若要在代码中检索 `PSCredential` 对象，必须导入 `Orchestrator.AssetManagement.Cmdlets` 模块。 有关详细信息，请参阅[在 Azure 自动化中管理模块](modules.md)。

```azurepowershell
Import-Module Orchestrator.AssetManagement.Cmdlets -ErrorAction SilentlyContinue
```

> [!NOTE]
> 应避免在 `Get-AutomationPSCredential` 的 `Name` 参数中使用变量。 使用变量在设计时可能会导致难以厘清 Runbook 或 DSC 配置与凭据资产之间的依赖关系。

## <a name="python-2-functions-that-access-credentials"></a>用于访问凭据的 Python 2 函数

下表中的函数用于在 Python 2 Runbook 中访问凭据。

| 函数 | 说明 |
|:---|:---|
| `automationassets.get_automation_credential` | 检索有关凭据资产的信息。 |

> [!NOTE]
> 在 Python Runbook 顶部导入 `automationassets` 模块以访问资产函数。

## <a name="create-a-new-credential-asset"></a>创建新的凭据资产

可以使用 Azure 门户或使用 Windows PowerShell 来创建新的凭据资产。

### <a name="create-a-new-credential-asset-with-the-azure-portal"></a>使用 Azure 门户创建新的凭据资产

1. 从自动化帐户中，选择“共享资源”下的“凭据”。
1. 选择“添加凭据”。
2. 在“新建凭据”窗格中，根据你的命名标准输入合适的凭据名称。 
3. 在“用户名”字段中键入你的访问 ID。 
4. 对于两个密码字段，请输入机密访问密钥。

    ![创建新凭据](../media/credentials/credential-create.png)

5. 如果选中了“多重身份验证”框，请将其取消选中。 
6. 单击“创建”以保存新的凭据资产。

> [!NOTE]
> Azure 自动化不支持使用多重身份验证的用户帐户。

### <a name="create-a-new-credential-asset-with-windows-powershell"></a>使用 Windows PowerShell 创建新的凭据资产

以下示例演示了如何创建新的自动化凭据资产。 首先创建了一个具有名称和密码的 `PSCredential` 对象，然后使用该对象创建凭据资产。 可以改为使用 `Get-Credential` cmdlet 来提示用户键入名称和密码。

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="get-a-credential-asset"></a>获取凭据资产

Runbook 或 DSC 配置使用内部 `Get-AutomationPSCredential` cmdlet 检索凭据资产。 此 cmdlet 会获取一个 `PSCredential` 对象，它可用于需要凭据的 cmdlet。 还可以检索要单独使用的凭据对象的属性。 该对象包含用于用户名和安全密码的属性。 

> [!NOTE]
> `Get-AzAutomationCredential` cmdlet 不检索可用于身份验证的 `PSCredential` 对象。 它仅提供有关凭据的信息。 如果需要在 Runbook 中使用凭据，则必须使用 `Get-AutomationPSCredential` 将它作为 `PSCredential` 对象进行检索。

或者，可以使用 [GetNetworkCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential.getnetworkcredential?view=pscore-6.2.0) 方法检索表示不安全的密码版本的 [NetworkCredential](/dotnet/api/system.net.networkcredential) 对象。

### <a name="textual-runbook-example"></a>文本 Runbook 示例

下面的示例演示如何在 Runbook 中使用 PowerShell 凭据。 它检索凭据并将其用户名和密码分配给变量。


```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

还可以使用凭据通过 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.3.0) 向 Azure 进行身份验证。 在大多数情况下，应使用[运行方式帐户](../manage-runas-account.md)并使用 [Get-AzAutomationConnection](../automation-connections.md) 检索连接。


```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzAccount -Credential $myPsCred
```

### <a name="graphical-runbook-example"></a>图形 Runbook 示例

可以通过在图形编辑器的“库”窗格中右键单击凭据并选择“添加到画布”，将内部 `Get-AutomationPSCredential` cmdlet 的活动添加到图形 Runbook。

![将凭据添加到画布](../media/credentials/credential-add-canvas.png)

下图显示了在图形 Runbook 中使用凭据的示例。 在本例中，凭据为 runbook 提供针对 Azure 资源的身份验证，如[在 Azure 自动化中使用 Azure AD 以便向 Azure 进行身份验证](../automation-use-azure-ad.md)中所述。 第一个活动检索有权访问 Azure 订阅的凭据。 然后，帐户连接活动使用此凭据为它之后的任何活动提供身份验证。 此处使用了一个[管道链接](../automation-graphical-authoring-intro.md#use-links-for-workflow)，因为 `Get-AutomationPSCredential` 需要单个对象。  

![将凭据添加到画布](../media/credentials/get-credential.png)

## <a name="use-credentials-in-a-dsc-configuration"></a>在 DSC 配置中使用凭据

虽然 Azure 自动化中的 DSC 配置可以使用 `Get-AutomationPSCredential` 处理凭据资产，但它们也可以通过参数传递凭据资产。 有关详细信息，请参阅[在 Azure 自动化 DSC 中编译配置](../automation-dsc-compile.md#credential-assets)。

## <a name="use-credentials-in-a-python-2-runbook"></a>在 Python 2 Runbook 中使用凭据

以下示例演示了如何在 Python 2 Runbook 中访问凭据。


```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>后续步骤

* 若要了解有关用于访问凭据的 cmdlet 的详细信息，请参阅[在 Azure 自动化中管理模块](modules.md)。
* 有关 Runbook 的常规信息，请参阅[在 Azure 自动化中执行 Runbook](../automation-runbook-execution.md)。
* 有关 DSC 配置的详细信息，请参阅 [Azure 自动化 State Configuration 概述](../automation-dsc-overview.md)。 
