---
title: "使用 PowerShell 管理 Azure 堆栈中的密钥保管库 |Microsoft 文档"
description: "了解如何使用 PowerShell 管理 Azure 堆栈中的密钥保管库"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 22B62A3B-B5A9-4B8C-81C9-DA461838FAE5
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: mabrigg
ms.openlocfilehash: 6ee2ceff10d16456a6e8c6283f40fa594b3311bc
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="manage-key-vault-in-azure-stack-by-using-powershell"></a>使用 PowerShell 管理 Azure 堆栈中的密钥保管库

本文可帮助你开始创建和使用 PowerShell 管理 Azure 堆栈中的密钥保管库。 本文中所述的密钥保管库 PowerShell cmdlet 都可用作 Azure PowerShell SDK 的一部分。 以下各节描述了所需的 PowerShell cmdlet:
   - 创建保管库。 
   - 存储和管理加密密钥和机密。 
   - 授权用户或应用程序来调用保管库中的操作。 

## <a name="prerequisites"></a>必备组件
* 您必须订阅服务关联，它包含 Azure 密钥保管库服务。
* [安装适用于 Azure 堆栈 PowerShell](azure-stack-powershell-install.md)。  
* [配置 Azure 堆栈用户 PowerShell 环境](azure-stack-powershell-configure-user.md)。

## <a name="enable-your-tenant-subscription-for-key-vault-operations"></a>启用密钥保管库操作的租户订阅

你可以发出针对密钥保管库的任何操作之前，你需要确保为保管库操作启用了你的租户订阅。 若要确认保管库操作已启用，请运行以下命令：

```PowerShell
Get-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault | ft -Autosize
```
**输出**

如果为保管库操作启用了你的订阅，该输出将显示"RegistrationState"等于"已注册"的密钥保管库的所有资源类型。

![注册状态](media/azure-stack-kv-manage-powershell/image1.png)

如果未启用保管库操作，调用以下命令以注册你的订阅中的密钥保管库服务：

```PowerShell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault
```

**输出**

如果注册成功，将返回以下输出：

