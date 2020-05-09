---
title: 在 Azure 自动化中管理凭据
description: Azure 自动化中的凭据资产包含可用于向 Runbook 或 DSC 配置访问的资源进行身份验证的安全凭据。 本文介绍如何创建凭据资产并在 Runbook 或 DSC 配置中使用它们。
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 16b92108bcb4e5185a1990b0ed8f1278bfe44921
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82652828"
---
# <a name="manage-credentials-in-azure-automation"></a>在 Azure 自动化中管理凭据

自动化凭据资产包含包含安全凭据的对象，例如用户名和密码。 Runbook 和 DSC 配置使用接受[PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0)对象的 cmdlet 进行身份验证。 或者，他们可以提取`PSCredential`对象的用户名和密码，以便提供给需要进行身份验证的某些应用程序或服务。 

>[!NOTE]
>Azure 自动化中的安全资产包括凭据、证书、连接和加密的变量。 这些资产使用为每个自动化帐户生成的唯一密钥加密并存储在 Azure 自动化中。 Azure 自动化将密钥存储在系统管理的 Key Vault 中。 在存储安全资产之前，自动化会从 Key Vault 加载密钥，然后使用该密钥来加密资产。 

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可参阅[如何更新 Azure 自动化中的 Azure PowerShell 模块](../automation-update-azure-modules.md)，将模块更新到最新版本。

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="powershell-cmdlets-used-to-access-credentials"></a>用于访问凭据的 PowerShell cmdlet

