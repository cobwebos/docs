---
title: 使用 PowerShell 管理 Azure Stack 中的 Key Vault | Microsoft Docs
description: 了解如何使用 PowerShell 管理 Azure Stack 中的 Key Vault
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 22B62A3B-B5A9-4B8C-81C9-DA461838FAE5
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.openlocfilehash: a7d49c29445b425caae2fbd1967a2408657b4610
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2019
ms.locfileid: "54054115"
---
# <a name="manage-key-vault-in-azure-stack-using-powershell"></a>使用 PowerShell 管理 Azure Stack 中的 Key Vault

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

可以使用 PowerShell 管理 Azure Stack 中的 Key Vault。 本文介绍了如何使用密钥保管库 PowerShell cmdlet:

* 创建密钥保管库。
* 存储和管理加密密钥和机密。
* 授权用户或应用程序调用保管库中的操作。

>[!NOTE]
>本文中介绍的 Key Vault PowerShell cmdlet 在 Azure PowerShell SDK 中提供。

## <a name="prerequisites"></a>必备组件

* 必须订阅包含 Azure Key Vault 服务的产品/服务。
* [安装适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)。
* [配置 Azure Stack 用户的 PowerShell 环境](azure-stack-powershell-configure-user.md)。

## <a name="enable-your-tenant-subscription-for-key-vault-operations"></a>启用适用于 Key Vault 操作的租户订阅

在可以执行针对密钥保管库的任何操作之前，必须确保你的租户订阅可用于保管库操作。 若要验证保管库操作是否已启用，请运行以下命令：

```PowerShell
Get-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault | ft -Autosize
```

**输出**

如果为保管库操作启用你的订阅，则输出会显示**RegistrationState**是**已注册**的密钥保管库的所有资源类型。

![密钥保管库注册状态](media/azure-stack-kv-manage-powershell/image1.png)

如果未启用保管库操作，运行以下命令以在订阅中注册密钥保管库服务：

```PowerShell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault
```

**输出**

如果注册成功，则返回以下输出：

![注册](media/azure-stack-kv-manage-powershell/image2.png)