![注册](media/azure-stack-kv-manage-powershell/image2.png)调用密钥保管库命令时，你可能会遇到错误，如"订阅未注册要使用命名空间 Microsoft.KeyVault。"如果遇到错误时，确认你有[启用密钥保管库资源提供程序](#enable-your-tenant-subscription-for-vault-operations)之前提到过的说明。

## <a name="create-a-key-vault"></a>创建 key vault 

在创建密钥保管库之前，创建资源组，以便与密钥保管库相关的资源的所有资源组中存在。 使用以下命令来创建新的资源组：

```PowerShell
New-AzureRmResourceGroup -Name “VaultRG” -Location local -verbose -Force

```

**输出**

![新的资源组](media/azure-stack-kv-manage-powershell/image3.png)

现在，使用**New-azurermkeyvault**命令之前创建的资源组中创建密钥保管库。 此命令读取三个必需参数： 资源组名称、 密钥保管库名称和地理位置。 

运行以下命令以创建密钥保管库：

```PowerShell
New-AzureRmKeyVault -VaultName “Vault01” -ResourceGroupName “VaultRG” -Location local -verbose
```
**输出**

![新的密钥保管库](media/azure-stack-kv-manage-powershell/image4.png)

此命令的输出显示你创建的密钥保管库的属性。 当应用程序访问此保管库时，它会使用**保管库 URI**输出所示的属性。 例如，保管库统一资源标识符 (URI) 在这种情况下的"https://vault01.vault.local.azurestack.external"。 与通过 REST API 此密钥保管库进行交互的应用程序必须使用此 URI。

Active Directory 联合身份验证服务 (AD FS) 中的基于的部署，当你创建一个密钥保管库使用 PowerShell，你可能会收到一条警告，指出"未设置访问策略。 没有用户或应用程序具有使用此保管库的访问权限。" 若要解决此问题，请将保管库的访问策略设置通过使用[Set-azurermkeyvaultaccesspolicy](azure-stack-kv-manage-powershell.md#authorize-an-application-to-use-a-key-or-secret)命令：

```PowerShell
# Obtain the security identifier(SID) of the active directory user
$adUser = Get-ADUser -Filter "Name -eq '{Active directory user name}'"
$objectSID = $adUser.SID.Value 

#Set the key vault access policy
Set-AzureRmKeyVaultAccessPolicy -VaultName "{key vault name}" -ResourceGroupName "{resource group name}" -ObjectId "{object SID}" -PermissionsToKeys {permissionsToKeys} -PermissionsToSecrets {permissionsToSecrets} -BypassObjectIdValidation 
```

## <a name="manage-keys-and-secrets"></a>管理密钥和机密

创建保管库后，使用以下步骤来创建和管理密钥和机密在保管库中。

### <a name="create-a-key"></a>创建密钥

使用**Add-azurekeyvaultkey**命令以创建或导入软件保护密钥在密钥保管库中的。 

```PowerShell
Add-AzureKeyVaultKey -VaultName “Vault01” -Name “Key01” -verbose -Destination Software
```
**目标**参数用于指定该密钥是软件保护。 已成功创建密钥后，命令输出创建的密钥的详细信息。

**输出**

![新建密钥](media/azure-stack-kv-manage-powershell/image5.png)

现在，你可以通过使用其 URI 来引用创建的密钥。 如果你创建或导入一个具有同名的现有键项，原始密钥是使用新密钥中指定的值更新。 可以通过使用特定于版本的 URI 的密钥来访问以前的版本。 例如： 

* 使用"密钥/https://vault10.vault.local.azurestack.external:443/key01"始终获取当前版本。 
* 使用"https://vault010.vault.local.azurestack.external:443/密钥/key01/d0b36ee2e3d14e9f967b8b6b1d38938a"来获取此特定版本。

### <a name="get-a-key"></a>获取密钥

使用**Get AzureKeyVaultKey**命令读取一个键和其详细信息。

```PowerShell
Get-AzureKeyVaultKey -VaultName “Vault01” -Name “Key01”
```

### <a name="create-a-secret"></a>创建机密

使用**集 AzureKeyVaultSecret**命令以创建或更新保管库中的机密。 如果一个尚不存在，则创建一个机密。 如果它已存在，则创建新版本的机密。

```PowerShell
$secretvalue = ConvertTo-SecureString “User@123” -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName “Vault01” -Name “Secret01” -SecretValue $secretvalue
```

**输出**

![创建机密](media/azure-stack-kv-manage-powershell/image6.png)

### <a name="get-a-secret"></a>获取机密

使用**Get AzureKeyVaultSecret**命令读取密钥保管库中的机密。 此命令返回所有或特定版本的机密。 

```PowerShell
Get-AzureKeyVaultSecret -VaultName “Vault01” -Name “Secret01”
```

创建密钥和机密后，你可以向授权外部应用程序来使用它们。

## <a name="authorize-an-application-to-use-a-key-or-secret"></a>授权应用程序使用密钥或机密

使用**Set-azurermkeyvaultaccesspolicy**命令以授权应用程序访问密钥或密钥保管库中的机密。
以下示例中，在保管库名称是*ContosoKeyVault*并且你想要授权的应用程序的客户端 ID *8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed*。 若要授权应用程序，请运行以下命令。 或者，可以指定**PermissionsToKeys**参数设置为用户、 应用程序或安全组的权限。

```PowerShell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign
```

如果你想要授权同一应用程序读取保管库中的机密，请运行以下 cmdlet:

```PowerShell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300 -PermissionsToKeys Get
```

## <a name="next-steps"></a>后续步骤
* [使用密码存储在密钥保管库中部署 VM](azure-stack-kv-deploy-vm-with-secret.md) 
* [使用密钥保管库中存储的证书进行部署 VM](azure-stack-kv-push-secret-into-vm.md)

