---
title: 在 Azure API 管理中使用 Azure 托管服务标识 | Microsoft Docs
description: 了解如何在 API 管理中使用 Azure 托管服务标识
services: api-management
documentationcenter: ''
author: miaojiang
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/18/2017
ms.author: apimpm
ms.openlocfilehash: 98aa70935a3efbbe2edb07aade85fa3ea17ce786
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
ms.locfileid: "32150424"
---
# <a name="use-azure-managed-service-identity-in-azure-api-management"></a>在 Azure API 管理中使用 Azure 托管服务标识

本文说明如何创建 API 管理服务实例的托管服务标识以及如何访问其他资源。 借助 Azure Active Directory (Azure AD) 生成的托管服务标识，API 管理实例可以轻松、安全访问其他受 Azure AD 保护的资源（如 Azure Key Vault）。 此托管服务标识由 Azure 托管，无需设置或转交任何机密。 有关 Azure 托管服务标识的详细信息，请参阅 [Azure 资源的托管服务标识](../active-directory/msi-overview.md)。

## <a name="create-a-managed-service-identity-for-an-api-management-instance"></a>为 API 管理实例创建托管服务标识

### <a name="using-the-azure-portal"></a>使用 Azure 门户

若要在门户中设置托管服务标识，需先按常规创建 API 管理实例，然后启用该功能。

1. 按常规在门户中创建 API 管理实例。 在门户中导航到该应用。
2. 选择“托管服务标识”。
3. 将“使用 Azure Active Directory 注册”切换至“打开”。 单击“保存”。

![启用 MSI](./media/api-management-msi/enable-msi.png)

### <a name="using-the-azure-resource-manager-template"></a>使用 Azure 资源管理器模板

在资源定义中包括以下属性，可以创建具有标识的 API 管理实例： 

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

这将告知 Azure 为 API 管理实例创建和管理标识。 

例如，完整的 Azure 资源管理器模板可能如下所示：

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0"
    },
    "resources": [
        {
            "apiVersion": "2017-03-01",
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
        }
    ]
}
```
## <a name="use-the-managed-service-identity-to-access-other-resources"></a>使用托管服务标识访问其他资源

> [!NOTE]
> 目前，托管服务标识可用于从 Azure Key Vault 为 API 管理自定义域名获取证书。 不久后会支持更多方案。
> 
>


### <a name="obtain-a-certificate-from-azure-key-vault"></a>从 Azure Key Vault 中获取证书

#### <a name="prerequisites"></a>先决条件
1. 包含 pfx 证书的 Key Vault 必须与 API 管理服务在同一 Azure 订阅和同一资源组中。 这是 Azure 资源管理器模板的要求。 
2. 机密的内容类型必须是 *application/x-pkcs12*。 可以使用以下脚本上传证书：

```powershell
$pfxFilePath = "PFX_CERTIFICATE_FILE_PATH" # Change this path 
$pwd = "PFX_CERTIFICATE_PASSWORD" # Change this password 
$flag = [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable 
$collection = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2Collection 
$collection.Import($pfxFilePath, $pwd, $flag) 
$pkcs12ContentType = [System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12 
$clearBytes = $collection.Export($pkcs12ContentType) 
$fileContentEncoded = [System.Convert]::ToBase64String($clearBytes) 
$secret = ConvertTo-SecureString -String $fileContentEncoded -AsPlainText –Force 
$secretContentType = 'application/x-pkcs12' 
Set-AzureKeyVaultSecret -VaultName KEY_VAULT_NAME -Name KEY_VAULT_SECRET_NAME -SecretValue $Secret -ContentType $secretContentType
```

> [!Important]
> 如果未提供证书的对象版本，在将证书的较新版本上传到 Key Vault 后，API 管理将自动获取该版本。 

以下示例显示包含以下步骤的 Azure 资源管理器模板：

1. 创建含托管服务标识的 API 管理实例。
2. 更新 Azure Key Vault 实例的访问策略，并允许 API 管理实例从中获取机密。
3. 通过 Key Vault 实例中的证书设置自定义域名来更新 API 管理实例。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
                "description": "The pricing tier of this API Management service"
            }
        },
        "skuCount": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The instance size of this API Management service."
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
                "description": "Reference to the KeyVault certificate."
            }
        }
    },
    "variables": {
        "apiManagementServiceName": "[concat('apiservice', uniqueString(resourceGroup().id))]",
        "apimServiceIdentityResourceId": "[concat(resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName')),'/providers/Microsoft.ManagedIdentity/Identities/default')]"
    },
    "resources": [{
        "apiVersion": "2017-03-01",
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

## <a name="next-steps"></a>后续步骤

了解有关 Azure 托管服务标识的详细信息：

* [Azure 资源的托管服务标识](../active-directory/msi-overview.md)
* [Azure 资源管理器模板](https://github.com/Azure/azure-quickstart-templates)

