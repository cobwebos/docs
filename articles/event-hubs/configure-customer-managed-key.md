---
title: 配置您自己的密钥以加密静态 Azure 事件中心数据
description: 本文提供有关如何配置自己的密钥以加密 Azure 事件中心数据静态的信息。
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: spelluru
ms.openlocfilehash: f515d3ad832db7f78f98111ab67628a2874033ff
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459128"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-event-hubs-data-at-rest-by-using-the-azure-portal"></a>配置客户管理的密钥，以便使用 Azure 门户加密静态 Azure 事件中心数据
Azure 事件中心通过 Azure 存储服务加密 （Azure SSE） 提供静态数据加密。 事件中心依赖于 Azure 存储来存储数据，默认情况下，与 Azure 存储一起存储的所有数据都使用 Microsoft 管理的密钥进行加密。 

## <a name="overview"></a>概述
Azure 事件中心现在支持使用 Microsoft 管理的密钥或客户管理的密钥（自带密钥 + BYOK）加密静态数据的选项。 此功能使您能够创建、旋转、禁用和撤销对用于加密静态 Azure 事件中心数据的客户托管密钥的访问权限。

启用 BYOK 功能是命名空间上的一次性设置过程。

> [!NOTE]
> BYOK 功能由[事件中心专用单租户](event-hubs-dedicated-overview.md)群集支持。 无法为标准事件中心命名空间启用它。

您可以使用 Azure 密钥保管库来管理密钥并审核密钥使用情况。 可以创建自己的密钥并将其存储在 Key Vault 中，或者使用 Azure Key Vault API 来生成密钥。 有关 Azure 密钥保管库的详细信息，请参阅[什么是 Azure 密钥保管库？](../key-vault/general/overview.md)

本文演示如何使用 Azure 门户使用客户管理的密钥配置密钥保管库。 若要了解如何使用 Azure 门户创建密钥保管库，请参阅[快速入门：使用 Azure 门户从 Azure 密钥保管库设置和检索机密](../key-vault/secrets/quick-create-portal.md)。

> [!IMPORTANT]
> 将客户管理的密钥与 Azure 事件中心一起使用需要密钥保管库配置两个必需属性。 它们是：**软删除**和**不清除**。 在 Azure 门户中创建新的 Key Vault 时，默认会启用这些属性。 但是，如果需要针对现有的 Key Vault 启用这些属性，必须使用 PowerShell 或 Azure CLI。

## <a name="enable-customer-managed-keys"></a>启用客户管理的密钥
若要在 Azure 门户中启用客户管理的密钥，请执行以下步骤：

1. 导航到事件中心专用群集。
1. 选择要在其上启用 BYOK 的命名空间。
1. 在事件中心命名空间的 **"设置"** 页上，选择 **"加密**"。 
1. 选择**客户管理密钥静态加密，** 如下图所示。 

    ![启用客户托管密钥](./media/configure-customer-managed-key/enable-customer-managed-key.png)

