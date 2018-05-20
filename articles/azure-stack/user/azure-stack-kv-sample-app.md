---
title: 允许应用程序检索 Azure Stack Key Vault 机密 | Microsoft Docs
description: 使用示例应用操作 Azure Stack Key Vault
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 3748b719-e269-4b48-8d7d-d75a84b0e1e5
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/11/2018
ms.author: mabrigg
ms.openlocfilehash: 39bce286c756660cd8755358cf98f2c8d35ce351
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2018
---
# <a name="a-sample-application-that-uses-keys-and-secrets-stored-in-a-key-vault"></a>示例使用的应用程序密钥和密钥保管库中存储的机密

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

按照运行示例应用程序 (HelloKeyVault)，从 Azure 堆栈中的密钥保管库中检索密钥和机密这篇文章中的步骤。

## <a name="prerequisites"></a>必备组件

你可以从 Azure 堆栈安装以下先决条件[开发工具包](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop)，或从基于 Windows 的外部客户端如果你是[通过 VPN 连接](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* 安装 [Azure Stack 兼容的 Azure PowerShell 模块](azure-stack-powershell-install.md)。
* 下载[使用 Azure Stack 所需的工具](azure-stack-powershell-download.md)。

## <a name="create-and-get-the-key-vault-and-application-settings"></a>创建并获取密钥保管库和应用程序设置

若要准备示例应用程序：

* 在 Azure 堆栈中创建密钥保管库。
* 在 Azure Active Directory (Azure AD) 中注册应用程序。

可以使用 Azure 门户或 PowerShell 准备示例应用程序。 这篇文章演示如何创建密钥保管库和使用 PowerShell 注册应用程序。

>[!NOTE]
>默认情况下，PowerShell 脚本将在 Active Directory 中创建新的应用程序。 但是，你可以注册一个现有的应用程序。

 在之前运行以下脚本，请确保你提供的值`aadTenantName`和`applicationPassword`变量。 如果没有指定的值`applicationPassword`，此脚本生成的随机密码。

```powershell
$vaultName           = 'myVault'
$resourceGroupName   = 'myResourceGroup'
$applicationName     = 'myApp'
$location            = 'local'

# Password for the application. If not specified, this script will generate a random password during app creation.
$applicationPassword = ''

# Function to generate a random password for the application.
Function GenerateSymmetricKey()
{
    $key = New-Object byte[](32)
    $rng = [System.Security.Cryptography.RNGCryptoServiceProvider]::Create()
    $rng.GetBytes($key)
    return [System.Convert]::ToBase64String($key)
}

Write-Host 'Please log into your Azure Stack user environment' -foregroundcolor Green

$tenantARM = "https://management.local.azurestack.external"
$aadTenantName = "PLEASE FILL THIS IN WITH YOUR AAD TENANT NAME. FOR EXAMPLE: myazurestack.onmicrosoft.com"

# Configure the Azure Stack operator’s PowerShell environment.
Add-AzureRMEnvironment `
  -Name "AzureStackUser" `
  -ArmEndpoint $tenantARM

Set-AzureRmEnvironment `
  -Name "AzureStackAdmin" `
  -GraphAudience "https://graph.windows.net/"

$TenantID = Get-AzsDirectoryTenantId `
  -AADTenantName $aadTenantName `
  -EnvironmentName AzureStackUser

# Sign in to the user portal.
Add-AzureRmAccount `
  -EnvironmentName "AzureStackUser" `
  -TenantId $TenantID `

$now = [System.DateTime]::Now
$oneYearFromNow = $now.AddYears(1)

$applicationPassword = GenerateSymmetricKey

# Create a new Azure AD application.
$identifierUri = [string]::Format("http://localhost:8080/{0}",[Guid]::NewGuid().ToString("N"))
$homePage = "http://contoso.com"

Write-Host "Creating a new AAD Application"
$ADApp = New-AzureRmADApplication `
  -DisplayName $applicationName `
  -HomePage $homePage `
  -IdentifierUris $identifierUri `
  -StartDate $now `
  -EndDate $oneYearFromNow `
  -Password $applicationPassword

Write-Host "Creating a new AAD service principal"
$servicePrincipal = New-AzureRmADServicePrincipal `
  -ApplicationId $ADApp.ApplicationId

# Create a new resource group and a key vault in that resource group.
New-AzureRmResourceGroup `
  -Name $resourceGroupName `
  -Location $location

Write-Host "Creating vault $vaultName"
$vault = New-AzureRmKeyVault -VaultName $vaultName `
  -ResourceGroupName $resourceGroupName `
  -Sku standard `
  -Location $location

# Specify full privileges to the vault for the application.
Write-Host "Setting access policy"
Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName `
  -ObjectId $servicePrincipal.Id `
  -PermissionsToKeys all `
  -PermissionsToSecrets all

Write-Host "Paste the following settings into the app.config file for the HelloKeyVault project:"
'<add key="VaultUrl" value="' + $vault.VaultUri + '"/>'
'<add key="AuthClientId" value="' + $servicePrincipal.ApplicationId + '"/>'
'<add key="AuthClientSecret" value="' + $applicationPassword + '"/>'
Write-Host

```

下一步的屏幕截图显示了用于创建密钥保管库的脚本的输出：

![密钥保管库用于访问密钥](media/azure-stack-kv-sample-app/settingsoutput.png)

请记下前面脚本返回的 **VaultUrl**、**AuthClientId** 和 **AuthClientSecret** 值。 将使用这些值运行 HelloKeyVault 应用程序。

## <a name="download-and-configure-the-sample-application"></a>下载和配置的示例应用程序

从 Azure [密钥保管库客户端示例](https://www.microsoft.com/en-us/download/details.aspx?id=45343)页下载密钥保管库示例。 在开发工作站上的.zip 文件的内容提取。 有两个示例文件夹中的应用程序，本文章将使用 HelloKeyVault。

加载 HelloKeyVault 示例：

* 浏览到**Microsoft.Azure.KeyVault.Samples** > **示例** > **HelloKeyVault**文件夹。
* 在 Visual Studio 中打开 HelloKeyVault 应用程序。

### <a name="configure-the-sample-application"></a>配置示例应用程序

在 Visual Studio 中：

* 打开 HelloKeyVault\App.config 文件并找到浏览到&lt; **appSettings** &gt;元素。
* 更新**VaultUrl**， **AuthClientId**，和**AuthClientSecret**键与用于创建密钥保管库返回的值。 (默认情况下，App.config 文件具有一个占位符，供*AuthCertThumbprint*。 替换此占位符替换为*AuthClientSecret*。)

  ![应用设置](media/azure-stack-kv-sample-app/appconfig.png)

* 重新生成解决方案。

## <a name="run-the-application"></a>运行应用程序

当你运行 HelloKeyVault 时，应用程序将在登录到 Azure AD 中，，，然后使用 AuthClientSecret 令牌向 Azure 堆栈中的密钥保管库进行身份验证。

你可以使用到 HelloKeyVault 示例：

* 执行基本操作创建，加密、 包装，和删除此类密钥和机密。
* 传递参数，如*加密*和*解密*到 HelloKeyVault，并将指定的更改应用到密钥保管库。

## <a name="next-steps"></a>后续步骤

[使用密钥保管库密码部署 VM](azure-stack-kv-deploy-vm-with-secret.md)

[使用 Key Vault 证书部署 VM](azure-stack-kv-push-secret-into-vm.md)
