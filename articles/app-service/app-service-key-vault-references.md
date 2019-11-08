---
title: Key Vault 引用 - Azure 应用服务 | Microsoft Docs
description: Azure 应用服务和 Azure Functions 中的 Azure Key Vault 引用的概念性引用和安装指南
services: app-service
author: mattchenderson
manager: jeconnoc
editor: ''
ms.service: app-service
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/09/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 8f237e4c676a53f6df15940a196a998bee529f6b
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73817970"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions"></a>为应用服务和 Azure Functions 使用 Key Vault 引用

> [!NOTE] 
> Key Vault 引用目前在 Linux 消耗计划中不可用。

本主题介绍在不需进行任何代码更改的情况下，如何使用应用服务或 Azure Functions 应用程序的 Azure Key Vault 中的机密。 [Azure Key Vault](../key-vault/key-vault-overview.md) 是一项服务，可以提供集中式机密管理，并且可以完全控制访问策略和审核历史记录。

## <a name="granting-your-app-access-to-key-vault"></a>授予应用对 Key Vault 的访问权限

若要从 Key Vault 读取机密，需创建一个保管库并授予应用访问该保管库的权限。

1. 按照 [Key Vault 快速入门](../key-vault/quick-create-cli.md)中的说明创建一个密钥保管库。

1. 为应用程序创建一个[系统分配托管标识](overview-managed-identity.md)。

   > [!NOTE] 
   > Key Vault 引用目前仅支持系统分配托管标识。 不能使用用户分配标识。

1. 在 Key Vault 中为此前创建的应用程序标识创建一项[访问策略](../key-vault/key-vault-secure-your-key-vault.md#key-vault-access-policies)。 在此策略上启用“获取”机密权限。 请勿配置“授权的应用程序”或 `applicationId` 设置，因为这与托管标识不兼容。

    > [!NOTE]
    > Key Vault 引用目前不能解析密钥保管库中存储的具有[网络限制](../key-vault/key-vault-overview-vnet-service-endpoints.md)的机密。

## <a name="reference-syntax"></a>引用语法

Key Vault 引用采用 `@Microsoft.KeyVault({referenceString})` 格式，其中 `{referenceString}` 将替换为下述选项之一：

> [!div class="mx-tdBreakAll"]
> | 引用字符串                                                            | 说明                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri=_secretUri_                                                       | **SecretUri** 应该是 Key Vault 中机密的完整数据平面 URI（包括版本），例如 https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931  |
> | VaultName=_vaultName_;SecretName=_secretName_;SecretVersion=_secretVersion_ | **VaultName** 应该是 Key Vault 资源的名称。 **SecretName** 应该是目标机密的名称。 **SecretVersion** 应该是要使用的机密的版本。 |

例如，使用版本的完整引用如下所示：

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931)
```
也可使用以下命令：

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret;SecretVersion=ec96f02080254f109c51a1f14cdb1931)
```


## <a name="source-application-settings-from-key-vault"></a>Key Vault 中的源应用程序设置

