---
title: 在 Azure API 管理中使用托管标识 | Microsoft Docs
description: 了解如何使用 Azure 门户、PowerShell 和资源管理器模板在 API 管理中创建系统分配的和用户分配的标识。
services: api-management
documentationcenter: ''
author: miaojiang
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/12/2020
ms.author: apimpm
ms.openlocfilehash: 8a7fa295bdc8881c0c1ba58c95872a9380231b81
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85558027"
---
# <a name="use-managed-identities-in-azure-api-management"></a>在 Azure API 管理中使用托管标识

本文介绍如何为 Azure API 管理实例创建托管标识以及如何访问其他资源。 借助 Azure Active Directory (Azure AD) 生成的托管标识，API 管理实例可以轻松、安全访问其他受 Azure AD 保护的资源（如 Azure Key Vault）。 Azure 管理此标识，因此你无需预配或轮换任何机密。 有关托管标识的详细信息，请参阅[Azure 资源的托管标识是什么？](../active-directory/managed-identities-azure-resources/overview.md)。

你可以向 API 管理实例授予两种类型的标识：

- *系统分配的标识*将绑定到服务，如果服务已删除，则会将其删除。 服务只能有一个系统分配的标识。
- *用户分配的标识*是可分配给服务的独立 Azure 资源。 该服务可以有多个用户分配的标识。

## <a name="create-a-system-assigned-managed-identity"></a>创建系统分配的托管标识

### <a name="azure-portal"></a>Azure 门户

若要在 Azure 门户中设置托管标识，首先要创建 API 管理实例，然后启用该功能。

1. 按常规在门户中创建 API 管理实例。 在门户中浏览到该网站。
2. 选择 "**托管标识**"。
3. 在 "**系统分配**" 选项卡上，将 "**状态**" 切换到 **"打开"**。 选择“保存”。

    :::image type="content" source="./media/api-management-msi/enable-system-msi.png" alt-text="选择启用系统分配的托管标识" border="true":::


### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

以下步骤将引导创建 API 管理实例，并使用 Azure PowerShell 向其分配标识。 

1. 如果需要，请按照[Azure PowerShell 指南](/powershell/azure/install-az-ps)中的说明安装 Azure PowerShell。 然后运行 `Connect-AzAccount` 以创建与 Azure 的连接。

2. 使用以下代码创建实例。 有关如何在 API 管理实例中使用 Azure PowerShell 的更多示例，请参阅[Api 管理 PowerShell 示例](powershell-samples.md)。

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an API Management Consumption Sku service.
    New-AzApiManagement -ResourceGroupName $resourceGroupName -Name consumptionskuservice -Location $location -Sku Consumption -Organization contoso -AdminEmail contoso@contoso.com -SystemAssignedIdentity
    ```

3. 更新现有实例以创建标识：

    ```azurepowershell-interactive
    # Get an API Management instance
    $apimService = Get-AzApiManagement -ResourceGroupName $resourceGroupName -Name $apiManagementName

    # Update an API Management instance
    Set-AzApiManagement -InputObject $apimService -SystemAssignedIdentity
    ```

### <a name="azure-resource-manager-template"></a>Azure 资源管理器模板

在资源定义中包括以下属性，可以创建具有标识的 API 管理实例：

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

此属性将告知 Azure 为 API 管理实例创建和管理标识。

例如，完整的 Azure 资源管理器模板可能如下所示：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "resources": [{
        "apiVersion": "2019-01-01",
        "name": "contoso",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {},
        "sku": {
            "name": "Developer",
            "capacity": "1"
        },
        "properties": {
            "publisherEmail": "admin@contoso.com",
            "publisherName": "Contoso"
        },
        "identity": {
            "type": "systemAssigned"
        }
    }]
}
```

创建实例后，它具有以下附加属性：

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

`tenantId`属性标识标识所属 Azure AD 租户。 `principalId`属性是实例的新标识的唯一标识符。 在 Azure AD 中，服务主体的名称与你为 API 管理实例提供的名称相同。


> [!NOTE]
> API 管理实例可以同时具有系统分配的标识和用户分配的标识。 在这种情况下， `type` 属性将为 `SystemAssigned,UserAssigned` 。

### <a name="supported-scenarios"></a>支持的方案

#### <a name="obtain-a-custom-tlsssl-certificate-for-the-api-management-instance-from-azure-key-vault"></a><a name="use-ssl-tls-certificate-from-azure-key-vault"></a>从 Azure Key Vault 获取 API 管理实例的自定义 TLS/SSL 证书
你可以使用 API 管理实例的系统分配的标识来检索存储在 Azure Key Vault 中的自定义 TLS/SSL 证书。 然后，你可以将这些证书分配到 API 管理实例中的自定义域。 请记住以下注意事项：

