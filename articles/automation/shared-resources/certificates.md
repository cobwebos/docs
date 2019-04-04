---
title: Azure 自动化中的证书资产
description: 证书在 Azure 自动化中是安全，以便它们可以访问通过 runbook 或 DSC 配置对 Azure 和第三方资源进行身份验证。  本文介绍了有关证书的详细信息，以及如何在文本和图形创作中使用证书。
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5ab8a231d4d44cfe2bfaf7c003ca57772a7a1814
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2019
ms.locfileid: "58896260"
---
# <a name="certificate-assets-in-azure-automation"></a>Azure 自动化中的证书资产

证书安全地存储在 Azure 自动化中以便通过 runbook 或 DSC 配置使用可以访问这些**Get-azurermautomationcertificate** Azure 资源管理器资源的活动。 此功能允许创建使用证书进行身份验证的 Runbook 和 DSC 配置，或者将证书添加到 Azure 或第三方资源。

>[!NOTE]
>Azure 自动化中的安全资产包括凭据、证书、连接和加密的变量。 这些资产已使用针对每个自动化帐户生成的唯一密钥加密并存储在 Azure 自动化中。 此密钥存储在系统托管的密钥保管库中。 在存储安全资产之前，从密钥保管库加载密钥，然后使用该密钥加密资产。 此过程由 Azure 自动化管理。

## <a name="azurerm-powershell-cmdlets"></a>AzureRM PowerShell cmdlet

对于 AzureRM，下表中的 cmdlet 用于通过 Windows PowerShell 创建和管理自动化凭据资产。 可在自动化 Runbook 和 DSC 配置中使用的 [AzureRM.Automation 模块](/powershell/azure/overview)已随附了这些 cmdlet。

|Cmdlet|描述|
|:---|:---|
|[Get-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/get-azurermautomationcertificate)|检索有关要在 Runbook 或 DSC 配置中使用的证书的信息。 只能从 Get-AutomationCertificate 活动中检索证书本身。|
|[New-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/new-azurermautomationcertificate)|将新证书创建到 Azure 自动化中。|
[Remove-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/remove-azurermautomationcertificate)|从 Azure自动化中删除证书。|
|[Set-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/set-azurermautomationcertificate)|设置现有证书的属性，包括上传证书文件和设置 .pfx 的密码。|
|[Add-AzureCertificate](/previous-versions/azure/dn495214)|为指定的云服务上传服务证书。|

## <a name="activities"></a>活动

下表中的活动用于在 Runbook 和 DSC 配置中访问证书。

| 活动 | 描述 |
|:---|:---|
|Get-AutomationCertificate|在 Runbook 或 DSC 配置中获取要使用的证书。 返回一个 [System.Security.Cryptography.X509Certificates.X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) 对象。|

> [!NOTE] 
> 应避免在 Runbook 或 DSC 配置中的 Get-AutomationCertificate 的 - Name 参数中使用变量，因为这可能会使设计时发现 Runbook 或 DSC 配置与自动化变量之间的依赖关系变得复杂化。

## <a name="python2-functions"></a>Python2 函数

下表中的函数用于在 Python2 Runbook 中访问证书。

| 函数 | 描述 |
|:---|:---|
| automationassets.get_automation_certificate | 检索有关证书资产的信息。 |

> [!NOTE]
> 必须在 Python Runbook 开头部分导入 automationassets 模块才能访问资产函数。

## <a name="creating-a-new-certificate"></a>创建新证书

创建新证书时，需要将 .cer 或 .pfx 文件上传到 Azure 自动化。 将证书标记为可导出后，可以将其转出 Azure 自动化证书存储区。 如果不是可导出，然后它可以仅用于 runbook 或 DSC 配置中签名。 Azure 自动化要求证书具有以下提供程序：Microsoft 增强 RSA 和 AES 加密提供程序。

### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>使用 Azure 门户创建新证书

1. 从自动化帐户中，单击**资产**磁贴以打开**资产**页。
2. 单击**证书**磁贴以打开**证书**页。
3. 单击**将证书添加**页的顶部。
4. 在“名称”框中键入证书的名称。
5. 若要浏览 .cer 或.pfx 文件，请单击“上传证书文件”下的“选择文件”。 如果选择了.pfx 文件，指定密码，以及是否可以导出。
6. 单击“创建”以保存新的证书资产。

### <a name="to-create-a-new-certificate-with-powershell"></a>若要使用 PowerShell 创建新的证书

以下示例演示了如何创建新的自动化证书并将其标记为可导出。 这会导入现有的 .pfx 文件。

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzureRmAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-resource-manager-template"></a>使用资源管理器模板创建新的证书

下面的示例演示如何将证书部署到使用 Resource Manager 模板通过 PowerShell 在自动化帐户：

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
New-AzureRmResourceGroupDeployment -Name NewCert -ResourceGroupName TestAzureAuto -TemplateFile .\template.json
```

## <a name="using-a-certificate"></a>使用证书

若要使用证书，请使用 Get-AutomationCertificate 活动。 不能使用[Get-azurermautomationcertificate](/powershell/module/azurerm.automation/get-azurermautomationcertificate) cmdlet，因为它将返回有关证书资产而不是证书本身的信息。

### <a name="textual-runbook-sample"></a>文本 Runbook 示例

以下示例代码演示了如何将证书添加到 Runbook 中的云服务。 在此示例中，已从加密的自动化变量检索了密码。

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-sample"></a>图形 Runbook 示例

您将添加**Get-automationcertificate**到库窗格中的证书上右键单击并选择图形 runbook**添加到画布**。

![将证书添加到画布](../media/certificates/automation-certificate-add-to-canvas.png)

下图显示了在图形 Runbook 中使用证书的示例。 这是与前面的示例演示如何从文本 runbook 向云服务添加证书的相同。

![示例图形创作](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python2-sample"></a>Python2 示例

以下示例演示了如何在 Python2 Runbook 中访问证书。

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>后续步骤

- 若要详细了解如何使用链接控制 runbook 旨在执行的活动的逻辑流，请参阅[图形创作中的链接](../automation-graphical-authoring-intro.md#links-and-workflow)。 
