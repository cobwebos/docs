---
title: Azure Service Fabric-向 Service Fabric 应用程序授予对其他 Azure 资源的访问权限 |Microsoft Docs
description: 本文介绍如何为启用了托管标识的 Service Fabric 应用程序授予对支持基于 Azure Active Directory 身份验证的其他 Azure 资源的访问权限。
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.date: 08/08/2019
ms.author: atsenthi
ms.openlocfilehash: 467b202cf6b981969316a2646aac99f788f7a2f4
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091185"
---
# <a name="granting-a-service-fabric-applications-managed-identity-access-to-azure-resources-preview"></a>为 Service Fabric 应用程序的托管标识授予对 Azure 资源的访问权限（预览）

在应用程序可以使用其托管标识访问其他资源之前，必须为该标识授予对所要访问的受保护 Azure 资源的权限。 授予权限通常是 Azure 服务的“控制平面”中执行的一项管理操作，该服务拥有通过 Azure 资源管理器路由的受保护资源。这会强制实施任何适用的基于角色的访问检查。

具体的步骤顺序取决于要访问的 Azure 资源的类型，以及用于授予权限的语言/客户端。 本文的余下内容假设已将用户分配的标识分配到应用程序，其中包含一些典型示例供你参考，但本主题的参考内容不可能做到详尽；有关授予权限的最新说明，请参阅相应 Azure 服务的文档。  

## <a name="granting-access-to-azure-storage"></a>授予对 Azure 存储的访问权限
可以使用 Service Fabric 应用程序的托管标识（在本例中为用户分配的标识）来检索 Azure 存储 Blob 中的数据。 在 Azure 门户中使用以下步骤为标识授予所需的权限：

1. 导航到存储帐户
2. 单击左侧面板中的“访问控制(IAM)”链接。
3. （可选）检查现有的访问权限：在“查找”控件中选择“系统分配的托管标识”或“用户分配的托管标识”；从随后的结果列表中选择适当的标识
4. 单击页面顶部的“+ 添加角色分配”，为应用程序的标识添加新的角色分配。
在“角色”下面的下拉列表中，选择“存储 Blob 数据读取者”。
5. 在“将访问权限分配给”下面的下拉列表中，选择 `User assigned managed identity`。
6. 接下来，确保“订阅”下拉列表中列出了正确的订阅，然后将“资源组”设置为“所有资源组”。
7. 在“选择”下，选择对应于 Service Fabric 应用程序的 UAI，然后单击“保存”。

对系统分配的 Service Fabric 托管标识的支持不包括 Azure 门户中的集成；如果应用程序使用系统分配的标识，则必须先找到该应用程序标识的客户端 ID，然后重复上述步骤，不过要在“查找”控件中选择 `Azure AD user, group, or service principal` 选项。

## <a name="granting-access-to-azure-key-vault"></a>授予对 Azure Key Vault 的访问权限
与访问存储类似，可以利用 Service Fabric 应用程序的托管标识来访问 Azure Key Vault。 在 Azure 门户中授予访问权限的步骤类似于上面所列的步骤，这里不再重复。 有关差异，请参考下图。

![Key Vault 访问策略](../key-vault/media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

下面的示例演示如何通过模板部署授予对保管库的访问权限;将下面的代码段添加到模板元素下的`resources`其他项。 此示例演示如何为用户分配的标识类型和系统分配的标识类型分别授予访问权限。

```json
    # under 'variables':
  "variables": {
        "userAssignedIdentityResourceId" : "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]",
    }
    # under 'resources':
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2018-02-14",
        "properties": {
            "accessPolicies": [
                {
                    "tenantId": "[reference(variables('userAssignedIdentityResourceId'), '2018-11-30').tenantId]",
                    "objectId": "[reference(variables('userAssignedIdentityResourceId'), '2018-11-30').principalId]",
                    "dependsOn": [
                        "[variables('userAssignedIdentityResourceId')]"
                    ],
                    "permissions": {
                        "keys":         ["get", "list"],
                        "secrets":      ["get", "list"],
                        "certificates": ["get", "list"]
                    }
                }
            ]
        }
    },
```
对于系统分配的托管标识：
```json
    # under 'variables':
  "variables": {
        "sfAppSystemAssignedIdentityResourceId": "[concat(resourceId('Microsoft.ServiceFabric/clusters/applications/', parameters('clusterName'), parameters('applicationName')), '/providers/Microsoft.ManagedIdentity/Identities/default')]"
    }
    # under 'resources':
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2018-02-14",
        "properties": {
            "accessPolicies": [
            {
                    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
                    "tenantId": "[reference(variables('sfAppSystemAssignedIdentityResourceId'), '2018-11-30').tenantId]",
                    "objectId": "[reference(variables('sfAppSystemAssignedIdentityResourceId'), '2018-11-30').principalId]",
                    "dependsOn": [
                        "[variables('sfAppSystemAssignedIdentityResourceId')]"
                    ],
                    "permissions": {
                        "secrets": [
                            "get",
                            "list"
                        ],
                        "certificates": 
                        [
                            "get", 
                            "list"
                        ]
                    }
            },
        ]
        }
    }
```

有关更多详细信息，请参阅[保管库 - 更新访问策略](https://docs.microsoft.com/rest/api/keyvault/vaults/updateaccesspolicy)。

## <a name="next-steps"></a>后续步骤
* [使用系统分配的托管标识部署 Azure Service Fabric 应用程序](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [使用用户分配的托管标识部署 Azure Service Fabric 应用程序](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)