当您调用密钥保管库命令时，可能会收到错误，例如"该订阅未注册为使用命名空间 'microsoft.keyvault '。"如果遇到错误，确认你有[启用密钥保管库资源提供程序](#enable-your-tenant-subscription-for-vault-operations)前面给出的说明。

## <a name="create-a-key-vault"></a>创建 key vault

在创建密钥保管库之前，请创建资源组，使得与密钥保管库相关的所有资源都存在于一个资源组中。 使用以下命令来创建新资源组：

```PowerShell
New-AzureRmResourceGroup -Name "VaultRG" -Location local -verbose -Force
```

**输出**

![新建资源组](media/azure-stack-kv-manage-powershell/image3.png)

现在，使用**New-azurermkeyvault** cmdlet 以前创建的资源组中创建密钥保管库。 此命令读取三个必需参数：资源组名称、密钥保管库名称和地理位置。

运行以下命令，创建密钥保管库：

```PowerShell
New-AzureRmKeyVault -VaultName "Vault01" -ResourceGroupName "VaultRG" -Location local -verbose
```

**输出**

![新的密钥保管库](media/azure-stack-kv-manage-powershell/image4.png)

此命令的输出会显示创建的密钥保管库的属性。 当应用程序访问此保管库时，它必须使用`Vault URI`属性，它是`https://vault01.vault.local.azurestack.external`在此示例中。

### <a name="active-directory-federation-services-ad-fs-deployment"></a>Active Directory 联合身份验证服务 (AD FS) 部署

在 AD FS 部署中，可能会收到此警告："未设置访问策略。 没有用户或应用程序具有使用此保管库所需的访问权限。” 若要解决此问题，通过设置保管库的访问策略[Set-azurermkeyvaultaccesspolicy](azure-stack-kv-manage-powershell.md#authorize-an-application-to-use-a-key-or-secret) cmdlet:

```PowerShell
# Obtain the security identifier(SID) of the active directory user
$adUser = Get-ADUser -Filter "Name -eq '{Active directory user name}'"
$objectSID = $adUser.SID.Value

# Set the key vault access policy
Set-AzureRmKeyVaultAccessPolicy -VaultName "{key vault name}" -ResourceGroupName "{resource group name}" -ObjectId "{object SID}" -PermissionsToKeys {permissionsToKeys} -PermissionsToSecrets {permissionsToSecrets} -BypassObjectIdValidation
```

## <a name="manage-keys-and-secrets"></a>管理密钥和机密

创建密钥保管库后，使用以下步骤来创建和管理保管库中密钥和机密。

### <a name="create-a-key"></a>创建密钥

使用**Add-azurekeyvaultkey** cmdlet 来创建或导入受软件保护密钥在密钥保管库中的。

```PowerShell
Add-AzureKeyVaultKey -VaultName "Vault01" -Name "Key01" -verbose -Destination Software
```

`Destination`参数用于指定该密钥是软件保护。 已成功创建密钥，此命令会输出新创建的密钥的详细信息。

**输出**

![新建密钥](media/azure-stack-kv-manage-powershell/image5.png)

现在可以使用其 URI 来引用新创建的密钥。 如果创建或导入的密钥的名称与现有密钥相同，则会使用新密钥中指定的值来更新原始密钥。 可以使用密钥的特定于版本的 URI 来访问以前的版本。 例如：

* 使用`https://vault10.vault.local.azurestack.external:443/keys/key01`可以始终获取最新版本。
* 使用`https://vault010.vault.local.azurestack.external:443/keys/key01/d0b36ee2e3d14e9f967b8b6b1d38938a`可获取此特定版本。

### <a name="get-a-key"></a>获取密钥

使用**Get-azurekeyvaultkey** cmdlet 来读取一个键和其详细信息。

```PowerShell
Get-AzureKeyVaultKey -VaultName "Vault01" -Name "Key01"
```

### <a name="create-a-secret"></a>创建机密

使用**Set-azurekeyvaultsecret** cmdlet 来创建或更新保管库中的机密。 如果尚不存在，则创建机密。 如果已经存在机密，则会创建新版机密。

```PowerShell
$secretvalue = ConvertTo-SecureString "User@123" -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName "Vault01" -Name "Secret01" -SecretValue $secretvalue
```

**输出**

![创建机密](media/azure-stack-kv-manage-powershell/image6.png)

### <a name="get-a-secret"></a>获取机密

使用**Get-azurekeyvaultsecret** cmdlet 来读取密钥保管库中的机密。 此命令可以返回所有版本或特定版本的机密。

```PowerShell
Get-AzureKeyVaultSecret -VaultName "Vault01" -Name "Secret01"
```

创建密钥和机密以后，即可授权外部应用程序使用它们。

## <a name="authorize-an-application-to-use-a-key-or-secret"></a>授权应用程序使用密钥或机密

使用**Set-azurermkeyvaultaccesspolicy** cmdlet 来授权访问密钥或密钥保管库中的机密的应用程序。 以下示例中，在保管库名称是`ContosoKeyVault`，并且你想要授权的应用程序的客户端 ID 为`8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed`。 若要授权此应用程序，请运行以下命令。 或者，您可以指定`PermissionsToKeys`参数来设置用户、 应用程序或安全组的权限。

```PowerShell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign
```

如果要授权同一应用程序读取保管库中的机密，请运行以下 cmdlet：

```PowerShell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300 -PermissionsToKeys Get
```

## <a name="next-steps"></a>后续步骤

* [使用 Key Vault 中存储的密码来部署 VM](azure-stack-kv-deploy-vm-with-secret.md)
* [使用 Key Vault 中存储的证书来部署 VM](azure-stack-kv-push-secret-into-vm.md)
