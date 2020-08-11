---
title: 配置自己的密钥以用于加密 Azure 服务总线静态数据
description: 本文介绍了如何配置自己的密钥以用于加密 Azure 服务总线静态数据。
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 97de8df336367a74f66628675569c06d7726f2a4
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2020
ms.locfileid: "88067233"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-service-bus-data-at-rest-by-using-the-azure-portal"></a>使用 Azure 门户配置客户管理的密钥以用于加密 Azure 服务总线静态数据
Azure 服务总线高级层提供了通过 Azure 存储服务加密 (Azure SSE) 对静态数据进行加密的功能。 服务总线高级层依赖于 Azure 存储来存储数据，默认情况下，存储在 Azure 存储中的所有数据将使用 Microsoft 管理的密钥进行加密。 

## <a name="overview"></a>概述
Azure 服务总线现在支持用户选择通过 Microsoft 管理的密钥或客户管理的密钥（创建自己的密钥 - BYOK）来加密静态数据。 使用此功能可以创建、轮换、禁用用于加密 Azure 服务总线静态数据的客户管理密钥，以及撤销对这些密钥的访问权限。

启用 BYOK 功能是在命名空间中执行的一次性设置过程。

> [!NOTE]
> 对于用于服务端加密的客户管理密钥，需要注意一些事项。 
>   * [Azure 服务总线高级层](service-bus-premium-messaging.md)支持此功能。 不能为标准层服务总线命名空间启用此功能。
>   * 只能为新的或空的命名空间启用加密。 如果命名空间包含任何队列或主题，则加密操作将失败。

可以使用 Azure Key Vault 管理密钥并审核密钥使用情况。 可以创建自己的密钥并将其存储在 Key Vault 中，或者使用 Azure Key Vault API 来生成密钥。 有关 Azure 密钥保管库的详细信息，请参阅[什么是 Azure 密钥保管库？](../key-vault/general/overview.md)

本文介绍了如何使用 Azure 门户配置包含客户管理的密钥的密钥保管库。 若要了解如何使用 Azure 门户创建 Key Vault，请参阅[快速入门：使用 Azure 门户在 Azure Key Vault 中设置和检索机密](../key-vault/secrets/quick-create-portal.md)。

> [!IMPORTANT]
> 为 Azure 服务总线使用客户管理的密钥需要为密钥保管库配置两个必需的属性。 它们具有以下特点：“软删除”和“不清除”。 在 Azure 门户中创建新的 Key Vault 时，默认会启用这些属性。 但是，如果需要针对现有的 Key Vault 启用这些属性，必须使用 PowerShell 或 Azure CLI。

## <a name="enable-customer-managed-keys"></a>启用客户管理的密钥
若要在 Azure 门户中启用客户管理的密钥，请执行以下步骤：

1. 导航到你的服务总线高级层命名空间。
2. 在你的服务总线命名空间的“设置”页上，选择“加密”。
3. 选择“客户管理的密钥加密(静态)”，如下图所示。

    ![启用客户管理的密钥](./media/configure-customer-managed-key/enable-customer-managed-key.png)


## <a name="set-up-a-key-vault-with-keys"></a>设置密钥保管库与密钥

