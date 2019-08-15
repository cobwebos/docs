---
title: 将客户载入到 Azure 委派资源管理 - Azure Lighthouse
description: 了解如何将客户载入到 Azure 委派资源管理，使你能够通过自己的租户访问和管理其资源。
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: d1876977d819b50569b6f07242af91fb1d6832ee
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934320"
---
# <a name="onboard-a-customer-to-azure-delegated-resource-management"></a>将客户载入到 Azure 委派资源管理

本文介绍了服务提供商可如何将客户载入到 Azure 委托资源管理，使其能够通过自己的 Azure Active Directory (Azure AD) 租户访问和管理他们委托的资源（订阅和/或资源组）。 在此处我们将提到服务提供商和客户，但管理多个租户的企业可以使用相同的过程来整合其管理体验。

如果要为多位客户管理资源，可重复此过程。 随后，当授权用户登录到你的租户时，可跨客户租赁范围向此用户授权，使其无需登录到每个单独的客户租户即可执行管理操作。

可将 Microsoft 合作伙伴网络 (MPN) ID 与载入的订阅相关联，以跟踪你在客户参与中的影响。 有关详细信息，请参阅[将合作伙伴 ID 链接到 Azure 帐户](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started)。

> [!NOTE]
> 可在客户购买发布到 Azure 市场的托管服务产品（公共或专用）时将其自动载入。 有关详细信息，请参阅[将托管服务产品发布到 Azure 市场](publish-managed-services-offers.md)。 还可对发布到 Azure 市场的产品/服务使用此处所述的流程。

载入过程要求从服务提供商的租户和客户的租户中执行操作。 上述所有步骤均可参见本文。

> [!IMPORTANT]
> 目前，如果订阅使用 Azure Databricks，则无法为 Azure 委托资源管理载入订阅（或订阅内的资源组）。 同样，如果订阅已注册要向 Microsoft.ManagedServices 资源提供程序载入，则此时无法为此订阅创建 Databricks 工作区  。

## <a name="gather-tenant-and-subscription-details"></a>收集租户和订阅详细信息

要载入客户的租户，必须具备有效的 Azure 订阅。 需了解以下信息：

- 服务提供商租户的租户 ID（要在其中管理客户的资源）
- 客户租户的租户 ID（具有由服务提供商管理的资源）
- 客户租户中由服务提供商管理的每个特定订阅的订阅 ID（或包含将由服务提供商管理的资源组的订阅 ID）

如果尚没有此信息，可通过以下方式之一进行检索。

### <a name="azure-portal"></a>Azure 门户

可将鼠标悬停在 Azure 门户右上方的帐户名称上，或者选择“切换目录”来查看租户 ID  。 要选择并复制租户 ID，请从门户中搜索“Azure Active Directory”，然后选择“属性”并复制“目录 ID”字段中显示的值   。 要查找订阅的 ID，请搜索“订阅”，然后选择相应的订阅 ID。

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Select-AzSubscription <subscriptionId>
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account set --subscription <subscriptionId/name>
az account show
```


## <a name="ensure-the-customers-subscription-is-registered-for-onboarding"></a>确保客户的订阅已注册可进行载入

每个订阅都必须通过手动注册 Microsoft.ManagedServices 资源提供程序获得载入授权  。 客户可按照 [Azure 资源提供程序和类型](../../azure-resource-manager/resource-manager-supported-services.md)中概述的步骤注册订阅。

客户可通过以下方式之一确认订阅已准备好进行载入。

### <a name="azure-portal"></a>Azure 门户

1. 在 Azure 门户中，选择订阅。
1. 选择“资源提供程序”  。
1. 确认 Microsoft.ManagedServices 显示为“已注册”   。

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Set-AzContext -Subscription <subscriptionId>
Get-AzResourceProvider -ProviderNameSpace 'Microsoft.ManagedServices'
```

这应会返回如下结果：

```output
ProviderNamespace : Microsoft.ManagedServices
RegistrationState : Registered
ResourceTypes     : {registrationDefinitions}
Locations         : {}

ProviderNamespace : Microsoft.ManagedServices
RegistrationState : Registered
ResourceTypes     : {registrationAssignments}
Locations         : {}

ProviderNamespace : Microsoft.ManagedServices
RegistrationState : Registered
ResourceTypes     : {operations}
Locations         : {}
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account set –subscription <subscriptionId>
az provider show --namespace "Microsoft.ManagedServices" --output table
```

