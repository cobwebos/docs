---
title: 为静态 Azure 服务总线数据配置你自己的密钥
description: 本文提供了有关如何配置自己的密钥来加密 Azure 服务总线数据的信息。
services: service-bus-messaging
ms.service: service-bus
documentationcenter: ''
author: axisc
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: aschhab
ms.openlocfilehash: aeb9a9730ddc61793e49c9e042906457e0068d9a
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624091"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-service-bus-data-at-rest-by-using-the-azure-portal"></a>使用 Azure 门户配置客户托管的密钥，以便在静态上加密 Azure 服务总线数据
Azure 服务总线高级版通过 Azure 存储服务加密（Azure SSE）提供静态数据的加密。 服务总线高级依赖于 Azure 存储空间来存储数据，默认情况下，使用 Microsoft 托管密钥对存储在 Azure 存储中的所有数据进行加密。 

## <a name="overview"></a>概述
Azure 服务总线现在支持通过 Microsoft 管理的密钥或客户托管的密钥（创建自己的密钥 BYOK）来加密静态数据。 此功能使你能够创建、轮换、禁用和撤消对用于加密 Azure 服务总线的客户托管密钥的访问权限。

启用 BYOK 功能只是在命名空间上进行一次设置。

> [!NOTE]
> 对于服务端加密的客户托管密钥，需要注意一些事项。 
>   * [Azure 服务总线高级](service-bus-premium-messaging.md)层支持此功能。 不能为标准层服务总线命名空间启用此功能。
>   * 只能为新的或空的命名空间启用加密。 如果命名空间包含数据，则加密操作将失败。

你可以使用 Azure Key Vault 来管理密钥并审核密钥用法。 你可以创建自己的密钥并将其存储在密钥保管库中，也可以使用 Azure Key Vault Api 来生成密钥。 有关 Azure 密钥保管库的详细信息，请参阅[什么是 Azure 密钥保管库？](../key-vault/key-vault-overview.md)

本文介绍如何使用 Azure 门户配置包含客户管理密钥的密钥保管库。 若要了解如何使用 Azure 门户创建密钥保管库，请参阅[快速入门：使用 Azure 门户从 Azure Key Vault 设置和检索机密](../key-vault/quick-create-portal.md)。

> [!IMPORTANT]
> 若要将客户托管密钥用于 Azure 服务总线，要求密钥保管库配置了两个必需的属性。 它们是：**软删除**和不**清除**。 默认情况下，在 Azure 门户中创建新的密钥保管库时，将启用这些属性。 但是，如果需要在现有的密钥保管库上启用这些属性，则必须使用 PowerShell 或 Azure CLI。

## <a name="enable-customer-managed-keys"></a>启用客户管理的密钥
若要在 Azure 门户中启用客户托管的密钥，请执行以下步骤：

1. 导航到服务总线高级命名空间。
2. 在服务总线命名空间的 "**设置**" 页上，选择 "**加密**"。
3. 选择**客户托管的密钥加密**，如下图所示。

    ![启用客户托管的密钥](./media/configure-customer-managed-key/enable-customer-managed-key.png)


## <a name="set-up-a-key-vault-with-keys"></a>使用密钥设置密钥保管库

