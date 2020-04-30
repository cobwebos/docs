---
title: 在 Azure 自动化中管理证书
description: 证书安全地存储在 Azure 自动化中，因此 runbook 或 DSC 配置可以访问这些证书，对 Azure 和第三方资源进行身份验证。 本文介绍了有关证书的详细信息，以及如何在文本和图形创作中使用证书。
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 69bc1a0333365e15452c6d3b253266d37d99b608
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732832"
---
# <a name="manage-certificates-in-azure-automation"></a>在 Azure 自动化中管理证书

证书已安全地存储在 Azure 自动化中，因此可以使用适用于 Azure 资源管理器资源的[AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0)活动通过 RUNBOOK 或 DSC 配置访问这些证书。 通过安全证书存储，可以创建使用证书进行身份验证的 runbook 和 DSC 配置，或者将证书添加到 Azure 或第三方资源。

Azure 自动化中的安全资产包括凭据、证书、连接和加密的变量。 这些资产已使用针对每个自动化帐户生成的唯一密钥加密并存储在 Azure 自动化中。 此密钥存储在系统管理的 Key Vault 中。 在存储安全资产之前，从密钥保管库加载密钥，然后使用该密钥加密资产。 此过程由 Azure 自动化管理。

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可参阅[如何更新 Azure 自动化中的 Azure PowerShell 模块](../automation-update-azure-modules.md)，将模块更新到最新版本。

## <a name="az-powershell-cmdlets"></a>Az PowerShell cmdlet

对于 Az，下表中的 cmdlet 用于通过 Windows PowerShell 创建和管理自动化凭据资产。 它们作为[Az 模块](/powershell/azure/overview)的一部分提供，可在自动化 RUNBOOK 和 DSC 配置中使用。

|Cmdlet |说明|
| --- | ---|
|[Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate)|为指定的云服务上传服务证书。|
|[AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0)|检索有关要在 Runbook 或 DSC 配置中使用的证书的信息。 只能使用`Get-AutomationCertificate`活动检索证书本身。|
|[新-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate?view=azps-3.7.0)|在 Azure Automation 中创建新证书。|
|[AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationCertificate?view=azps-3.7.0)|从 Azure自动化中删除证书。|
|[AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationCertificate?view=azps-3.7.0)|设置现有证书的属性，包括上传证书文件和设置 **.pfx**文件的密码。|

## <a name="activities"></a>活动

下表中的活动用于在 Runbook 和 DSC 配置中访问证书。

| 活动 | 说明 |
|:---|:---|
|`Get-AutomationCertificate`|在 Runbook 或 DSC 配置中获取要使用的证书。 返回一个 [System.Security.Cryptography.X509Certificates.X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) 对象。|

> [!NOTE] 
> 应避免`Get-AutomationCertificate`在 RUNBOOK 或 DSC 配置`Name`中的参数中使用变量。 在此参数中使用变量会使在设计时对 runbook 或 DSC 配置与自动化变量之间的依赖关系的发现复杂化。

## <a name="python-2-functions"></a>Python 2 功能

下表中的函数用于在 Python 2 runbook 中访问证书。

| 函数 | 说明 |
|:---|:---|
| `automationassets.get_automation_certificate` | 检索有关证书资产的信息。 |

> [!NOTE]
> 必须将`automationassets`模块导入到 Python runbook 的开始处才能访问资产函数。

## <a name="creating-a-new-certificate"></a>创建新证书

创建新证书时，需要将 .cer 或 .pfx 文件上传到 Azure 自动化。 将证书标记为可导出后，可以将其转出 Azure 自动化证书存储区。 如果证书不可导出，则它只可用于在 Runbook 或 DSC 配置中签名。 Azure Automation 要求证书具有提供商**Microsoft 增强的 RSA 和 AES 加密提供程序**。

### <a name="create-a-new-certificate-with-the-azure-portal"></a>使用 Azure 门户创建新证书

1. 在自动化帐户中，单击 "**资产**" 以打开 "资产" 页。
2. 选择 "**证书**"，打开 "证书" 页。
3. 单击页面顶部的“添加证书”****。
4. 在 "**名称**" 字段中键入证书的名称。
5. 若要浏览 **.cer**或 **.pfx**文件，请单击 "**上传证书文件**" 下**的 "选择文件**"。 如果选择了 **.pfx**文件，请指定密码，并指示是否可以导出。
6. 单击“创建”**** 以保存新的证书资产。

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

## <a name="using-a-certificate"></a>使用证书

若要使用证书，请使用`Get-AutomationCertificate`活动。 不能使用[AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationCertificate?view=azps-3.7.0) cmdlet，因为它返回有关证书资产而不是证书本身的信息。

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

右键单击`Get-AutomationCertificate` "库" 窗格中的证书，然后选择 "**添加到画布**"，将活动添加到图形 runbook。

![将证书添加到画布](../media/certificates/automation-certificate-add-to-canvas.png)

下图显示了在图形 Runbook 中使用证书的示例。 这与上面演示如何从文本 Runbook 向云服务添加证书的示例相同。

![示例图形创作](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python-2-example"></a>Python 2 示例

下面的示例演示如何在 Python2 runbook 中访问证书。

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>后续步骤

- 若要详细了解如何使用链接来控制 runbook 执行的活动的逻辑流，请参阅[图形创作中的链接](../automation-graphical-authoring-intro.md#links-and-workflow)。 
