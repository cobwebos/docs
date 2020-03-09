---
title: 部署可修正的策略
description: 了解如何将客户载入到 Azure 委派资源管理，使你能够通过自己的租户访问和管理其资源。
ms.date: 10/11/2019
ms.topic: conceptual
ms.openlocfilehash: c06ed4ea597808aee18d4a848bcfea7152b9cf8e
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78394025"
---
# <a name="deploy-a-policy-that-can-be-remediated-within-a-delegated-subscription"></a>部署可以在委派的订阅中修正的策略

服务提供商可以通过 [Azure Lighthouse](../overview.md) 在委托订阅中创建和编辑策略定义。 但是，若要部署使用[补救任务](../../governance/policy/how-to/remediate-resources.md)的策略（即，具有 [deployIfNotExists](../../governance/policy/concepts/effects.md#deployifnotexists) 或 [modify](../../governance/policy/concepts/effects.md#modify) 效果的策略），需在客户租户中创建[托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。 此托管标识可供 Azure Policy 用来在策略中部署模板。 在载入 Azure 委托资源管理的客户时，以及部署策略本身时，需要执行相关步骤来启用此方案。

## <a name="create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant"></a>创建一个可以在客户租户中将角色分配给托管标识的用户

载入 Azure 委托资源管理的客户时，我们使用 [Azure 资源管理器模板](onboard-customer.md#create-an-azure-resource-manager-template)以及用于在管理租户中定义用户、用户组和服务主体的参数文件，而管理租户将能够访问客户租户中的委托资源。 在参数文件中，将为每个此类用户 (**principalId**) 分配一个用于定义访问级别的[内置角色](../../role-based-access-control/built-in-roles.md) (**roleDefinitionId**)。

若要允许 **principalId** 在客户租户中创建托管标识，必须将其 **roleDefinitionId** 设置为“用户访问管理员”。 虽然此角色通常不受支持，但可以将它用在此特定方案中，允许具有此权限的用户将一个或多个特定的内置角色分配给托管标识。 这些角色在 **delegatedRoleDefinitionIds** 属性中定义。 可以在此处包括任何内置角色，用户访问管理员或所有者除外。

将客户加入以后，在此授权中创建的 **principalId** 即可将这些内置角色分配给客户租户中的托管标识。 但是，他们不会有通常与“用户访问管理员”角色关联的任何其他权限。

下面的示例显示了一个 **principalId**，该 ID 将有“用户访问管理员”角色。 此用户将能够将两个内置角色分配到客户租户中的托管标识：参与者和 Log Analytics 参与者。

```json
{
    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
    "principalIdDisplayName": "Policy Automation Account",
    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "delegatedRoleDefinitionIds": [
         "b24988ac-6180-42a0-ab88-20f7382dd24c",
         "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
    ]
}
```

## <a name="deploy-policies-that-can-be-remediated"></a>部署可修正的策略

按照上述说明创建具有必需权限的用户以后，该用户即可在客户租户中部署使用补救任务的策略。

例如，假设你想要在客户租户的 Azure Key Vault 资源上启用诊断，如此[示例](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-keyvault-monitoring)所示。 管理租户中具有适当权限（如上所述）的用户会部署 [Azure 资源管理器模板](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/policy-enforce-keyvault-monitoring/enforceAzureMonitoredKeyVault.json)来启用此方案。

请注意，目前必须通过 API 而不是 Azure 门户创建与委托订阅配合使用的策略分配。 这样做时，必须将 **apiVersion** 设置为 **2019-04-01-preview**，其中包含新的 **delegatedManagedIdentityResourceId** 属性。 此属性用于包括驻留在客户租户中的托管标识（该租户位于已载入到 Azure 委托资源管理的订阅或资源组中）。

以下示例显示具有 **delegatedManagedIdentityResourceId** 的角色分配。

```json
"type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2019-04-01-preview",
            "name": "[parameters('rbacGuid')]",
            "dependsOn": [
                "[variables('policyAssignment')]"
            ],
            "properties": {
                "roleDefinitionId": "[concat(subscription().id, '/providers/Microsoft.Authorization/roleDefinitions/', variables('rbacContributor'))]",
                "principalType": "ServicePrincipal",
                "delegatedManagedIdentityResourceId": "[concat(subscription().id, '/providers/Microsoft.Authorization/policyAssignments/', variables('policyAssignment'))]",
                "principalId": "[toLower(reference(concat('/providers/Microsoft.Authorization/policyAssignments/', variables('policyAssignment')), '2018-05-01', 'Full' ).identity.principalId)]"
            }
```

> [!TIP]
> 这里有[类似的示例](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-add-or-replace-tag)，该示例演示如何部署策略，以便为委托的订阅添加或删除标记（使用 modify 效果）。

## <a name="next-steps"></a>后续步骤

- 了解 [Azure Policy](../../governance/policy/index.yml)。
- 了解 [Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。