启用客户管理的密钥后，需要将客户托管密钥与 Azure 服务总线命名空间相关联。 Service Bus 仅支持 Azure Key Vault。 如果在上一节中启用 "**使用客户管理的密钥加密**" 选项，则需要将密钥导入 Azure Key Vault。 此外，这些密钥必须包含**软删除**，并且**不会清除**密钥。 可以使用[PowerShell](../key-vault/key-vault-soft-delete-powershell.md)或[CLI](../key-vault/key-vault-soft-delete-cli.md#enabling-purge-protection)配置这些设置。

1. 若要创建新的密钥保管库，请按照 Azure Key Vault[快速入门](../key-vault/key-vault-overview.md)中的步骤进行操作。 有关导入现有密钥的详细信息，请参阅[关于密钥、机密和证书](../key-vault/about-keys-secrets-and-certificates.md)。
1. 若要在创建保管库时启用软删除和清除保护，请使用[az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create)命令。

    ```azurecli-interactive
    az keyvault create --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. 若要将清除保护添加到现有保管库（已启用软删除），请使用[az keyvault update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update)命令。

    ```azurecli-interactive
    az keyvault update --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --enable-purge-protection true
    ```
1. 按照以下步骤创建密钥：
    1. 若要创建新密钥，请从“设置”下的“密钥”菜单中选择“生成/导入”。
        
        ![选择 "生成/导入" 按钮](./media/configure-customer-managed-key/select-generate-import.png)

    1. 将“选项”设置为“生成”并提供密钥名称。

        ![创建密钥](./media/configure-customer-managed-key/create-key.png) 

    1. 你现在可以从下拉列表中选择此密钥以与要进行加密的服务总线命名空间相关联。 

        ![从 key vault 中选择密钥](./media/configure-customer-managed-key/select-key-from-key-vault.png)
        > [!NOTE]
        > 为实现冗余，最多可以添加3个键。 如果其中一个密钥已过期或无法访问，则其他密钥将用于加密。
        
    1. 填写密钥的详细信息，并单击 "**选择**"。 这将允许使用客户管理的密钥加密命名空间上的静态数据。 


    > [!IMPORTANT]
    > 如果你想要使用客户托管密钥以及异地灾难恢复，请查看以下- 
    >
    > 若要使用客户托管密钥启用静态加密，请在指定的 Azure KeyVault 上为服务总线的托管标识设置[访问策略](../key-vault/key-vault-secure-your-key-vault.md)。 这可确保从 Azure 服务总线命名空间控制对 Azure KeyVault 的访问。
    >
    > 原因如下：
    > 
    >   * 如果已为服务总线命名空间启用[异地灾难恢复](service-bus-geo-dr.md)，并且你希望启用客户托管密钥，则 
    >     * 断开配对
    >     * 为密钥保管库的主命名空间和辅助命名空间设置托管标识的[访问策略](../key-vault/managed-identity.md)。
    >     * 在主命名空间上设置加密。
    >     * 重新配对主命名空间和辅助命名空间。
    > 
    >   * 如果你想要在已设置客户托管密钥的服务总线命名空间上启用异地灾难恢复，则-
    >     * 为密钥保管库的辅助命名空间的托管标识[设置访问策略](../key-vault/managed-identity.md)。
    >     * 配对主命名空间和辅助命名空间。


## <a name="rotate-your-encryption-keys"></a>旋转加密密钥

可以通过使用 Azure 密钥保管库旋转机制，在密钥保管库中轮换密钥。 有关详细信息，请参阅[设置密钥轮替和审核](../key-vault/key-vault-key-rotation-log-monitoring.md)。 激活和到期日期还可以设置为自动执行密钥轮换。 Service Bus 服务将检测新的密钥版本，并自动开始使用它们。

## <a name="revoke-access-to-keys"></a>撤消对密钥的访问权限

撤消对加密密钥的访问权限不会从服务总线中清除数据。 但是，无法从服务总线命名空间访问数据。 可以通过访问策略或通过删除密钥来撤消加密密钥。 了解有关访问策略的详细信息，以及如何保护密钥保管库免受[对密钥保管库的安全访问](../key-vault/key-vault-secure-your-key-vault.md)。

撤消加密密钥后，已加密命名空间上的服务总线服务将无法操作。 如果启用了对密钥的访问或还原了删除的密钥，则服务总线服务将选取密钥，以便可以从加密的服务总线命名空间访问数据。

## <a name="use-resource-manager-template-to-enable-encryption"></a>使用资源管理器模板启用加密
本部分演示如何使用**Azure 资源管理器模板**执行以下任务。 

1. 使用**托管服务标识**创建**高级**服务总线命名空间。
2. 创建**密钥保管库**，并向服务标识授予对密钥保管库的访问权限。 
3. 用密钥保管库信息（键/值）更新 Service Bus 命名空间。 


### <a name="create-a-premium-service-bus-namespace-with-managed-service-identity"></a>使用托管服务标识创建高级服务总线命名空间
本部分演示如何使用 Azure 资源管理器模板和 PowerShell 创建具有托管服务标识的 Azure 服务总线命名空间。 

1. 创建 Azure 资源管理器模板来创建具有托管服务标识的服务总线高级层命名空间。 将文件命名为： **CreateServiceBusPremiumNamespace**： 

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
2. 创建名为： **CreateServiceBusPremiumNamespaceParams**的模板参数文件。 

    > [!NOTE]
    > 请替换以下值： 
    > - `<ServiceBusNamespaceName>`-服务总线命名空间的名称
    > - `<Location>`-服务总线命名空间的位置

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
3. 运行以下 PowerShell 命令以部署模板，以创建高级服务总线命名空间。 然后，检索 Service Bus 命名空间的 ID 以在以后使用。 运行命令之前，请将 `{MyRG}` 替换为资源组的名称。  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateServiceBusPremiumNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateServiceBusPremiumNamespace.json -TemplateParameterFile ./CreateServiceBusPremiumNamespaceParams.json
    
    $ServiceBusNamespaceId = $outputs.Outputs["serviceBusNamespaceId"].value
    ```
 
### <a name="grant-service-bus-namespace-identity-access-to-key-vault"></a>向服务总线命名空间标识授予对密钥保管库的访问权限

1. 运行以下命令来创建启用了**清除保护**并启用**软删除**的密钥保管库。 

    ```powershell
    New-AzureRmKeyVault -Name "{keyVaultName}" -ResourceGroupName {RGName}  -Location "{location}" -EnableSoftDelete -EnablePurgeProtection    
    ```
    
    （或者）
    
    运行以下命令更新**现有的密钥保管库**。 在运行命令之前，指定资源组和密钥保管库名称的值。 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. 设置密钥保管库访问策略，使服务总线命名空间的托管标识可以访问密钥保管库中的密钥值。 使用上一节中的服务总线命名空间的 ID。 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $ServiceBusNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-service-bus-namespace-with-customer-managed-key-from-key-vault"></a>通过密钥保管库中的客户托管密钥加密 Service Bus 命名空间中的数据
至此，你已完成以下步骤： 

1. 使用托管标识创建高级命名空间。
2. 创建密钥保管库，并向托管标识授予对密钥保管库的访问权限。 

在此步骤中，将更新包含密钥保管库信息的服务总线命名空间。 

1. 使用以下内容创建名为**UpdateServiceBusNamespaceWithEncryption**的 json 文件： 

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

2. 创建模板参数文件： **UpdateServiceBusNamespaceWithEncryptionParams**。

    > [!NOTE]
    > 请替换以下值： 
    > - `<ServiceBusNamespaceName>`-服务总线命名空间的名称
    > - `<Location>`-服务总线命名空间的位置
    > - `<KeyVaultName>`-密钥保管库的名称
    > - `<KeyName>`-密钥保管库中密钥的名称  

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
3. 运行以下 PowerShell 命令以部署资源管理器模板。 运行命令之前，请将 `{MyRG}` 替换为资源组的名称。 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateServiceBusNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateServiceBusNamespaceWithEncryption.json -TemplateParameterFile ./UpdateServiceBusNamespaceWithEncryptionParams.json
    ```
    

## <a name="next-steps"></a>后续步骤
请参阅以下文章：
- [服务总线概述](service-bus-messaging-overview.md)
- [Key Vault 概述](../key-vault/key-vault-overview.md)