Key Vault 引用可以用作[应用程序设置](configure-common.md#configure-app-settings)的值，从而使你可以在 Key Vault 而不是站点配置中保存机密。应用程序设置在静态上进行安全加密，但是，如果你需要机密管理功能，它们应该进入 Key Vault。

若要将 Key Vault 引用用于应用程序设置，请将引用设为设置的值。 应用可以通过密钥正常引用机密。 不需更改代码。

> [!TIP]
> 应该将大多数使用 Key Vault 引用的应用程序设置标记为槽设置，因为你应该为每个环境设置单独的保管库。

### <a name="azure-resource-manager-deployment"></a>Azure 资源管理器部署

通过 Azure 资源管理器模板自动进行资源部署时，可能需要将依赖项按特定的顺序排列，这样才能使该功能发挥作用。 请注意，需将应用程序设置定义为其自己的资源，而不能使用站点定义中的 `siteConfig` 属性。 这是因为，站点需先进行定义，这样才能使用它来创建系统分配标识并将该标识用在访问策略中。

函数应用的示例仿真模板可能如下所示：

```json
{
    //...
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            //...
        },
        {
            "type": "Microsoft.Insights/components",
            "name": "[variables('appInsightsName')]",
            //...
        },
        {
            "type": "Microsoft.Web/sites",
            "name": "[variables('functionAppName')]",
            "identity": {
                "type": "SystemAssigned"
            },
            //...
            "resources": [
                {
                    "type": "config",
                    "name": "appsettings",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('storageConnectionStringName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('appInsightsKeyName'))]"
                    ],
                    "properties": {
                        "AzureWebJobsStorage": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "APPINSIGHTS_INSTRUMENTATIONKEY": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('appInsightsKeyResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_ENABLE_SYNC_UPDATE_SITE": "true"
                        //...
                    }
                },
                {
                    "type": "sourcecontrols",
                    "name": "web",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.Web/sites/config', variables('functionAppName'), 'appsettings')]"
                    ],
                }
            ]
        },
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[variables('keyVaultName')]",
            //...
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]"
            ],
            "properties": {
                //...
                "accessPolicies": [
                    {
                        "tenantId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').tenantId]",
                        "objectId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').principalId]",
                        "permissions": {
                            "secrets": [ "get" ]
                        }
                    }
                ]
            },
            "resources": [
                {
                    "type": "secrets",
                    "name": "[variables('storageConnectionStringName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
                    ],
                    "properties": {
                        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountResourceId'),'2015-05-01-preview').key1)]"
                    }
                },
                {
                    "type": "secrets",
                    "name": "[variables('appInsightsKeyName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
                    ],
                    "properties": {
                        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
                    }
                }
            ]
        }
    ]
}
```

> [!NOTE] 
> 在此示例中，源代码管理部署取决于应用程序设置。 这通常是不安全的行为，因为应用设置更新是以异步方式表现的。 不过，由于我们已包括 `WEBSITE_ENABLE_SYNC_UPDATE_SITE` 应用程序设置，因此更新是同步的。 这意味着源代码管理部署只有在应用程序设置已完全更新后才会开始。

## <a name="troubleshooting-key-vault-references"></a>Key Vault 引用疑难解答

如果引用未正确解析，则将改用引用值。 这意味着，对于应用程序设置，将创建值具有 `@Microsoft.KeyVault(...)` 语法的环境变量。 这可能导致应用程序引发错误，因为它需要特定结构的机密。

最常见的原因是， [Key Vault 访问策略](#granting-your-app-access-to-key-vault)的配置错误。 但是，这也可能是由于机密已不再存在，或者引用本身中存在语法错误。

如果语法正确，可以通过在门户中检查当前解决方案状态来查看其他错误原因。 导航到 "应用程序设置"，然后选择 "编辑" 以获取相关引用。 在设置配置下面，应会看到状态信息，包括任何错误。 缺少这一点意味着引用语法无效。

你还可以使用某个内置检测程序来获取其他信息。

### <a name="using-the-detector-for-app-service"></a>使用应用程序服务的检测程序

1. 在门户中，导航到你的应用。
2. 选择 "**诊断并解决问题**"。
3. 选择 "**可用性和性能**"，然后选择 " **Web 应用"。**
4. 查找**Key Vault 应用程序设置诊断**，并单击 "**详细信息**"。


### <a name="using-the-detector-for-azure-functions"></a>使用探测器进行 Azure Functions

1. 在门户中，导航到你的应用。
2. 导航到 "**平台功能"。**
3. 选择 "**诊断并解决问题**"。
4. 选择 "**可用性和性能**"，然后选择 "**函数应用关闭" 或 "报告错误"。**
5. 单击 " **Key Vault 应用程序设置**" "诊断"。