下表中的 cmdlet 通过 PowerShell 创建和管理自动化凭据。 它们作为[Az 模块](modules.md#az-modules)的一部分提供。

| Cmdlet | 说明 |
|:--- |:--- |
| [AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential?view=azps-3.3.0) |检索包含有关凭据的元数据的[CredentialInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.commands.automation.model.credentialinfo?view=azurerm-ps)对象。 Cmdlet 不检索`PSCredential`对象本身。  |
| [新-AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential?view=azps-3.3.0) |创建新的自动化凭据。 |
| [AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential?view=azps-3.3.0) |删除自动化凭据。 |
| [AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential?view=azps-3.3.0) |设置现有自动化凭据的属性。 |

## <a name="other-cmdlets-used-to-access-credentials"></a>用于访问凭据的其他 cmdlet

下表中的 cmdlet 用于在 runbook 和 DSC 配置中访问凭据。 

| Cmdlet | 说明 |
|:--- |:--- |
| `Get-AutomationPSCredential` |获取要`PSCredential`在 RUNBOOK 或 DSC 配置中使用的对象。 大多数情况下，应使用此[内部 cmdlet](modules.md#internal-cmdlets)而不是`Get-AzAutomationCredential` cmdlet，因为后者仅检索凭据信息。 此信息通常不会传递到另一个 cmdlet。 |
| [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) |获取具有用户名和密码提示的凭据。 此 cmdlet 是默认的 Microsoft PowerShell 模块的一部分。 请参阅[默认模块](modules.md#default-modules)。|
| [New-AzureAutomationCredential](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) | 创建凭据资产。 此 cmdlet 是默认 Azure 模块的一部分。 请参阅[默认模块](modules.md#default-modules)。|

若要`PSCredential`在代码中检索对象，则必须导`Orchestrator.AssetManagement.Cmdlets`入该模块。 有关详细信息，请参阅[在 Azure 自动化中管理模块](modules.md)。

```azurepowershell
Import-Module Orchestrator.AssetManagement.Cmdlets -ErrorAction SilentlyContinue
```

> [!NOTE]
> 应避免在的`Name`参数中使用变量。 `Get-AutomationPSCredential` 它们的使用可能会在设计时使 runbook 或 DSC 配置和凭据资产之间依赖项的发现复杂化。

## <a name="python-2-functions-that-access-credentials"></a>用于访问凭据的 Python 2 函数

下表中的函数用于在 Python 2 runbook 中访问凭据。

| 函数 | 说明 |
|:---|:---|
| `automationassets.get_automation_credential` | 检索有关凭据资产的信息。 |

> [!NOTE]
> 将`automationassets`模块导入到 Python runbook 的顶部以访问资产功能。

## <a name="create-a-new-credential-asset"></a>创建新的凭据资产

你可以使用 Azure 门户或使用 Windows PowerShell 来创建新的凭据资产。

### <a name="create-a-new-credential-asset-with-the-azure-portal"></a>使用 Azure 门户创建新的凭据资产

1. 在自动化帐户中，选择 "**共享资源**" 下的 "**凭据**"。
1. 选择“添加凭据”****。
2. 在 "新建凭据" 窗格中，根据命名标准输入适当的凭据名称。 
3. 在 "**用户名**" 字段中键入访问 ID。 
4. 对于这两个密码字段，请输入密钥访问密钥。

    ![创建新凭据](../media/credentials/credential-create.png)

5. 如果选中 "多重身份验证" 复选框，请将其取消选中。 
6. 单击 "**创建**" 以保存新的凭据资产。

> [!NOTE]
> Azure 自动化不支持使用多重身份验证的用户帐户。

### <a name="create-a-new-credential-asset-with-windows-powershell"></a>使用 Windows PowerShell 创建新的凭据资产

下面的示例演示如何创建新的自动化凭据资产。 首先`PSCredential`创建一个具有名称和密码的对象，然后使用该对象创建凭据资产。 相反，你可以使用`Get-Credential` cmdlet 来提示用户键入名称和密码。

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="get-a-credential-asset"></a>获取凭据资产

Runbook 或 DSC 配置使用内部`Get-AutomationPSCredential` cmdlet 检索凭据资产。 此 cmdlet 将获取`PSCredential`一个对象，该对象可用于需要凭据的 cmdlet。 还可以检索要单独使用的凭据对象的属性。 对象包含用户名和安全密码的属性。 

> [!NOTE]
> `Get-AzAutomationCredential` Cmdlet 不会检索可用于`PSCredential`身份验证的对象。 它仅提供有关凭据的信息。 如果需要在 runbook 中使用凭据，则必须使用`PSCredential` `Get-AutomationPSCredential`将其作为对象进行检索。

或者，你可以使用[GetNetworkCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential.getnetworkcredential?view=pscore-6.2.0)方法来检索[NetworkCredential](/dotnet/api/system.net.networkcredential)对象，该对象表示密码的不安全版本。

### <a name="textual-runbook-example"></a>文本 runbook 示例

下面的示例演示如何在 runbook 中使用 PowerShell 凭据。 它检索凭据并将其用户名和密码分配给变量。


```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

你还可以使用[AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.3.0)在 Azure 中使用凭据进行身份验证。 在大多数情况下，应使用[运行方式帐户](../manage-runas-account.md)，并使用[AzAutomationConnection](../automation-connections.md)检索连接。


```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzAccount -Credential $myPsCred
```

### <a name="graphical-runbook-example"></a>图形 runbook 示例

可以通过在图形编辑器的 "库`Get-AutomationPSCredential` " 窗格中右键单击凭据，然后选择 "**添加到画布**"，将内部 cmdlet 的活动添加到图形 runbook。

![将凭据添加到画布](../media/credentials/credential-add-canvas.png)

下图显示了在图形 Runbook 中使用凭据的示例。 在这种情况下，凭据将为 runbook 提供对 Azure 资源的身份验证，如[使用 Azure 自动化中的 Azure AD 对 azure 进行身份验证](../automation-use-azure-ad.md)中所述。 第一个活动检索有权访问 Azure 订阅的凭据。 然后，"帐户连接" 活动将使用此凭据为其后的任何活动提供身份验证。 此处使用[管道链接](../automation-graphical-authoring-intro.md#links-and-workflow)，因为`Get-AutomationPSCredential`需要单个对象。  

![将凭据添加到画布](../media/credentials/get-credential.png)

## <a name="use-credentials-in-a-dsc-configuration"></a>在 DSC 配置中使用凭据

尽管 Azure 自动化中的 DSC 配置可以使用`Get-AutomationPSCredential`凭据资产，但他们也可以通过参数传递凭据资产。 有关详细信息，请参阅[在 Azure 自动化 DSC 中编译配置](../automation-dsc-compile.md#credential-assets)。

## <a name="use-credentials-in-a-python-2-runbook"></a>在 Python 2 runbook 中使用凭据

下面的示例演示了在 Python 2 runbook 中访问凭据的示例。


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
* 有关 runbook 的常规信息，请参阅[在 Azure 自动化中执行 Runbook](../automation-runbook-execution.md)。
* 有关 DSC 配置的详细信息，请参阅[状态配置概述](../automation-dsc-overview.md)。