- 机密的内容类型必须为*application/x-pkcs12*。
- 使用包含机密的 Key Vault 证书机密终结点。

> [!Important]
> 如果未提供证书的对象版本，在 Key Vault 中更新后，API 管理将在4小时内自动获取证书的较新版本。

以下示例显示包含以下步骤的 Azure 资源管理器模板：

1. 创建具有托管标识的 API 管理实例。
2. 更新 Azure Key Vault 实例的访问策略，并允许 API 管理实例从中获取机密。
3. 通过 Key Vault 实例中的证书设置自定义域名来更新 API 管理实例。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publisherEmail": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "The email address of the owner of the service"
            }
        },
        "publisherName": {
            "type": "string",
            "defaultValue": "Contoso",
            "minLength": 1,
            "metadata": {
                "description": "The name of the owner of the service"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": ["Developer",
            "Standard",
            "Premium"],
            "defaultValue": "Developer",
            "metadata": {
                "description": "The pricing tier of this API Management instance"
            }
        },
        "skuCount": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The instance size of this API Management instance."
            }
        },
        "keyVaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the vault"
            }
        },
        "proxyCustomHostname1": {
            "type": "string",
            "metadata": {
                "description": "Proxy Custom hostname."
            }
        },
        "keyVaultIdToCertificate": {
            "type": "string",
            "metadata": {
                "description": "Reference to the Key Vault certificate. https://contoso.vault.azure.net/secrets/contosogatewaycertificate."
            }
        }
    },
    "variables": {
        "apiManagementServiceName": "[concat('apiservice', uniqueString(resourceGroup().id))]",
        "apimServiceIdentityResourceId": "[concat(resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName')),'/providers/Microsoft.ManagedIdentity/Identities/default')]"
    },
    "resources": [{
        "apiVersion": "2019-01-01",
        "name": "[variables('apiManagementServiceName')]",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {
        },
        "sku": {
            "name": "[parameters('sku')]",
            "capacity": "[parameters('skuCount')]"
        },
        "properties": {
            "publisherEmail": "[parameters('publisherEmail')]",
            "publisherName": "[parameters('publisherName')]"
        },
        "identity": {
            "type": "systemAssigned"
        }
    },
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2015-06-01",
        "dependsOn": [
            "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
        ],
        "properties": {
            "accessPolicies": [{
                "tenantId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').tenantId]",
                "objectId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').principalId]",
                "permissions": {
                    "secrets": ["get"]
                }
            }]
        }
    },
    {
        "apiVersion": "2017-05-10",
        "name": "apimWithKeyVault",
        "type": "Microsoft.Resources/deployments",
        "dependsOn": [
        "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
        ],
        "properties": {
            "mode": "incremental",
            "templateLink": {
                "uri": "https://raw.githubusercontent.com/solankisamir/arm-templates/master/basicapim.keyvault.json",
                "contentVersion": "1.0.0.0"
            },
            "parameters": {
                "publisherEmail": { "value": "[parameters('publisherEmail')]"},
                "publisherName": { "value": "[parameters('publisherName')]"},
                "sku": { "value": "[parameters('sku')]"},
                "skuCount": { "value": "[parameters('skuCount')]"},
                "proxyCustomHostname1": {"value" : "[parameters('proxyCustomHostname1')]"},
                "keyVaultIdToCertificate": {"value" : "[parameters('keyVaultIdToCertificate')]"}
            }
        }
    }]
}
```

#### <a name="authenticate-to-the-back-end-by-using-an-api-management-identity"></a>使用 API 管理标识向后端进行身份验证

您可以使用系统分配的标识通过[身份验证托管标识](api-management-authentication-policies.md#ManagedIdentity)策略向后端进行身份验证。


## <a name="create-a-user-assigned-managed-identity"></a>创建用户分配的托管标识

> [!NOTE]
> 可以将 API 管理实例与最多10个用户分配的托管标识相关联。

### <a name="azure-portal"></a>Azure 门户

若要在门户中设置托管标识，首先需要创建一个 API 管理实例，然后再启用该功能。

1. 按常规在门户中创建 API 管理实例。 在门户中浏览到该网站。
2. 选择 "**托管标识**"。
3. 在 "**用户分配**" 选项卡上，选择 "**添加**"。
4. 搜索之前创建的标识，然后选择它。 选择 **添加** 。

   :::image type="content" source="./media/api-management-msi/enable-user-assigned-msi.png" alt-text="用于启用用户分配的托管标识的选择" border="true":::

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

以下步骤将引导创建 API 管理实例，并使用 Azure PowerShell 向其分配标识。 

1. 如果需要，请按照[Azure PowerShell 指南](/powershell/azure/install-az-ps)中的说明安装 Azure PowerShell。 然后运行 `Connect-AzAccount` 以创建与 Azure 的连接。

2. 使用以下代码创建实例。 有关如何在 API 管理实例中使用 Azure PowerShell 的更多示例，请参阅[Api 管理 PowerShell 示例](powershell-samples.md)。

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Create an API Management Consumption Sku service.
    $userIdentities = @($userAssignedIdentity.Id)

    New-AzApiManagement -ResourceGroupName $resourceGroupName -Location $location -Name $apiManagementName -Organization contoso -AdminEmail admin@contoso.com -Sku Consumption -UserAssignedIdentity $userIdentities
    ```

