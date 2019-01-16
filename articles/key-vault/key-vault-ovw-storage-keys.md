---
ms.assetid: ''
title: Azure Key Vault 托管存储帐户 - CLI
description: 存储帐户密钥在 Azure Key Vault 与 Azure 存储帐户基于密钥的访问方式之间提供无缝集成。
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: prashanthyv
ms.author: pryerram
manager: mbaldwin
ms.date: 10/03/2018
ms.openlocfilehash: 3ee0d19c174490d558a8ff06d3f5e038ffff211f
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/07/2019
ms.locfileid: "54064434"
---
# <a name="azure-key-vault-managed-storage-account---cli"></a>Azure Key Vault 托管存储帐户 - CLI

> [!NOTE]
> [Azure 存储现在支持 AAD 授权](https://docs.microsoft.com/azure/storage/common/storage-auth-aad)。 我们建议使用 Azure Active Directory 对存储进行身份验证和授权，因为用户不必担心轮换其存储帐户密钥。

- Azure Key Vault 管理 Azure 存储帐户 (ASA) 的密钥。
    - 在内部，Azure Key Vault 可以使用 Azure 存储帐户列出（同步）密钥。    
    - Azure Key Vault 定期重新生成（轮换）密钥。
    - 响应调用方时永远不会返回密钥值。
    - Azure Key Vault 管理存储帐户和经典存储帐户的密钥。

<a name="prerequisites"></a>先决条件
--------------
1. [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) 安装 Azure CLI   
2. [创建存储帐户](https://azure.microsoft.com/services/storage/)
    - 请按照此[文档](https://docs.microsoft.com/azure/storage/)中的步骤创建一个存储帐户  
    - **命名指南：** 存储帐户名称必须为 3 到 24 个字符，并且只能包含数字和小写字母。        
      
<a name="step-by-step-instructions-on-how-to-use-key-vault-to-manage-storage-account-keys"></a>有关如何使用 Key Vault 管理存储帐户密钥的分步说明
--------------------------------------------------------------------------------
在以下说明中，我们将 Key Vault 分配为服务，以便为存储帐户授予操作员权限

> [!NOTE]
> 请注意，设置了 Azure Key Vault 托管存储帐户密钥后，除了通过 Key Vault 进行更改外，这些密钥应**不**再进行更改。 托管存储帐户密钥意味着 Key Vault 将管理存储帐户密钥的轮换

1. 创建存储帐户后，运行以下命令来获取要管理的存储帐户的资源 ID

    ```
    az storage account show -n storageaccountname (Copy ID field out of the result of this command)
    ```
    
2. 获取 Azure Key Vault 的服务主体的应用程序 ID 

    ```
    az ad sp show --id cfa8b339-82a2-471a-a3c9-0fc0be7a4093
    ```
    
3. 将存储密钥操作员角色分配给 Azure Key Vault 标识

    ```
    az role assignment create --role "Storage Account Key Operator Service Role"  --assignee-object-id <ApplicationIdOfKeyVault> --scope <IdOfStorageAccount>
    ```
    
4. 创建 Key Vault 托管存储帐户。     <br /><br />
   下面，我们将重新生成周期设置为 90 天。 90 天后，Key Vault 将重新生成“key1”，并将活动密钥从“key2”交换为“key1”。
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key2 --auto-regenerate-key --regeneration-period P90D --resource-id <Resource-id-of-storage-account>
    ```
    如果用户未创建存储帐户并且对存储帐户没有权限，以下步骤会为你的帐户设置权限，确保你可以管理 Key Vault 中的所有存储权限。
 > [!NOTE] 
    如果用户对存储帐户没有权限，我们会先获取用户的对象 ID

    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
    ```
### <a name="relavant-azure-cli-cmdlets"></a>相关 Azure CLI cmdlet
- [Azure CLI 存储 Cmdlet](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest)

### <a name="relevant-powershell-cmdlets"></a>相关的 Powershell cmdlet

- [Get-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurekeyvaultmanagedstorageaccount)
- [Add-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureKeyVaultManagedStorageAccount)
- [Get-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Get-AzureKeyVaultManagedStorageSasDefinition)
- [Update-AzureKeyVaultManagedStorageAccountKey](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureKeyVaultManagedStorageAccountKey)
- [Remove-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/remove-azurekeyvaultmanagedstorageaccount)
- [Remove-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureKeyVaultManagedStorageSasDefinition)
- [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition)

## <a name="see-also"></a>另请参阅

- [关于键、密钥和证书](https://docs.microsoft.com/rest/api/keyvault/)
- [Key Vault 团队博客](https://blogs.technet.microsoft.com/kv/)
