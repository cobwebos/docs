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
ms.date: 08/26/2017
ms.author: mabrigg
ms.openlocfilehash: da2c733dbb1d1d3c8294ee82e722d33d363b29ab
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="sample-application-that-uses-keys-and-secrets-stored-in-a-key-vault"></a>使用密钥保管库中存储的密钥和机密的示例应用程序

本文说明如何运行示例应用程序 (HelloKeyVault)，从 Azure Stack 的密钥保管库中检索密钥和机密。

## <a name="prerequisites"></a>必备组件 

如果已[通过 VPN 建立连接](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)，请通过[开发工具包](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop)或基于 Windows 的外部客户端运行以下先决条件操作：

* 安装 [Azure Stack 兼容的 Azure PowerShell 模块](azure-stack-powershell-install.md)。  
* 下载[使用 Azure Stack 所需的工具](azure-stack-powershell-download.md)。 

## <a name="create-and-get-the-key-vault-and-application-settings"></a>创建并获取密钥保管库和应用程序设置

首先，应在 Azure Stack 中创建密钥保管库，并在 Azure Active Directory (Azure AD) 中注册应用程序。 可以使用 Azure 门户或 PowerShell 创建及注册密钥保管库。 本文说明以 PowerShell 方式执行任务。 默认情况下，此 PowerShell 脚本会在 Active Directory 中创建新的应用程序。 不过，也可以使用其中一个现有应用程序。 请确保提供 `aadTenantName` 和 `applicationPassword` 变量的值。 如果未为 `applicationPassword` 变量指定值，此脚本会生成随机密码。 

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

# Create a new resource group and a key vault within that resource group.
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

以下屏幕截图显示前面脚本的输出：

![应用配置](media/azure-stack-kv-sample-app/settingsoutput.png)

请记下前面脚本返回的 **VaultUrl**、**AuthClientId** 和 **AuthClientSecret** 值。 将使用这些值运行 HelloKeyVault 应用程序。

## <a name="download-and-run-the-sample-application"></a>下载并运行示例应用程序

从 Azure [密钥保管库客户端示例](https://www.microsoft.com/en-us/download/details.aspx?id=45343)页下载密钥保管库示例。 将 .zip 文件的内容解压缩到开发工作站上。 示例文件夹中有两个示例。 我们在本文中会使用 HellpKeyVault 示例。 浏览到 **Microsoft.Azure.KeyVault.Samples** > **示例** > **HelloKeyVault** 文件夹，然后在 Visual Studio 中打开 HelloKeyVault 应用程序。 

打开 HelloKeyVault\App.config 文件，并将 <appSettings> 元素的值替换为前面脚本返回的 **VaultUrl**、**AuthClientId** 和 **AuthClientSecret** 值。 请注意，默认情况下 App.config 包含 *AuthCertThumbprint* 的占位符，但请改用 *AuthClientSecret*。 替换设置后，请重新生成解决方案并启动应用程序。

![应用设置](media/azure-stack-kv-sample-app/appconfig.png)
 
应用程序会登录到 Azure AD，然后使用令牌向 Azure Stack 中的密钥保管库进行身份验证。 应用程序会对密钥保管库的密钥和机密执行创建、加密、包装和删除等操作。 也可以将特定参数（如 *encrypt* 和 *decrypt*）传递给应用程序，以确保应用程序仅对保管库执行这些操作。 


## <a name="next-steps"></a>后续步骤
[使用密钥保管库密码部署 VM](azure-stack-kv-deploy-vm-with-secret.md)

[使用 Key Vault 证书部署 VM](azure-stack-kv-push-secret-into-vm.md)