## <a name="set-up-a-key-vault-with-keys"></a>设置带密钥的密钥保管库
启用客户管理的密钥后，需要将客户托管密钥与 Azure 事件中心命名空间相关联。 事件中心仅支持 Azure 密钥保管库。 如果在上一节中启用**了使用客户管理的密钥选项的加密**，则需要将密钥导入 Azure 密钥保管库。 此外，密钥必须为密钥配置 **"软删除****"和"不清除**"。 这些设置可以使用[PowerShell](../key-vault/general/soft-delete-powershell.md)或[CLI](../key-vault/general/soft-delete-cli.md#enabling-purge-protection)进行配置。

1. 要创建新密钥保管库，请按照 Azure 密钥保管库[快速入门](../key-vault/general/overview.md)。 有关导入现有密钥的详细信息，请参阅[有关密钥、机密和证书](../key-vault/about-keys-secrets-and-certificates.md)。
1. 要在创建保管库时同时打开软删除和清除保护，请使用[az 密钥保管库创建](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create)命令。

    ```azurecli-interactive
    az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. 要向现有保管库（已启用软删除）添加清除保护，请使用[az keyvault 更新](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update)命令。

    ```azurecli-interactive
    az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
    ```
1. 通过以下步骤创建键：
    1. 若要创建新密钥，请从“设置”**** 下的“密钥”**** 菜单中选择“生成/导入”****。
        
        ![选择"生成/导入"按钮](./media/configure-customer-managed-key/select-generate-import.png)
    1. 将“选项”**** 设置为“生成”**** 并提供密钥名称。

        ![创建密钥](./media/configure-customer-managed-key/create-key.png) 
    1. 现在，您可以选择此密钥以与事件中心命名空间关联，以便从下拉列表中进行加密。 

        ![从密钥保管库中选择密钥](./media/configure-customer-managed-key/select-key-from-key-vault.png)
    1. 填写密钥的详细信息，然后单击 **"选择**"。 这将启用使用客户托管密钥在命名空间上静态数据加密。 


## <a name="rotate-your-encryption-keys"></a>旋转加密密钥
可以使用 Azure 密钥保管库轮换机制在密钥保管库中旋转密钥。 有关详细信息，请参阅[设置密钥轮换和审核](../key-vault/secrets/key-rotation-log-monitoring.md)。 激活和到期日期也可以设置为自动轮换密钥。 事件中心服务将检测新的密钥版本，并自动开始使用它们。

## <a name="revoke-access-to-keys"></a>撤销对密钥的访问
撤销对加密密钥的访问不会从事件中心清除数据。 但是，无法从事件中心命名空间访问数据。 您可以通过访问策略或删除密钥来撤消加密密钥。 详细了解访问策略和保护密钥保管库从[安全访问密钥保管库](../key-vault/general/secure-your-key-vault.md)。

一旦加密密钥被吊销，加密命名空间上的事件中心服务将变得无法运行。 如果启用了对密钥的访问或已还原删除密钥，事件中心服务将选取该密钥，以便可以从加密的事件中心命名空间访问数据。

## <a name="set-up-diagnostic-logs"></a>设置诊断日志 
为启用 BYOK 的命名空间设置诊断日志可提供有关命名空间使用客户管理的密钥加密时操作所需的信息。 这些日志可以启用，然后流式传输到事件中心，或通过日志分析进行分析，或流式传输到存储以执行自定义分析。 要了解有关诊断日志的详细信息，请参阅[Azure 诊断日志概述](../azure-monitor/platform/platform-logs-overview.md)。

## <a name="enable-user-logs"></a>启用用户日志
按照以下步骤启用客户管理密钥的日志。

1. 在 Azure 门户中，导航到启用了 BYOK 的命名空间。
1. 选择 **"监视**"下的**诊断设置**。

    ![选择诊断设置](./media/configure-customer-managed-key/select-diagnostic-settings.png)
1. 选择 **+添加诊断设置**。 

    ![选择添加诊断设置](./media/configure-customer-managed-key/select-add-diagnostic-setting.png)
1. 提供**名称**并选择要将日志流式传输到的位置。
1. 选择**客户管理密钥用户日志**并**保存**。 此操作启用命名空间上的 BYOK 日志。

    ![选择客户管理的关键用户日志选项](./media/configure-customer-managed-key/select-customer-managed-key-user-logs.png)

## <a name="log-schema"></a>日志架构 
所有日志均以 JavaScript 对象表示法 (JSON) 格式存储。 每个条目都有使用下表中描述的格式的字符串字段。 

| 名称 | 说明 |
| ---- | ----------- | 
| TaskName | 失败的任务的说明。 |
| ActivityId | 用于跟踪的内部 ID。 |
| category | 定义任务的分类。 例如，如果禁用密钥保管库中的密钥，则该密钥将是信息类别，或者如果无法解包密钥，则该密钥可能会出错。 |
| resourceId | Azure 资源管理器资源 ID |
| 键库 | 密钥保管库的全名。 |
| key | 用于加密事件中心命名空间的密钥名称。 |
| 版本 | 正在使用的密钥的版本。 |
| operation | 对密钥保管库中的密钥执行的操作。 例如，禁用/启用密钥、换行或取消包装 |
| 代码 | 与操作关联的代码。 示例：错误代码 404 表示未找到密钥。 |
| message | 与操作关联的任何错误消息 |

下面是客户托管密钥的日志示例：

```json
{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111-1111-1111-1111-111111111111",
   "category": "error"
   "resourceId": "/SUBSCRIPTIONS/11111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "1111111111111111111111111111111",
   "operation": "wrapKey",
   "code": "404",
   "message": "Key not found: ehbyok0/111111111111111111111111111111",
}



{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111111111-1111-1111-1111111111111",
   "category": "info"
   "resourceId": "/SUBSCRIPTIONS/111111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "111111111111111111111111111111",
   "operation": "disable" | "restore",
   "code": "",
   "message": "",
}
```

## <a name="use-resource-manager-template-to-enable-encryption"></a>使用资源管理器模板启用加密
本节演示如何使用**Azure 资源管理器模板**执行以下任务。 

1. 使用托管服务标识创建**事件中心命名空间**。
2. 创建**密钥保管库**并授予对密钥保管库的服务标识访问权限。 
3. 使用密钥保管库信息（键/值）更新事件中心命名空间。 


### <a name="create-an-event-hubs-cluster-and-namespace-with-managed-service-identity"></a>使用托管服务标识创建事件中心群集和命名空间
本节演示如何使用 Azure 资源管理器模板和 PowerShell 使用托管服务标识创建 Azure 事件中心命名空间。 

1. 创建 Azure 资源管理器模板以创建具有托管服务标识的事件中心命名空间。 命名文件：**创建事件HubCluster和命名空间.json**： 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
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
          }
       },
       "resources":[
          {
             "type":"Microsoft.EventHub/clusters",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('clusterName')]",
             "location":"[parameters('location')]",
             "sku":{
                "name":"Dedicated",
                "capacity":1
             }
          },
          {
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             },
             "dependsOn":[
                "[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             ]
          }
       ],
       "outputs":{
          "EventHubNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.EventHub/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. 创建名为：**创建事件HubCluster和命名空间帕拉森.json 的**模板参数文件。 

    > [!NOTE]
    > 请替换以下值： 
    > - `<EventHubsClusterName>`- 事件中心群集的名称    
    > - `<EventHubsNamespaceName>`- 事件中心命名空间的名称
    > - `<Location>`- 事件中心命名空间的位置

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    
    ```
3. 运行以下 PowerShell 命令以部署模板以创建事件中心命名空间。 然后，检索事件中心命名空间的 ID 以以后使用它。 在`{MyRG}`运行命令之前，请替换为资源组的名称。  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateEventHubClusterAndNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateEventHubClusterAndNamespace.json -TemplateParameterFile ./CreateEventHubClusterAndNamespaceParams.json

    $EventHubNamespaceId = $outputs.Outputs["eventHubNamespaceId"].value
    ```
 
### <a name="grant-event-hubs-namespace-identity-access-to-key-vault"></a>授予事件中心命名空间对密钥保管库的标识访问

1. 运行以下命令以创建具有**清除保护和****软删除**功能的密钥保管库。 

    ```powershell
    New-AzureRmKeyVault -Name {keyVaultName} -ResourceGroupName {RGName}  -Location {location} -EnableSoftDelete -EnablePurgeProtection    
    ```     
    
    （或者）    
    
    运行以下命令以更新**现有密钥保管库**。 在运行命令之前，指定资源组和密钥保管库名称的值。 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. 设置密钥保管库访问策略，以便事件中心命名空间的托管标识可以访问密钥保管库中的键值。 使用上一节中的事件中心命名空间的 ID。 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $EventHubNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-event-hubs-namespace-with-customer-managed-key-from-key-vault"></a>使用密钥保管库的客户托管密钥对事件中心命名空间中的数据进行加密
到目前为止，您已完成以下步骤： 

1. 使用托管标识创建高级命名空间。
2. 创建密钥保管库并授予对密钥保管库的托管标识访问权限。 

在此步骤中，您将使用密钥保管库信息更新事件中心命名空间。 

1. 创建名为**CreateEventHubCluster 和Namespace.json**的 JSON 文件，内容如下： 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
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
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]",
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

2. 创建模板参数文件：**更新事件HubCluster和命名空间帕拉森.** 

    > [!NOTE]
    > 请替换以下值： 
    > - `<EventHubsClusterName>`- 事件中心群集的名称。        
    > - `<EventHubsNamespaceName>`- 事件中心命名空间的名称
    > - `<Location>`- 事件中心命名空间的位置
    > - `<KeyVaultName>`- 密钥保管库的名称
    > - `<KeyName>`- 密钥保管库中密钥的名称

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
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
    New-AzResourceGroupDeployment -Name UpdateEventHubNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateEventHubClusterAndNamespace.json -TemplateParameterFile ./UpdateEventHubClusterAndNamespaceParams.json 
    ```

## <a name="troubleshoot"></a>疑难解答
最佳做法是，始终启用上一节所示的日志。 它有助于在启用 BYOK 加密时跟踪活动。 它还有助于缩小问题的范围。

以下是启用 BYOK 加密时要查找的常见错误代码。

| 操作 | 错误代码 | 结果的数据状态 |
| ------ | ---------- | ----------------------- | 
| 从密钥保管库中删除换行/解包权限 | 403 |    Inaccessible |
| 从授予换行/解包权限的 AAD 主体中删除 AAD 角色成员身份 | 403 |  Inaccessible |
| 从密钥保管库中删除加密密钥 | 404 | Inaccessible |
| 删除密钥保管库 | 404 | 无法访问（假定启用了软删除，这是必需的设置。 |
| 更改加密密钥的过期期限，使其已过期 | 403 |   Inaccessible  |
| 更改 NBF（以前不），以便密钥加密密钥不处于活动状态 | 403 | Inaccessible  |
| 为密钥保管库防火墙选择"**允许 MSFT 服务**"选项，或者阻止网络访问具有加密密钥的密钥保管库 | 403 | Inaccessible |
| 将密钥保管库移动到其他租户 | 404 | Inaccessible |  
| 间歇性网络问题或 DNS/AAD/MSI 中断 |  | 使用缓存的数据加密密钥访问 |

> [!IMPORTANT]
> 要在使用 BYOK 加密的命名空间上启用 Geo-DR，用于配对的辅助命名空间必须位于专用群集中，并且必须启用系统分配的托管标识。 要了解更多信息，请参阅[Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)。

## <a name="next-steps"></a>后续步骤
请参阅以下文章：
- [事件中心概述](event-hubs-about.md)
- [密钥保管库概述](../key-vault/general/overview.md)




