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
ms.date: 11/20/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 30bd7c68ae1c88aba288b515d0ec32581f90b868
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70088188"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions-preview"></a>使用应用服务和 Azure Functions 的 Key Vault 引用（预览版）

> [!NOTE] 
> Key Vault 引用目前为预览版。

本主题介绍在不需进行任何代码更改的情况下，如何使用应用服务或 Azure Functions 应用程序的 Azure Key Vault 中的机密。 [Azure Key Vault](../key-vault/key-vault-overview.md) 是一项服务，可以提供集中式机密管理，并且可以完全控制访问策略和审核历史记录。

## <a name="granting-your-app-access-to-key-vault"></a>授予应用对 Key Vault 的访问权限

若要从 Key Vault 读取机密，需创建一个保管库并授予应用访问该保管库的权限。

1. 按照 [Key Vault 快速入门](../key-vault/quick-create-cli.md)中的说明创建一个密钥保管库。

1. 为应用程序创建一个[系统分配托管标识](overview-managed-identity.md)。

   > [!NOTE] 
   > Key Vault 引用目前仅支持系统分配托管标识。 不能使用用户分配标识。

1. 在 Key Vault 中为此前创建的应用程序标识创建一项[访问策略](../key-vault/key-vault-secure-your-key-vault.md#key-vault-access-policies)。 在此策略上启用“获取”机密权限。 请勿配置“授权的应用程序”或 `applicationId` 设置，因为这与托管标识不兼容。

    授予对密钥保管库中的应用程序标识的访问权限是一次性操作，并且对于所有 Azure 订阅都将保持相同。 可以使用它部署所需的任意数量的证书。 

## <a name="reference-syntax"></a>引用语法

Key Vault 引用采用 `@Microsoft.KeyVault({referenceString})` 格式，其中 `{referenceString}` 将替换为下述选项之一：

> [!div class="mx-tdBreakAll"]
> | 引用字符串                                                            | 描述                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri=_secretUri_                                                       | **SecretUri** 应该是 Key Vault 中机密的完整数据平面 URI（包括版本），例如 https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931  |
> | VaultName=_vaultName_;SecretName=_secretName_;SecretVersion=_secretVersion_ | **VaultName** 应该是 Key Vault 资源的名称。 **SecretName** 应该是目标机密的名称。 **SecretVersion** 应该是要使用的机密的版本。 |

> [!NOTE] 
> 在当前的预览版中，版本是必需的。 轮换机密时，需在应用程序配置中更新版本。

例如，完整的引用将如下所示：

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931)
```

也可使用以下命令：

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret;SecretVersion=ec96f02080254f109c51a1f14cdb1931)
```


## <a name="source-application-settings-from-key-vault"></a>Key Vault 中的源应用程序设置

Key Vault 引用可以用作[应用程序设置](configure-common.md#configure-app-settings)的值，以便将机密保存在 Key Vault 而不是站点配置中。可以对应用程序设置进行安全的静态加密，但如果需要机密管理功能，则应将它们置于 Key Vault 中。

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
