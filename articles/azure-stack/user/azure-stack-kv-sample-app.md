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
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2018
ms.locfileid: "34807374"
---
# <a name="a-sample-application-that-uses-keys-and-secrets-stored-in-a-key-vault"></a>使用密钥保管库中存储的密钥和机密的示例应用程序

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

按照本文中的步骤运行示例应用程序 (HelloKeyVault)，从 Azure Stack 中的密钥保管库检索密钥和机密。

## <a name="prerequisites"></a>必备组件

如果是[通过 VPN 建立连接](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)，可以从 Azure Stack [开发工具包](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop)或从基于 Windows 的外部客户端安装以下先决条件组件：

* 安装 [Azure Stack 兼容的 Azure PowerShell 模块](azure-stack-powershell-install.md)。
* 下载[使用 Azure Stack 所需的工具](azure-stack-powershell-download.md)。

## <a name="create-and-get-the-key-vault-and-application-settings"></a>创建并获取密钥保管库和应用程序设置

若要准备示例应用程序，请执行以下操作：

* 在 Azure Stack 中创建密钥保管库。
* 在 Azure Active Directory (Azure AD) 中注册应用程序。

可以使用 Azure 门户或 PowerShell 来准备示例应用程序。 本文展示了如何使用 PowerShell 创建密钥保管库并注册应用程序。

>[!NOTE]
>默认情况下，此 PowerShell 脚本会在 Active Directory 中创建一个新的应用程序。 不过，你也可以注册现有的某个应用程序。

 在运行以下脚本之前，请确保为 `aadTenantName` 和 `applicationPassword` 变量提供值。 如果没有为 `applicationPassword` 指定值，此脚本会生成随机密码。

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

下面的屏幕捕获显示了用于创建密钥保管库的脚本的输出：

![具有访问密钥的密钥保管库](media/azure-stack-kv-sample-app/settingsoutput.png)

请记下前面脚本返回的 **VaultUrl**、**AuthClientId** 和 **AuthClientSecret** 值。 将使用这些值运行 HelloKeyVault 应用程序。

## <a name="download-and-configure-the-sample-application"></a>下载并配置示例应用程序

从 Azure [密钥保管库客户端示例](https://www.microsoft.com/en-us/download/details.aspx?id=45343)页下载密钥保管库示例。 将 .zip 文件的内容解压缩到开发工作站上。 samples 文件夹中有两个应用程序，本文使用了 HelloKeyVault。

若要加载 HelloKeyVault 示例，请执行以下操作：

* 浏览到 **Microsoft.Azure.KeyVault.Samples** > **samples** > **HelloKeyVault** 文件夹。
* 在 Visual Studio 中打开 HelloKeyVault 应用程序。

### <a name="configure-the-sample-application"></a>配置示例应用程序

在 Visual Studio 中：

* 打开 HelloKeyVault\App.config 文件并浏览到 &lt;**appSettings**&gt; 元素。
* 将 **VaultUrl**、**AuthClientId** 和 **AuthClientSecret** 密钥更新为用来创建密钥库的脚本返回的值。 （默认情况下，App.config 文件中有一个用于 *AuthCertThumbprint* 的占位符。 请将此占位符替换为 *AuthClientSecret*。）

  ![应用设置](media/azure-stack-kv-sample-app/appconfig.png)

* 重新生成解决方案。

## <a name="run-the-application"></a>运行应用程序

运行 HelloKeyVault 时，应用程序会登录到 Azure AD，然后使用 AuthClientSecret 令牌向 Azure Stack 中的密钥保管库证明身份。

可以使用 HelloKeyVault 示例执行以下操作：

* 对密钥和机密执行基本操作，例如创建、加密、包装和删除。
* 向 HelloKeyVault 传递诸如 *encrypt* 和 *decrypt* 等参数，以及向密钥保管库应用指定的更改。

## <a name="next-steps"></a>后续步骤

[使用密钥保管库密码部署 VM](azure-stack-kv-deploy-vm-with-secret.md)

[使用 Key Vault 证书部署 VM](azure-stack-kv-push-secret-into-vm.md)
