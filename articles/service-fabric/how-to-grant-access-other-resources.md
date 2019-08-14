---
title: Azure Service Fabric-向 Service Fabric 应用程序授予对其他 Azure 资源的访问权限 |Microsoft Docs
description: 本文介绍如何向支持管理身份的 Service Fabric 应用程序授予对支持基于 Azure Active Directory 身份验证的其他 Azure 资源的访问权限。
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.date: 08/08/2019
ms.author: atsenthi
ms.openlocfilehash: b6e1108ffee13f1583d920947404963a69616788
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/12/2019
ms.locfileid: "68958416"
---
# <a name="granting-a-service-fabric-applications-managed-identity-access-to-azure-resources"></a>向 Service Fabric 应用程序的托管标识授予对 Azure 资源的访问权限

在应用程序可以使用其托管标识来访问其他资源之前, 必须在访问受保护的 Azure 资源上授予该标识的权限。 授予权限通常是 Azure 服务的 "控制平面" 上的管理操作, 该服务拥有通过 Azure 资源管理器路由的受保护资源, 这将强制实施任何适用的基于角色的访问检查。

具体的步骤顺序将取决于要访问的 Azure 资源的类型, 以及用于授予权限的语言/客户端。 本文的其余部分假定已向应用程序分配了一个用户分配的标识, 其中包含一些用于方便的典型示例, 但没有任何办法可用于本主题;有关授予权限的最新说明, 请参阅各个 Azure 服务的文档。  

## <a name="granting-access-to-azure-storage"></a>授予对 Azure 存储的访问权限
你可以使用 Service Fabric 应用程序的托管标识 (在本例中为用户分配) 来检索 Azure 存储 blob 中的数据。 按照以下步骤向标识授予 Azure 门户中所需的权限:

1. 导航到存储帐户
2. 单击左侧面板中的 "访问控制 (IAM)" 链接。
3. 可有可无检查现有访问权限: 在 "查找" 控件中选择系统或用户分配的托管标识;从随后的结果列表中选择适当的标识
4. 单击页面顶部的 "+ 添加角色分配", 为应用程序的标识添加新的角色分配。
在 "角色" 下, 从下拉列表中选择 "存储 Blob 数据读取器"。
5. 在下一个下拉列表中的 "分配访问权限`User assigned managed identity`" 下, 选择。
6. 接下来, 请确保 "订阅" 下拉列表中列出了正确的订阅, 然后将 "资源组" 设置为 "所有资源组"。
7. 在 "选择" 下, 选择对应于 Service Fabric 应用程序的 UAI, 然后单击 "保存"。

对系统分配 Service Fabric 托管标识的支持不包括 Azure 门户中的集成;如果你的应用程序使用系统分配的标识, 则必须首先找到应用程序标识的客户端 ID, 然后重复上述步骤, 但选择 "查找" 控件`Azure AD user, group, or service principal`中的选项。

## <a name="granting-access-to-azure-key-vault"></a>授予对 Azure Key Vault 的访问权限
同样, 在访问存储时, 可以利用 Service Fabric 应用程序的托管标识访问 Azure 密钥保管库。 在 Azure 门户中授予访问权限的步骤类似于上面列出的步骤, 并且不会在此处重复出现。 有关差异, 请参阅下图。

![Key Vault 访问策略](../key-vault/media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

下面的示例演示如何通过模板部署授予对保管库的访问权限;将下面的代码片段添加为模板元素`resources`下的另一个条目。

```json
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

有关更多详细信息, 请参阅[保管库-更新访问策略](https://docs.microsoft.com/rest/api/keyvault/vaults/updateaccesspolicy)。

## <a name="next-steps"></a>后续步骤

* [使用系统分配的托管标识部署 Azure Service Fabric 应用程序](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)

* [使用用户分配的托管标识部署 Azure Service Fabric 应用程序](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)

## <a name="related-articles"></a>相关文章

* 查看 Azure 中的[托管标识支持](./concepts-managed-identity.md)Service Fabric

* [部署新的](./configure-new-azure-service-fabric-enable-managed-identity.md)支持托管标识的 Azure Service Fabric 群集 

* 在现有 Azure Service Fabric 群集中[启用托管标识](./configure-existing-cluster-enable-managed-identity-token-service.md)

* 利用源代码中 Service Fabric 应用程序的[托管标识](./how-to-managed-identity-service-fabric-app-code.md)

* 查看[支持 Azure AD 身份验证的 Azure 服务](../active-directory/managed-identities-azure-resources/services-support-msi.md)列表
