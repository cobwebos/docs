---
title: 配置自己的密钥以加密静态 Azure 服务总线数据
description: 本文提供有关如何配置自己的密钥以加密 Azure 服务总线数据静态的信息。
services: service-bus-messaging
ms.service: service-bus
documentationcenter: ''
author: axisc
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: aschhab
ms.openlocfilehash: 82a5fbef8c307d60d82b147f04a2a687b8b0433e
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459060"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-service-bus-data-at-rest-by-using-the-azure-portal"></a>配置客户管理密钥，以便使用 Azure 门户加密静态 Azure 服务总线数据
Azure 服务总线高级版通过 Azure 存储服务加密 （Azure SSE） 提供静态数据加密。 服务总线高级版依赖于 Azure 存储来存储数据，默认情况下，与 Azure 存储一起存储的所有数据都使用 Microsoft 管理的密钥进行加密。 

## <a name="overview"></a>概述
Azure 服务总线现在支持使用 Microsoft 管理的密钥或客户管理的密钥（自带密钥 - BYOK）加密静态数据的选项。 此功能使您能够创建、旋转、禁用和撤销对用于在静态时加密 Azure 服务总线的客户管理密钥的访问权限。

启用 BYOK 功能是命名空间上的一次性设置过程。

> [!NOTE]
> 对于服务端加密，客户托管密钥有一些注意事项。 
>   * [Azure 服务总线高级](service-bus-premium-messaging.md)层支持此功能。 无法为标准层服务总线命名空间启用它。
>   * 只能为新或空命名空间启用加密。 如果命名空间包含数据，则加密操作将失败。

您可以使用 Azure 密钥保管库来管理密钥并审核密钥使用情况。 可以创建自己的密钥并将其存储在 Key Vault 中，或者使用 Azure Key Vault API 来生成密钥。 有关 Azure 密钥保管库的详细信息，请参阅[什么是 Azure 密钥保管库？](../key-vault/general/overview.md)

本文演示如何使用 Azure 门户使用客户管理的密钥配置密钥保管库。 若要了解如何使用 Azure 门户创建密钥保管库，请参阅[快速入门：使用 Azure 门户从 Azure 密钥保管库设置和检索机密](../key-vault/secrets/quick-create-portal.md)。

> [!IMPORTANT]
> 将客户管理的密钥与 Azure 服务总线一起使用需要密钥保管库配置两个必需属性。 它们是：**软删除**和**不清除**。 在 Azure 门户中创建新的 Key Vault 时，默认会启用这些属性。 但是，如果需要针对现有的 Key Vault 启用这些属性，必须使用 PowerShell 或 Azure CLI。

## <a name="enable-customer-managed-keys"></a>启用客户管理的密钥
若要在 Azure 门户中启用客户管理的密钥，请执行以下步骤：

1. 导航到服务总线高级命名空间。
2. 在服务总线命名空间的 **"设置"** 页上，选择 **"加密**"。
3. 选择**客户管理密钥静态加密，** 如下图所示。

    ![启用客户托管密钥](./media/configure-customer-managed-key/enable-customer-managed-key.png)


## <a name="set-up-a-key-vault-with-keys"></a>设置带密钥的密钥保管库

