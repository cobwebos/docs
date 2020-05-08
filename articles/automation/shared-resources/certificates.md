---
title: 在 Azure 自动化中管理证书
description: Azure Automation 安全地存储证书，以便 runbook 或 DSC 配置可以访问这些证书，对 Azure 和第三方资源进行身份验证。 本文介绍证书的详细信息，以及如何在文本和图形创作中使用证书。
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2793679fb4588d00ea4e37340b19183398cb9d90
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864311"
---
# <a name="manage-certificates-in-azure-automation"></a>在 Azure 自动化中管理证书

Azure Automation 使用适用于 Azure 资源管理器资源的[AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) cmdlet 安全地存储证书以供 RUNBOOK 和 DSC 配置访问。 通过安全证书存储，可以创建使用证书进行身份验证的 runbook 和 DSC 配置，或者将证书添加到 Azure 或第三方资源。

>[!NOTE]
>Azure 自动化中的安全资产包括凭据、证书、连接和加密的变量。 这些资产通过使用为每个自动化帐户生成的唯一密钥加密并存储在自动化中。 自动化将密钥存储在系统托管的 Key Vault 服务中。 在存储安全资产之前，自动化会从 Key Vault 加载密钥，然后使用该密钥来加密资产。 

>[!NOTE]
>本文介绍如何使用 Azure PowerShell Az 模块。 你仍可以使用 AzureRM 模块。 若要了解有关 Az module 和 AzureRM 兼容性的详细信息，请参阅[新增 Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az module 安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于你的自动化帐户，你可以通过使用[如何在 Azure 自动化中更新 Azure PowerShell 模块](../automation-update-azure-modules.md)，将模块更新到最新版本。

## <a name="powershell-cmdlets-to-access-certificates"></a>用于访问证书的 PowerShell cmdlet

下表中的 cmdlet 通过 PowerShell 创建和管理自动化证书。 它们作为[Az 模块](modules.md#az-modules)的一部分提供。

|Cmdlet |说明|
| --- | ---|
|[AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0)|检索有关要在 Runbook 或 DSC 配置中使用的证书的信息。 只能使用内部`Get-AutomationCertificate` cmdlet 检索证书本身。|
|[新-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate?view=azps-3.7.0)|在自动化中创建新的证书。|
|[AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationCertificate?view=azps-3.7.0)|从自动化中删除证书。|
|[AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationCertificate?view=azps-3.7.0)|设置现有证书的属性，包括上传证书文件和设置 **.pfx**文件的密码。|

[AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate) cmdlet 还可用于为指定的云服务上传服务证书。

## <a name="internal-cmdlets-to-access-certificates"></a>用于访问证书的内部 cmdlet

下表中的内部 cmdlet 用于在 runbook 中访问证书。 此 cmdlet 附带全局模块`Orchestrator.AssetManagement.Cmdlets`。 有关详细信息，请参阅[内部 cmdlet](modules.md#internal-cmdlets)。

| 内部 Cmdlet | 说明 |
|:---|:---|
|`Get-AutomationCertificate`|在 Runbook 或 DSC 配置中获取要使用的证书。 返回一个 [System.Security.Cryptography.X509Certificates.X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) 对象。|

> [!NOTE] 
> 应避免`Get-AutomationCertificate`在 RUNBOOK 或 DSC 配置`Name`中的参数中使用变量。 此类变量可以在设计时使 runbook 或 DSC 配置和自动化变量之间依赖项的发现复杂化。

## <a name="python-2-functions-to-access-certificates"></a>Python 2 用于访问证书的函数

使用下表中的函数来访问 Python 2 runbook 中的证书。

| 函数 | 说明 |
|:---|:---|
| `automationassets.get_automation_certificate` | 检索有关证书资产的信息。 |

> [!NOTE]
> 必须将`automationassets`模块导入到 Python runbook 的开始处才能访问资产函数。

## <a name="create-a-new-certificate"></a>创建新证书

创建新证书时，需要将 .cer 或 .pfx 文件上传到自动化。 如果将证书标记为可导出，则可以将其从自动化证书存储中传输出去。 如果证书不可导出，则它只可用于在 Runbook 或 DSC 配置中签名。 自动化要求证书具有提供商**Microsoft 增强的 RSA 和 AES 加密提供程序**。

### <a name="create-a-new-certificate-with-the-azure-portal"></a>使用 Azure 门户创建新证书

1. 在自动化帐户中，选择 "**资产** > **证书** > " "**添加证书**"。
1. 在 "**名称**" 字段中，键入证书的名称。
1. 若要浏览 **.cer**或 **.pfx**文件，请在 "**上传证书文件**" 下，选择 "**选择文件**"。 如果选择了 **.pfx**文件，请指定密码，并指示是否可以导出。
1. 选择 "**创建**" 以保存新的证书资产。

### <a name="create-a-new-certificate-with-powershell"></a>使用 PowerShell 创建新证书

以下示例演示了如何创建新的自动化证书并将其标记为可导出。 此示例导入现有的 **.pfx**文件。

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-a-resource-manager-template"></a>使用资源管理器模板创建新证书

下面的示例演示如何通过 PowerShell 使用资源管理器模板将证书部署到自动化帐户：

```powershell-interactive
$AutomationAccountName = "<automation account name>"
$PfxCertPath = '<PFX cert path'
$CertificatePassword = '<password>'
$certificateName = '<certificate name>'
$AutomationAccountName = '<automation account name>'
$flags = [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::PersistKeySet `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::MachineKeySet
# Load the certificate into memory
$PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPath, $CertificatePassword, $flags)
# Export the certificate and convert into base 64 string
$Base64Value = [System.Convert]::ToBase64String($PfxCert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12))
$Thumbprint = $PfxCert.Thumbprint


$json = @"
{
    '`$schema': 'https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#',
    'contentVersion': '1.0.0.0',
    'resources': [
        {
            'name': '$AutomationAccountName/$certificateName',
            'type': 'Microsoft.Automation/automationAccounts/certificates',
            'apiVersion': '2015-10-31',
            'properties': {
                'base64Value': '$Base64Value',
                'thumbprint': '$Thumbprint',
                'isExportable': true
            }
        }
    ]
}
"@

$json | out-file .\template.json
New-AzResourceGroupDeployment -Name NewCert -ResourceGroupName TestAzureAuto -TemplateFile .\template.json
```

## <a name="get-a-certificate"></a>获取证书

若要检索证书，请使用内部`Get-AutomationCertificate` cmdlet。 不能使用[AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) cmdlet，因为它返回有关证书资产的信息，而不是证书本身。

### <a name="textual-runbook-example"></a>文本 runbook 示例

下面的示例演示如何将证书添加到 runbook 中的云服务。 在此示例中，已从加密的自动化变量检索了密码。

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-example"></a>图形 runbook 示例

右键单击 "库" 窗格`Get-AutomationCertificate`中的证书，然后选择 "**添加到画布**"，将内部 cmdlet 的活动添加到图形 runbook。

![向画布添加证书的屏幕截图](../media/certificates/automation-certificate-add-to-canvas.png)

下图显示了在图形 Runbook 中使用证书的示例。 

![图形创作的示例屏幕截图](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python-2-example"></a>Python 2 示例

下面的示例演示如何在 Python 2 runbook 中访问证书。

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>后续步骤

* 若要了解有关用于访问证书的 cmdlet 的详细信息，请参阅[在 Azure 自动化中管理模块](modules.md)。
* 有关 runbook 的常规信息，请参阅[在 Azure 自动化中执行 Runbook](../automation-runbook-execution.md)。
* 有关 DSC 配置的详细信息，请参阅[状态配置概述](../automation-dsc-overview.md)。 
