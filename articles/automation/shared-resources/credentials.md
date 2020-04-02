---
title: Azure 自动化中的凭据资产
description: Azure 自动化中的凭据资产包含可用于向 Runbook 或 DSC 配置访问的资源进行身份验证的安全凭据。 本文介绍如何创建凭据资产并在 Runbook 或 DSC 配置中使用它们。
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c8b63a2676690004d23094b490fea0ef150ab9cb
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546410"
---
# <a name="credential-assets-in-azure-automation"></a>Azure 自动化中的凭据资产

自动化凭据资产包含包含安全凭据（如用户名和密码）的对象。 Runbook 和 DSC 配置使用接受[PS凭据](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0)对象的 cmdlet 进行身份验证。 或者，他们可以提取`PSCredential`对象的用户名和密码，以提供给需要身份验证的某些应用程序或服务。 

Azure 自动化安全地存储凭据的属性。 通过 Runbook 或 DSC 配置访问属性使用[获取自动化PS凭据](#activities-used-to-access-credentials)活动。

> [!NOTE]
> Azure 自动化中的安全资产包括凭据、证书、连接和加密的变量。 这些资产使用为每个自动化帐户生成的唯一密钥在 Azure 自动化中加密和存储。 此密钥存储在密钥保管库中。 在存储安全资产之前，从密钥保管库加载密钥，然后使用该密钥加密资产。

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="azure-powershell-az-cmdlets-used-for-credential-assets"></a>用于凭据资产的 Azure PowerShell Az cmdlet

作为 Azure PowerShell Az 模块的一部分，下表中的 cmdlet 用于使用 Windows PowerShell 创建和管理自动化凭据资产。 它们在[Az.自动化模块](/powershell/azure/new-azureps-module-az?view=azps-1.1.0)中发货，可用于自动化手册和 DSC 配置。 请参阅[Azure 自动化 中的 Az 模块支持](https://docs.microsoft.com/azure/automation/az-modules)。

| Cmdlet | 说明 |
|:--- |:--- |
| [获取阿兹自动化证书](/powershell/module/az.automation/get-azautomationcredential?view=azps-3.3.0) |检索有关凭据资产的信息。 此 cmdlet 不会返回对象`PSCredential`。  |
| [新自动化证书](/powershell/module/az.automation/new-azautomationcredential?view=azps-3.3.0) |创建新的自动化凭据。 |
| [删除-阿兹自动化凭据](/powershell/module/az.automation/remove-azautomationcredential?view=azps-3.3.0) |删除自动化凭据。 |
| [设置-阿兹自动化证书](/powershell/module/az.automation/set-azautomationcredential?view=azps-3.3.0) |设置现有自动化凭据的属性。 |

## <a name="activities-used-to-access-credentials"></a>用于访问凭据的活动

下表中的活动用于访问 Runbook 和 DSC 配置中的凭据。

| 活动 | 说明 |
|:--- |:--- |
| `Get-AutomationPSCredential` |在 Runbook 或 DSC 配置中获取要使用的凭据。 凭据以对象的形式出现`PSCredential`。 |
| [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) |获取具有用户名和密码提示的凭据。 |
| [New-AzureAutomationCredential](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) | 创建凭据资产。 |

对于使用 Azure 自动化创作工具包的本地开发，cmdlet`Get-AutomationPSCredential`是程序集 Azure[自动化创作工具包的](https://www.powershellgallery.com/packages/AzureAutomationAuthoringToolkit/0.2.3.9)一部分。 对于使用自动化上下文的 Azure，cmdlet 位于`Orchestrator.AssetManagement.Cmdlets`中。 请参阅[在 Azure 自动化中管理模块](modules.md)。

若要检索`PSCredential`代码中的对象，可以[安装 PowerShell ISE 的 Microsoft Azure 自动化 ISE 加载项](https://github.com/azureautomation/azure-automation-ise-addon)。

```azurepowershell
Install-Module AzureAutomationAuthoringToolkit -Scope CurrentUser -Force
```

脚本还可以根据需要导入所需的模块，如以下示例所示： 

```azurepowershell
Import-Module Orchestrator.AssetManagement.Cmdlets -ErrorAction SilentlyContinue
```

> [!NOTE]
> 应避免在`Name`的`Get-AutomationPSCredential`参数中使用变量。 它们的使用会使在设计时 Runbook 或 DSC 配置和凭据资产之间的依赖项发现复杂化。

## <a name="python2-functions-that-access-credentials"></a>访问凭据的 Python2 函数

下表中的函数用于在 Python2 Runbook 中访问凭据。

| 函数 | 说明 |
|:---|:---|
| `automationassets.get_automation_credential` | 检索有关凭据资产的信息。 |

> [!NOTE]
> 导入`automationassets`Python runbook 顶部的模块以访问资产函数。

## <a name="creating-a-new-credential-asset"></a>创建新凭据资产

您可以使用 Azure 门户或使用 Windows PowerShell 创建新的凭据资产。

### <a name="create-a-new-credential-asset-with-the-azure-portal"></a>使用 Azure 门户创建新的凭据资产

1. 从自动化帐户中选择 **"共享资源**"下的**凭据**。
1. 选择“添加凭据”****。
2. 在"新建凭据"窗格中，按照命名标准输入适当的凭据名称。 
3. 在 **"用户名"** 字段中键入您的访问 ID。 
4. 对于这两个密码字段，请输入您的机密访问密钥。

    ![创建新凭据](../media/credentials/credential-create.png)

5. 如果选中多重身份验证框，则取消选中它。 
6. 单击 **"创建**"以保存新的凭据资产。

> [!NOTE]
> Azure 自动化不支持使用多重身份验证的用户帐户。

### <a name="create-a-new-credential-asset-with-windows-powershell"></a>使用 Windows PowerShell 创建新的凭据资产

下面的示例演示如何创建新的自动化凭据资产。 首先`PSCredential`使用名称和密码创建对象，然后用于创建凭据资产。 相反，`Get-Credential`您可以使用 cmdlet 提示用户键入用户名和密码。

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>使用 PowerShell 凭据

Runbook 或 DSC 配置检索具有活动的`Get-AutomationPSCredential`凭据资产。 此活动检索可用于需要`PSCredential`凭据的活动或 cmdlet 的对象。 还可以检索要单独使用的凭据对象的属性。 该对象具有用户名和安全密码的属性。 或者，您可以使用[GetNetwork凭据](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential.getnetworkcredential?view=pscore-6.2.0)方法检索表示密码不安全版本的[Network凭据](/dotnet/api/system.net.networkcredential)对象。

> [!NOTE]
> `Get-AzAutomationCredential`不检索可用于身份验证`PSCredential`的对象。 它仅提供有关凭据的信息。 如果需要在 Runbook 中使用凭据，则必须使用`PSCredential``Get-AutomationPSCredential`检索该凭据作为对象。

### <a name="textual-runbook-example"></a>文本运行簿示例

下面的示例演示如何在 Runbook 中使用 PowerShell 凭据。 它检索凭据并将其用户名和密码分配给变量。


```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

您还可以使用凭据对 Azure 进行[连接-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.3.0)。 在大多数情况下，您应该使用["运行即"帐户](../manage-runas-account.md)，并检索与[Get-AzAutomationConnection](../automation-connections.md)的连接。


```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzAccount -Credential $myPsCred
```

### <a name="graphical-runbook-example"></a>图形运行簿示例

您可以通过右键单击`Get-AutomationPSCredential`图形编辑器的"库"窗格中的凭据并选择"**添加到画布**"，将活动添加到图形 Runbook。

![将凭据添加到画布](../media/credentials/credential-add-canvas.png)

下图显示了在图形 Runbook 中使用凭据的示例。 在这种情况下，凭据为 Runbook 向 Azure 资源提供身份验证，如在[Azure 自动化中使用 Azure AD 对 Azure](../automation-use-azure-ad.md)进行身份验证中所述。 第一个活动检索有权访问 Azure 订阅的凭据。 然后，帐户连接活动使用此凭据为它之后的任何活动提供身份验证。 此处使用[管道链接](../automation-graphical-authoring-intro.md#links-and-workflow)，因为`Get-AutomationPSCredential`需要单个对象。  

![将凭据添加到画布](../media/credentials/get-credential.png)

## <a name="using-credentials-in-a-dsc-configuration"></a>在 DSC 配置中使用凭据

虽然 Azure 自动化中的 DSC 配置可以使用 的`Get-AutomationPSCredential`凭据资产使用，但它们还可以通过参数传递凭据资产。 有关详细信息，请参阅[在 Azure 自动化 DSC 中编译配置](../automation-dsc-compile.md#credential-assets)。

## <a name="using-credentials-in-python2"></a>在 Python2 中使用凭据

下面的示例显示了在 Python2 Runbook 中访问凭据的示例。


```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>后续步骤

* 要了解有关图形创作中的链接的更多详细信息，请参阅[图形创作中的链接](../automation-graphical-authoring-intro.md#links-and-workflow)。
* 要了解自动化的不同身份验证方法，请参阅[Azure 自动化安全](../automation-security-overview.md)。
* 要开始使用图形运行簿，请参阅[我的第一个图形运行簿](../automation-first-runbook-graphical.md)。
* 若要开始使用 PowerShell 工作流 Runbook，请参阅 [我的第一个 PowerShell 工作流 Runbook](../automation-first-runbook-textual.md)。
* 要开始使用 Python2 运行簿，请参阅[我的第一个 Python2 运行簿](../automation-first-runbook-textual-python2.md)。 