启用客户管理的密钥后，需要将客户托管密钥与 Azure 服务总线命名空间相关联。 服务总线仅支持 Azure 密钥保管库。 如果在上一节中启用**了使用客户管理的密钥选项的加密**，则需要将密钥导入 Azure 密钥保管库。 此外，密钥必须为密钥配置 **"软删除****"和"不清除**"。 这些设置可以使用[PowerShell](../key-vault/general/soft-delete-powershell.md)或[CLI](../key-vault/general/soft-delete-cli.md#enabling-purge-protection)进行配置。

1. 要创建新密钥保管库，请按照 Azure 密钥保管库[快速入门](../key-vault/general/overview.md)。 有关导入现有密钥的详细信息，请参阅[有关密钥、机密和证书](../key-vault/about-keys-secrets-and-certificates.md)。
1. 要在创建保管库时同时打开软删除和清除保护，请使用[az 密钥保管库创建](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create)命令。

    ```azurecli-interactive
    az keyvault create --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. 要向现有保管库（已启用软删除）添加清除保护，请使用[az keyvault 更新](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update)命令。

    ```azurecli-interactive
    az keyvault update --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --enable-purge-protection true
    ```
1. 通过以下步骤创建键：
    1. 若要创建新密钥，请从“设置”**** 下的“密钥”**** 菜单中选择“生成/导入”****。
        
        ![选择"生成/导入"按钮](./media/configure-customer-managed-key/select-generate-import.png)

    1. 将“选项”**** 设置为“生成”**** 并提供密钥名称。

        ![创建密钥](./media/configure-customer-managed-key/create-key.png) 

    1. 现在，您可以选择此密钥以与服务总线命名空间关联，以便从下拉列表中进行加密。 

        ![从密钥保管库中选择密钥](./media/configure-customer-managed-key/select-key-from-key-vault.png)
        > [!NOTE]
        > 对于冗余，您最多可以添加 3 个密钥。 如果其中一个密钥已过期或无法访问，则其他密钥将用于加密。
        
    1. 填写密钥的详细信息，然后单击 **"选择**"。 这将启用使用客户托管密钥在命名空间上静态数据加密。 


    > [!IMPORTANT]
    > 如果您希望使用客户托管密钥以及地理灾难恢复，请查看以下 ： 
    >
    > 要使用客户托管密钥启用静态加密，将在指定的 Azure KeyVault 上为服务总线的托管标识设置[访问策略](../key-vault/general/secure-your-key-vault.md)。 这可确保从 Azure 服务总线命名空间对 Azure 密钥库进行受控访问。
    >
    > 因此：
    > 
    >   * 如果服务总线命名空间已启用[了地理灾难恢复](service-bus-geo-dr.md)，并且您希望启用客户托管密钥，则 
    >     * 中断配对
    >     * 为密钥保管库的主命名空间和辅助命名空间的托管标识[设置访问策略](../key-vault/general/managed-identity.md)。
    >     * 在主命名空间上设置加密。
    >     * 重新配对主命名空间和辅助命名空间。
    > 
    >   * 如果要在已设置客户托管密钥的服务总线命名空间启用 Geo-DR，则 -
    >     * 为密钥保管库的辅助命名空间的托管标识[设置访问策略](../key-vault/general/managed-identity.md)。
    >     * 配对主命名空间和辅助命名空间。


## <a name="rotate-your-encryption-keys"></a>旋转加密密钥

可以使用 Azure 密钥保管库轮换机制在密钥保管库中旋转密钥。 有关详细信息，请参阅[设置密钥轮换和审核](../key-vault/secrets/key-rotation-log-monitoring.md)。 激活和到期日期也可以设置为自动轮换密钥。 服务总线服务将检测新的密钥版本，并自动开始使用它们。

## <a name="revoke-access-to-keys"></a>撤销对密钥的访问

撤销对加密密钥的访问不会从服务总线清除数据。 但是，无法从服务总线命名空间访问数据。 您可以通过访问策略或删除密钥来撤消加密密钥。 详细了解访问策略和保护密钥保管库从[安全访问密钥保管库](../key-vault/general/secure-your-key-vault.md)。

一旦加密密钥被吊销，加密命名空间上的服务总线服务将变得无法运行。 如果启用了对密钥的访问或已删除的密钥已恢复，则服务总线服务将选择该密钥，以便您可以从加密的服务总线命名空间访问数据。

## <a name="use-resource-manager-template-to-enable-encryption"></a>使用资源管理器模板启用加密
本节演示如何使用**Azure 资源管理器模板**执行以下任务。 

1. 使用**托管服务标识**创建**高级**服务总线命名空间。
2. 创建**密钥保管库**并授予对密钥保管库的服务标识访问权限。 
3. 使用密钥保管库信息（密钥/值）更新服务总线命名空间。 


### <a name="create-a-premium-service-bus-namespace-with-managed-service-identity"></a>使用托管服务标识创建高级服务总线命名空间
本节演示如何使用 Azure 资源管理器模板和 PowerShell 使用托管服务标识创建 Azure 服务总线命名空间。 

1. 创建 Azure 资源管理器模板以创建具有托管服务标识的服务总线高级层命名空间。 命名文件：**创建服务总线高级命名空间.json**： 

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
2. 创建名为：**创建服务总线高级命名空间Params.json 的**模板参数文件。 

    > [!NOTE]
    > 请替换以下值： 
    > - `<ServiceBusNamespaceName>`- 服务总线命名空间的名称
    > - `<Location>`- 服务总线命名空间的位置

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
3. 运行以下 PowerShell 命令以部署模板以创建高级服务总线命名空间。 然后，检索服务总线命名空间的 ID 以以后使用它。 在`{MyRG}`运行命令之前，请替换为资源组的名称。  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateServiceBusPremiumNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateServiceBusPremiumNamespace.json -TemplateParameterFile ./CreateServiceBusPremiumNamespaceParams.json
    
    $ServiceBusNamespaceId = $outputs.Outputs["serviceBusNamespaceId"].value
    ```
 
### <a name="grant-service-bus-namespace-identity-access-to-key-vault"></a>授予服务总线命名空间对密钥保管库的标识访问

1. 运行以下命令以创建具有**清除保护和****软删除**功能的密钥保管库。 

    ```powershell
    New-AzureRmKeyVault -Name "{keyVaultName}" -ResourceGroupName {RGName}  -Location "{location}" -EnableSoftDelete -EnablePurgeProtection    
    ```
    
    （或者）
    
    运行以下命令以更新**现有密钥保管库**。 在运行命令之前，指定资源组和密钥保管库名称的值。 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. 设置密钥保管库访问策略，以便服务总线命名空间的托管标识可以访问密钥保管库中的密钥值。 使用上一节中的服务总线命名空间的 ID。 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $ServiceBusNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-service-bus-namespace-with-customer-managed-key-from-key-vault"></a>使用密钥保管库的客户托管密钥加密服务总线命名空间中的数据
到目前为止，您已完成以下步骤： 

1. 使用托管标识创建高级命名空间。
2. 创建密钥保管库并授予对密钥保管库的托管标识访问权限。 

在此步骤中，您将使用密钥保管库信息更新服务总线命名空间。 

1. 创建一个名为**更新服务Bus命名空间与加密.json**的JSON文件与以下内容： 

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

2. 创建模板参数文件：**更新服务Bus命名空间与加密帕拉姆斯.json**。

    > [!NOTE]
    > 请替换以下值： 
    > - `<ServiceBusNamespaceName>`- 服务总线命名空间的名称
    > - `<Location>`- 服务总线命名空间的位置
    > - `<KeyVaultName>`- 密钥保管库的名称
    > - `<KeyName>`- 密钥保管库中密钥的名称  

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
3. 运行以下 PowerShell 命令以部署资源管理器模板。 在`{MyRG}`运行命令之前，请替换为资源组的名称。 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateServiceBusNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateServiceBusNamespaceWithEncryption.json -TemplateParameterFile ./UpdateServiceBusNamespaceWithEncryptionParams.json
    ```
    

## <a name="next-steps"></a>后续步骤
请参阅以下文章：
- [服务总线概述](service-bus-messaging-overview.md)
- [密钥保管库概述](../key-vault/general/overview.md)


