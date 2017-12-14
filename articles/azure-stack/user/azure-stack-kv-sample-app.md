---
title: "允许检索 Azure 堆栈密钥保管库机密信息的应用程序 |Microsoft 文档"
description: "使用示例应用程序来使用 Azure 堆栈密钥保管库"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 3748b719-e269-4b48-8d7d-d75a84b0e1e5
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/26/2017
ms.author: mabrigg
ms.openlocfilehash: 50103dca21d047c5cee211b2250e750739131bc1
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="sample-application-that-uses-keys-and-secrets-stored-in-a-key-vault"></a>使用密钥和机密密钥保管库中存储的示例应用程序

在本文中，我们说明你如何运行示例应用程序 (HelloKeyVault)，从 Azure 堆栈中的密钥保管库中检索密钥和机密。

## <a name="prerequisites"></a>必备组件 

从运行以下先决条件[开发工具包](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop)，或从基于 Windows 的外部客户端如果你是[通过 VPN 连接](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* 安装[Azure 堆栈兼容 Azure PowerShell 模块](azure-stack-powershell-install.md)。  
* 下载[工具需要使用处理 Azure 堆栈](azure-stack-powershell-download.md)。 

## <a name="create-and-get-the-key-vault-and-application-settings"></a>创建和获取密钥保管库和应用程序设置

首先，应在 Azure 堆栈中创建密钥保管库，并在 Azure Active Directory (Azure AD) 中注册应用程序。 你可以创建和使用 Azure 门户或 PowerShell 注册密钥保管库。 这篇文章演示了执行任务的 PowerShell 方法。 默认情况下，此 PowerShell 脚本在 Active Directory 中创建新的应用程序。 但是，你可以还使用现有的应用程序之一。 请确保提供的值`aadTenantName`和`applicationPassword`变量。 如果没有指定的值`applicationPassword`变量，此脚本生成的随机密码。 

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
Login-AzureRmAccount `
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

以下屏幕截图显示以前的脚本的输出：

![应用配置](media/azure-stack-kv-sample-app/settingsoutput.png)

记下**VaultUrl**， **AuthClientId**，和**AuthClientSecret**由上一个脚本返回的值。 这些值用于运行 HelloKeyVault 应用程序。

## <a name="download-and-run-the-sample-application"></a>下载并运行示例应用程序

从 Azure 下载密钥保管库示例[密钥保管库客户端示例](https://www.microsoft.com/en-us/download/details.aspx?id=45343)页。 提取到开发工作站上的.zip 文件的内容。 有两个样本示例文件夹中。 我们在本文中使用 HellpKeyVault 示例。 浏览到**Microsoft.Azure.KeyVault.Samples** > **示例** > **HelloKeyVault**文件夹并打开 HelloKeyVault 应用程序在 Visual Studio。 

打开 HelloKeyVault\App.config 文件并将的值<appSettings>具有元素**VaultUrl**， **AuthClientId**，和**AuthClientSecret**值返回上一个脚本。 请注意，默认情况下 App.config 包含的占位符*AuthCertThumbprint*，但使用*AuthClientSecret*相反。 替换的设置后，重新生成解决方案并启动应用程序。

![应用设置](media/azure-stack-kv-sample-app/appconfig.png)
 
登录到 Azure AD 应用程序，然后使用该令牌向 Azure 堆栈中的密钥保管库进行身份验证。 在应用程序执行操作，如创建、 加密、 包装，和上的密钥和机密密钥保管库删除。 你还可以如传递特定参数*加密*和*解密*应用程序，从而确保应用程序执行的操作是操作到保管库。 


## <a name="next-steps"></a>后续步骤
[使用密钥保管库密码部署 VM](azure-stack-kv-deploy-vm-with-secret.md)

[将使用密钥保管库证书 VM 部署](azure-stack-kv-push-secret-into-vm.md)