这应会返回如下结果：

```output
Namespace                  RegistrationState
-------------------------  -------------------
Microsoft.ManagedServices  Registered
```

## <a name="define-roles-and-permissions"></a>定义角色和权限

作为服务提供商，你可能想要对单个客户使用多个产品/服务，这需要针对不同范围的不同访问权限。

为了简化管理，建议为每个角色使用 Azure AD 用户组，这使你能够向组添加或删除单个用户，而不是直接向此用户分配权限。 你可能还想要将角色分配给服务主体。 请务必遵循最低权限原则，使用户仅具有完成作业所需的权限，从而帮助减少意外错误发生的几率。 有关详细信息，请参阅[建议的安全做法](../concepts/recommended-security-practices.md)。

> [!NOTE]
> 角色分配必须使用基于角色的访问控制 (RBAC) [内置角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)。 除了所有者和带有 [DataActions](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#dataactions) 权限的任何内置角色之外，Azure 委派资源管理当前支持其他所有内置角色。 仅支持将用户访问管理员内置角色用于下述有限用途。 此外，也不支持自定义角色和[经典订阅管理员角色](https://docs.microsoft.com/azure/role-based-access-control/classic-administrators)。

要定义授权，需要知道要向其授予访问权限的每个用户、用户组或服务主体的 ID 值。 还需知道要分配的每个内置角色的角色定义 ID。 如果没有这些信息，可通过以下方式之一进行检索。



### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
(Get-AzADGroup -DisplayName '<yourGroupName>').id

# To retrieve the objectId for an Azure AD user
(Get-AzADUser -UserPrincipalName '<yourUPN>').id

# To retrieve the objectId for an SPN
(Get-AzADApplication -DisplayName '<appDisplayName>').objectId

# To retrieve role definition IDs
(Get-AzRoleDefinition -Name '<roleName>').id
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
az ad group list --query "[?displayName == '<yourGroupName>'].objectId" --output tsv

# To retrieve the objectId for an Azure AD user
az ad user show --upn-or-object-id "<yourUPN>" –-query "objectId" --output tsv

# To retrieve the objectId for an SPN
az ad sp list --query "[?displayName == '<spDisplayName>'].objectId" --output tsv

# To retrieve role definition IDs
az role definition list --name "<roleName>" | grep name
```

## <a name="create-an-azure-resource-manager-template"></a>创建 Azure 资源管理器模板

要载入客户，需要创建 [Azure 资源管理器](https://docs.microsoft.com/azure/azure-resource-manager/)模板，其中包含以下内容：

|字段  |定义  |
|---------|---------|
|**mspName**     |服务提供商名称         |
|**mspOfferDescription**     |产品/服务的简短说明（例如“Contoso VM 管理产品/服务”）      |
|**managedByTenantId**     |租户 ID         |
|**authorizations**     |租户中用户/组/SPN 的 principalId 值，每个值都带有一个 principalIdDisplayName（帮助客户了解授权的目的）并且已映射到内置 roleDefinitionId 值以指定访问级别            |

要载入用户的订阅，请使用[示例存储库](https://github.com/Azure/Azure-Lighthouse-samples/)中提供的相应 Azure 资源管理器模板以及相应的参数文件（可修改此文件，使其与你的配置相匹配并定义你的授权）。 提供的单独模板取决于你是要载入整个订阅、一个资源组还是订阅内的多个资源组。 我们还提供了一个模板，可供购买了你发布 Azure 市场的托管服务产品/服务的客户使用；如果你偏向于按此方式载入其资源，则可使用它。

|**载入此内容**  |**使用此 Azure 资源管理器模板**  |**修改此参数文件** |
|---------|---------|---------|
|Subscription   |[delegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Resource group   |[rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|订阅内的多个资源组   |[multipleRgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|订阅（使用发布到 Azure 市场的产品/服务时）   |[marketplaceDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!IMPORTANT]
> 此处所述的过程需要对每个要载入的订阅单独进行部署。
> 
> 如果要载入不同订阅中的多个资源组，也需要单独部署。 但是，可在一个部署中载入单个订阅中的多个资源组。

下面的示例展示了一个修改后的 resourceProjection.parameters.json 文件，该文件用于载入订阅  。 资源组参数文件（位于 [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) 文件夹）很类似，但还带有一个 rgName 参数，它用于标识要载入的特定资源组  。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspName": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "mspOfferDescription": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "managedByTenantId": {
            "value": "df4602a3-920c-435f-98c4-49ff031b9ef6"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "36243c78-bf99-498c-9df9-86d9f8d28608"
                },
                {
                    "principalId": "0afd8497-7bff-4873-a7ff-b19a6b7b332c",
                    "principalIdDisplayName": "Tier 2 Support",
                    "roleDefinitionId": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
                },
                {
                    "principalId": "9fe47fff-5655-4779-b726-2cf02b07c7c7",
                    "principalIdDisplayName": "Service Automation Account",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
                    "principalIdDisplayName": "Policy Automation Account",
                    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
                    "delegatedRoleDefinitionIds": [
                        "b24988ac-6180-42a0-ab88-20f7382dd24c",
                        "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
                    ]
                }
            ]
        }
    }
}
```
上面示例中的最后一个授权添加了具有用户访问管理员角色 (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9) 的 principalId  。 分配此角色时，必须包含 delegatedRoleDefinitionIds 属性和一个/多个内置角色  。 在此授权中创建的用户能够将这些内置角色分配给托管标识。 请注意，通常与用户访问管理员角色关联的其他权限均不适用于此用户。

## <a name="deploy-the-azure-resource-manager-templates"></a>部署 Azure 资源管理器模板

更新参数文件后，客户必须在其客户租户中将资源管理模板部署为订阅级部署。 对于要载入 Azure 委托资源管理的每个订阅（或者包含要载入的资源组的每个订阅），需要单独进行部署。

> [!IMPORTANT]
> 部署必须由客户租户中的非来宾帐户完成，该帐户对于正在载入的订阅（或包含正在载入的资源组的订阅）拥有[所有者内置角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)。

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
New-AzDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateFile <pathToTemplateFile> `
                 -TemplateParameterFile <pathToParameterFile> `
                 -Verbose

# Deploy Azure Resource Manager template that is located externally
New-AzDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateUri <templateUri> `
                 -TemplateParameterUri <parameterUri> `
                 -Verbose
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
az deployment create –-name <deploymentName> \
                     --location <AzureRegion> \
                     --template-file <pathToTemplateFile> \
                     --parameters <parameters/parameterFile> \
                     --verbose

# Deploy external Azure Resource Manager template, with local parameter file
az deployment create –-name <deploymentName \
                     –-location <AzureRegion> \
                     --template-uri <templateUri> \
                     --parameters <parameterFile> \
                     --verbose
```

## <a name="confirm-successful-onboarding"></a>确认成功载入

客户订阅成功载入 Azure 委托资源管理后，服务提供商租户中的用户将能够查看订阅及其资源（前提是通过上述过程单独或者作为具有相应权限的 Azure AD 组的成员向这些用户授予了访问此内容的权限）。 要确认此信息，请查看确保订阅以下列方式之一显示。  

### <a name="azure-portal"></a>Azure 门户

在服务提供商的租户中：

1. 导航到[“我的客户”页面](view-manage-customers.md)。
2. 选择“客户”  。
3. 确认可使用在资源管理器模板中提供的产品/服务名称查看订阅。

在客户的租户中：

1. 导航到[服务提供商页面](view-manage-service-providers.md)。
2. 选择“服务提供商产品/服务”  。
3. 确认可使用在资源管理器模板中提供的产品/服务名称查看订阅。

> [!NOTE]
> 部署完成后，可能需要几分钟时间才能在 Azure 门户中显示更新。

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Get-AzContext
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account list
```

## <a name="next-steps"></a>后续步骤

- 了解[跨租户管理体验](../concepts/cross-tenant-management-experience.md)。
- 访问 Azure 门户中的“我的客户”，[查看和管理客户](view-manage-customers.md)  。