启用客户管理的密钥后，需要将客户管理的密钥关联到 Azure 服务总线命名空间。 服务总线仅支持 Azure Key Vault。 如果启用了上一部分所述的“使用客户管理的密钥进行加密”选项，则需要将密钥导入 Azure Key Vault。 此外，必须为密钥配置“软删除”和“不清除”。 可以使用 [PowerShell](../key-vault/general/soft-delete-powershell.md) 或 [CLI](../key-vault/general/soft-delete-cli.md#enabling-purge-protection) 配置这些设置。

1. 若要创建新的密钥保管库，请遵循 Azure Key Vault [快速入门](../key-vault/general/overview.md)。 有关导入现有密钥的详细信息，请参阅[关于密钥、机密和证书](../key-vault/general/about-keys-secrets-certificates.md)。
1. 若要在创建保管库时启用“软删除”和“清除保护”，请使用 [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) 命令。

    ```azurecli-interactive
    az keyvault create --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. 若要向现有保管库（已启用“软删除”）添加“清除保护”，请使用 [az keyvault update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) 命令。

    ```azurecli-interactive
    az keyvault update --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --enable-purge-protection true
    ```
1. 遵循以下步骤创建密钥：
    1. 若要创建新密钥，请从“设置”  下的“密钥”  菜单中选择“生成/导入”  。
        
        ![选择“生成/导入”按钮](./media/configure-customer-managed-key/select-generate-import.png)

    1. 将“选项”  设置为“生成”  并提供密钥名称。

        ![创建密钥](./media/configure-customer-managed-key/create-key.png) 

    1. 现在，可以从下拉列表中选择要与服务总线命名空间关联的用于加密的密钥。 

        ![从密钥保管库中选择密钥](./media/configure-customer-managed-key/select-key-from-key-vault.png)
        > [!NOTE]
        > 最多可以添加 3 个密钥来实现冗余。 如果某个密钥已过期或不可访问，则会使用其他密钥进行加密。
        
    1. 填写密钥详细信息，然后单击“选择”。 然后，便可以使用客户管理的密钥来加密命名空间中的静态数据。 


    > [!IMPORTANT]
    > 如果你想将客户管理的密钥用于异地灾难恢复，请查看下文 - 
    >
    > 为了将客户管理的密钥用于静态加密，已在指定的 Azure KeyVault 上为服务总线托管标识设置了一个[访问策略](../key-vault/general/secure-your-key-vault.md)。 这可确保能够控制从 Azure 服务总线命名空间对 Azure KeyVault 的访问。
    >
    > 因此：
    > 
    >   * 如果已经为服务总线命名空间启用了[异地灾难恢复](service-bus-geo-dr.md)，并且你想要启用客户管理的密钥，请执行以下操作： 
    >     * 断开配对
    >     * 为密钥保管库[设置针对主要和辅助命名空间的托管标识的访问策略](../key-vault/general/managed-identity.md)。
    >     * 在主要命名空间上设置加密。
    >     * 将主要和辅助命名空间重新配对。
    > 
    >   * 如果想要对已设置客户管理的密钥的服务总线命名空间启用异地灾难恢复，请执行以下操作：
    >     * 为密钥保管库[设置针对辅助命名空间的托管标识的访问策略](../key-vault/general/managed-identity.md)。
    >     * 将主要和辅助命名空间配对。


## <a name="rotate-your-encryption-keys"></a>轮换加密密钥

可以使用 Azure Key Vault 轮换机制来轮换密钥保管库中的密钥。 还可以设置激活和过期日期以自动轮换密钥。 服务总线服务将检测新密钥版本，并自动开始使用新版本。

## <a name="revoke-access-to-keys"></a>撤销对密钥的访问权限

撤销对加密密钥的访问权限不会从服务总线中清除数据。 但是，将无法从服务总线命名空间访问数据。 可以通过使用访问策略或删除密钥来撤销加密密钥。 在[保护对密钥保管库的访问](../key-vault/general/secure-your-key-vault.md)中详细了解访问策略以及如何保护密钥保管库。

撤销加密密钥后，已加密的命名空间中的服务总线服务将无法正常运行。 如果启用了对密钥的访问或者还原了已删除的密钥，则服务总线服务将选取密钥，使你能够从已加密的服务总线命名空间访问数据。

## <a name="use-resource-manager-template-to-enable-encryption"></a>使用资源管理器模板启用加密
本部分介绍了如何使用 **Azure 资源管理器模板**执行以下任务。 

1. 创建具有**托管服务标识**的**高级**服务总线命名空间。
2. 创建**密钥保管库**并向服务标识授予对密钥保管库的访问权限。 
3. 使用密钥保管库信息（密钥/值）更新服务总线命名空间。 


### <a name="create-a-premium-service-bus-namespace-with-managed-service-identity"></a>创建具有托管服务标识的高级服务总线命名空间
本部分介绍了如何使用 Azure 资源管理器模板和 PowerShell 创建具有托管服务标识的 Azure 服务总线命名空间。 

1. 创建 Azure 资源管理器模板，以创建具有托管服务标识的服务总线高级层命名空间。 将文件命名为：**CreateServiceBusPremiumNamespace.json**： 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
             "properties":{
    
             }
          }
       ],
       "outputs":{
          "ServiceBusNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.ServiceBus/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. 创建一个模板参数文件，名为：**CreateServiceBusPremiumNamespaceParams.json**。 

    > [!NOTE]
    > 请替换以下值： 
    > - `<ServiceBusNamespaceName>` - 你的服务总线命名空间的名称
    > - `<Location>` - 你的服务总线命名空间的位置

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    ```
3. 运行以下 PowerShell 命令以部署用于创建高级服务总线命名空间的模板。 然后检索该服务总线命名空间的 ID 供稍后使用。 在运行该命令之前，请将 `{MyRG}` 替换为资源组的名称。  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateServiceBusPremiumNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateServiceBusPremiumNamespace.json -TemplateParameterFile ./CreateServiceBusPremiumNamespaceParams.json
    
    $ServiceBusNamespaceId = $outputs.Outputs["serviceBusNamespaceId"].value
    ```
 
### <a name="grant-service-bus-namespace-identity-access-to-key-vault"></a>向服务总线命名空间标识授予对密钥保管库的访问权限

1. 运行以下命令，以便在启用**清除保护**和**软删除**的情况下创建密钥保管库。 

    ```powershell
    New-AzureRmKeyVault -Name "{keyVaultName}" -ResourceGroupName {RGName}  -Location "{location}" -EnableSoftDelete -EnablePurgeProtection    
    ```
    
    （或者）
    
    运行以下命令来更新**现有的密钥保管库**。 在运行该命令之前，请指定资源组和密钥保管库名称的值。 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. 设置密钥保管库访问策略，使服务总线命名空间的托管标识可以访问密钥保管库中的密钥值。 使用在上一部分创建的服务总线命名空间的 ID。 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $ServiceBusNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-service-bus-namespace-with-customer-managed-key-from-key-vault"></a>使用密钥保管库中客户管理的密钥加密服务总线命名空间中的数据
到目前为止，你已完成以下步骤： 

1. 创建了具有托管标识的高级命名空间。
2. 创建了密钥保管库，并向托管标识授予了对该密钥保管库的访问权限。 

在此步骤中，你将使用密钥保管库信息更新服务总线命名空间。 

1. 创建名为 **UpdateServiceBusNamespaceWithEncryption.json** 的 JSON 文件，其中包含以下内容： 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace to be created in cluster."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          },
          "keyVaultUri":{
             "type":"string",
             "metadata":{
                "description":"URI of the KeyVault."
             }
          },
          "keyName":{
             "type":"string",
             "metadata":{
                "description":"KeyName."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
             "properties":{
                "encryption":{
                   "keySource":"Microsoft.KeyVault",
                   "keyVaultProperties":[
                      {
                         "keyName":"[parameters('keyName')]",
                         "keyVaultUri":"[parameters('keyVaultUri')]"
                      }
                   ]
                }
             }
          }
       ]
    }
    ``` 

2. 创建模板参数文件：**UpdateServiceBusNamespaceWithEncryptionParams.json**。

    > [!NOTE]
    > 请替换以下值： 
    > - `<ServiceBusNamespaceName>` - 你的服务总线命名空间的名称
    > - `<Location>` - 你的服务总线命名空间的位置
    > - `<KeyVaultName>` - 你的密钥保管库的名称
    > - `<KeyName>` - 密钥保管库中密钥的名称  

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          },
          "keyName":{
             "value":"<KeyName>"
          },
          "keyVaultUri":{
             "value":"https://<KeyVaultName>.vault.azure.net"
          }
       }
    }
    ```             
3. 运行以下 PowerShell 命令以部署资源管理器模板。 在运行该命令之前，请将 `{MyRG}` 替换为你的资源组名称。 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateServiceBusNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateServiceBusNamespaceWithEncryption.json -TemplateParameterFile ./UpdateServiceBusNamespaceWithEncryptionParams.json
    ```
    

## <a name="next-steps"></a>后续步骤
请参阅以下文章：
- [服务总线概述](service-bus-messaging-overview.md)
- [Key Vault 概述](../key-vault/general/overview.md)