3. 更新现有服务以将标识分配给服务：

    ```azurepowershell-interactive
    # Get an API Management instance
    $apimService = Get-AzApiManagement -ResourceGroupName $resourceGroupName -Name $apiManagementName

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Update an API Management instance
    $userIdentities = @($userAssignedIdentity.Id)
    Set-AzApiManagement -InputObject $apimService -UserAssignedIdentity $userIdentities
    ```

### <a name="azure-resource-manager-template"></a>Azure 资源管理器模板

在资源定义中包括以下属性，可以创建具有标识的 API 管理实例：

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}
```

添加用户分配的类型会告知 Azure 使用为实例指定的用户分配的标识。

例如，完整的 Azure 资源管理器模板可能如下所示：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "resources": [{
        "apiVersion": "2019-12-01",
        "name": "contoso",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {},
        "sku": {
            "name": "Developer",
            "capacity": "1"
        },
        "properties": {
            "publisherEmail": "admin@contoso.com",
            "publisherName": "Contoso"
        },
        "identity": {
            "type": "UserAssigned",
             "userAssignedIdentities": {
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]": {}
             }
        },
        "dependsOn": [       
          "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
        ]
    }]
}
```

创建服务时，它具有以下附加属性：

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {
            "principalId": "<PRINCIPALID>",
            "clientId": "<CLIENTID>"
        }
    }
}
```

`principalId`属性是用于 Azure AD 管理的标识的唯一标识符。 `clientId`属性是应用程序的新标识的唯一标识符，用于指定运行时调用期间要使用的标识。

> [!NOTE]
> API 管理实例可以同时具有系统分配的标识和用户分配的标识。 在这种情况下， `type` 属性将为 `SystemAssigned,UserAssigned` 。

### <a name="supported-scenarios"></a>支持的方案

#### <a name="authenticate-to-the-back-end-by-using-a-user-assigned-identity"></a>使用用户分配的标识向后端进行身份验证

您可以使用用户分配的标识通过[身份验证托管标识](api-management-authentication-policies.md#ManagedIdentity)策略向后端进行身份验证。


## <a name="remove-an-identity"></a><a name="remove"></a>删除标识

你可以通过门户或 Azure 资源管理器模板禁用该功能，方法与创建它时的方式相同。 可以单独删除用户分配的标识。 若要删除所有标识，请将标识类型设置为 `"None"` 。

以这种方式删除系统分配的标识也会将它从 Azure AD 中删除。 删除 API 管理实例时，系统分配的标识也会自动从 Azure AD 中删除。

若要使用 Azure 资源管理器模板删除所有标识，请更新此部分：

```json
"identity": {
    "type": "None"
}
```

> [!Important]
> 如果使用 Key Vault 中的自定义 SSL 证书配置了 API 管理实例，则尝试禁用托管标识时，请求将失败。
>
> 你可以通过将 Azure Key Vault 证书切换到内联编码的证书，然后禁用托管标识来取消阻止。 有关详细信息，请参阅[配置自定义域名](configure-custom-domain.md)。

## <a name="next-steps"></a>后续步骤

详细了解 Azure 资源的托管标识：

* [什么是 Azure 资源的托管标识？](../active-directory/managed-identities-azure-resources/overview.md)
* [Azure Resource Manager 模板](https://github.com/Azure/azure-quickstart-templates)
* [在策略中使用托管标识进行身份验证](./api-management-authentication-policies.md#ManagedIdentity)
