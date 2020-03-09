---
title: 将客户载入到 Azure 委派资源管理
description: 了解如何将客户载入到 Azure 委派资源管理，使你能够通过自己的租户访问和管理其资源。
ms.date: 01/28/2020
ms.topic: conceptual
ms.openlocfilehash: 7cf0ff1d64603215a9607f5a25ebc4077f9fa9da
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78925435"
---
# <a name="onboard-a-customer-to-azure-delegated-resource-management"></a>将客户载入到 Azure 委派资源管理

本文介绍了服务提供商可如何将客户载入到 Azure 委托资源管理，使其能够通过自己的 Azure Active Directory (Azure AD) 租户访问和管理他们委托的资源（订阅和/或资源组）。 尽管我们将在此处参考服务提供商和客户，但[管理多个租户的企业](../concepts/enterprise.md)可以使用相同的过程来整合其管理体验。

如果要为多位客户管理资源，可重复此过程。 随后，当授权用户登录到你的租户时，可跨客户租赁范围向此用户授权，使其无需登录到每个单独的客户租户即可执行管理操作。

若要跟踪客户约定和接收识别的影响，请将 Microsoft 合作伙伴网络（MPN） ID 与至少一个可访问每个载入订阅的用户帐户相关联。 请注意，你需要在服务提供商租户中执行此关联。 为简单起见，我们建议在租户中创建一个与你的 MPN ID 相关联的服务主体帐户，并授予它对你所登记的每个客户的读者访问权限。 有关详细信息，请参阅将[合作伙伴 ID 链接到 Azure 帐户](../../billing/billing-partner-admin-link-started.md)。 

> [!NOTE]
> 在客户购买你发布到 Azure 市场的托管服务产品（公共或专用）时也可将其加入。 有关详细信息，请参阅[将托管服务产品发布到 Azure 市场](publish-managed-services-offers.md)。 你还可以使用此处所述的载入流程以及发布到 Azure Marketplace 的产品/服务。

载入过程要求从服务提供商的租户和客户的租户中执行操作。 上述所有步骤均可参见本文。

## <a name="gather-tenant-and-subscription-details"></a>收集租户和订阅详细信息

要载入客户的租户，必须具备有效的 Azure 订阅。 需了解以下信息：

- 服务提供商租户的租户 ID（要在其中管理客户的资源）
- 客户租户的租户 ID（具有由服务提供商管理的资源）
- 客户租户中将由服务提供商（或包含将由服务提供商管理的资源组）管理的每个特定订阅的订阅 Id。

> [!NOTE]
> 即使只希望在订阅中载入一个或多个资源组，也必须在订阅级别完成部署，因此需要订阅 ID。

如果还没有这些 ID 值，可以通过以下方式之一来检索它们。 请确保并在部署中使用这些确切的值。

### <a name="azure-portal"></a>Azure 门户

可将鼠标悬停在 Azure 门户右上方的帐户名称上，或者选择“切换目录”来查看租户 ID。 要选择并复制租户 ID，请从门户中搜索“Azure Active Directory”，然后选择“属性”并复制“目录 ID”字段中显示的值。 要在客户租户中查找订阅 ID，请搜索“订阅”，然后选择相应的订阅 ID。

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

> [!NOTE]
> 使用此处所述的过程加入订阅（或订阅中的一个或多个资源组）时，将为该订阅注册 **Microsoft.ManagedServices** 资源提供程序。

## <a name="define-roles-and-permissions"></a>定义角色和权限

作为服务提供商，你可能想要为单个客户执行多个任务，这需要针对不同范围的不同访问权限。 可以根据需要定义任意数量的授权，以将[基于角色的访问控制 (RBAC) 内置角色](../../role-based-access-control/built-in-roles.md)分配给租户中的用户。

为了简化管理，建议为每个角色使用 Azure AD 用户组，这使你能够向组添加或删除单个用户，而不是直接向此用户分配权限。 你可能还想要将角色分配给服务主体。 请务必遵循最低权限原则，使用户仅具有完成作业所需的权限。 有关支持角色的建议和信息，请参阅 [Azure Lighthouse 方案中的租户、用户和角色](../concepts/tenants-users-roles.md)。

若要定义授权，需要知道要向其授予访问权限的服务提供商租户中的每个用户、用户组或服务主体的 ID 值。 还需知道要分配的每个内置角色的角色定义 ID。 如果尚未安装，可以从服务提供商租户内运行以下命令来检索它们。

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
az ad user show --id "<yourUPN>" --query "objectId" --output tsv

# To retrieve the objectId for an SPN
az ad sp list --query "[?displayName == '<spDisplayName>'].objectId" --output tsv

# To retrieve role definition IDs
az role definition list --name "<roleName>" | grep name
```
> [!TIP]
> 我们建议在加入客户时分配[托管服务注册分配删除角色](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)，这样租户中的用户之后可以[删除对委派的访问权限](#remove-access-to-a-delegation)（如果需要）。 如果未分配此角色，则只能由客户租户中的用户删除委派资源。

## <a name="create-an-azure-resource-manager-template"></a>创建 Azure 资源管理器模板

若要加入客户，需要使用以下信息为你的产品/服务创建 [Azure 资源管理器](../../azure-resource-manager/index.yml)模板。 在 Azure 门户的 "[服务提供程序" 页](view-manage-service-providers.md)中查看 "产品/服务" 时，客户将可以看到**mspOfferName**和**mspOfferDescription**值。

|字段  |Definition  |
|---------|---------|
|**mspOfferName**     |描述此定义的名称。 此值将作为产品/服务的标题显示给客户。         |
|**mspOfferDescription**     |产品/服务的简短说明（例如，"Contoso VM 管理产品/服务"）。      |
|**managedByTenantId**     |租户 ID。          |
|**authorizations**     |租户中的用户/组/Spn 的**principalId**值，每个值都有一个**principalIdDisplayName** ，可帮助客户了解授权的目的，并将其映射到内置的**roleDefinitionId**值以指定访问级别。      |

载入过程需要 Azure 资源管理器模板（在我们的[示例](https://github.com/Azure/Azure-Lighthouse-samples/)存储库中提供）和相应的参数文件，你可以修改该模板来匹配配置并定义你的授权。

你选择的模板取决于你是在订阅中加入整个订阅、资源组还是多个资源组。 我们还提供了一个模板，可供购买了你发布 Azure 市场的托管服务产品/服务的客户使用；如果你偏向于按此方式载入其资源，则可使用它。

|加入此内容  |使用此 Azure 资源管理器模板  |修改此参数文件 |
|---------|---------|---------|
|订阅   |[delegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|资源组   |[rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|订阅内的多个资源组   |[multipleRgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|订阅（使用发布到 Azure 市场的产品/服务时）   |[marketplaceDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!IMPORTANT]
> 此处所述的过程需要为要载入的每个订阅单独提供订阅级部署，即使你是在同一客户租户中加入订阅。 如果要在同一客户租户中的不同订阅内加入多个资源组，则还需要单独的部署。 但是，可以在一个订阅级别的部署中加入单个订阅中的多个资源组。
>
> 对于应用于同一订阅（或订阅内的资源组）的多个产品/服务，还需要单独部署。 所应用的每个产品/服务必须使用不同的 **mspOfferName**。

下面的示例显示了一个已修改的**delegatedResourceManagement**文件，该文件可用于载入订阅。 资源组参数文件（位于 [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) 文件夹）很类似，但还带有一个 rgName 参数，它用于标识要载入的特定资源组。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
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

上面示例中的最后一个授权添加了具有用户访问管理员角色 (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9) 的 principalId。 分配此角色时，必须包含 delegatedRoleDefinitionIds 属性和一个/多个内置角色。 在此授权中创建的用户能够将这些内置角色分配到客户租户中的[托管标识](../../active-directory/managed-identities-azure-resources/overview.md)，这是[部署可修正的策略](deploy-policy-remediation.md)所必需的。 通常与用户访问管理员角色关联的其他权限均不适用于此用户。

## <a name="deploy-the-azure-resource-manager-templates"></a>部署 Azure 资源管理器模板

更新参数文件后，客户租户中的用户必须在其租户中部署 Azure 资源管理器模板，作为订阅级部署。 对于要载入 Azure 委托资源管理的每个订阅（或者包含要载入的资源组的每个订阅），需要单独进行部署。

由于这是订阅级部署，因此无法在 Azure 门户中启动。 可以使用 PowerShell 或 Azure CLI 来完成部署，如下所示。

> [!IMPORTANT]
> 此订阅级部署必须由客户租户中的非来宾帐户完成，此帐户具有载入订阅的[所有者内置角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)（或包含正在载入的资源组）。 若要查看所有可以委托订阅的用户，客户租户中的用户可以在 Azure 门户中选择订阅，打开“访问控制(IAM)”，然后[查看具有“所有者”角色的所有用户](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription)。

### <a name="powershell"></a>PowerShell

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
az deployment create --name <deploymentName> \
                     --location <AzureRegion> \
                     --template-file <pathToTemplateFile> \
                     --parameters <parameters/parameterFile> \
                     --verbose

# Deploy external Azure Resource Manager template, with local parameter file
az deployment create --name <deploymentName> \
                     --location <AzureRegion> \
                     --template-uri <templateUri> \
                     --parameters <parameterFile> \
                     --verbose
```

## <a name="confirm-successful-onboarding"></a>确认成功载入

客户订阅成功载入 Azure 委托资源管理后，服务提供商租户中的用户将能够查看订阅及其资源（前提是通过上述过程单独或者作为具有相应权限的 Azure AD 组的成员向这些用户授予了访问此内容的权限）。 要确认此信息，请查看确保订阅以下列方式之一显示。  

### <a name="azure-portal"></a>Azure 门户

在服务提供商的租户中：

1. 导航到[“我的客户”页面](view-manage-customers.md)。
2. 选择“客户”。
3. 确认可使用在资源管理器模板中提供的产品/服务名称查看订阅。

> [!IMPORTANT]
> 若要在[我的客户](view-manage-customers.md)中查看委派的订阅，在为 Azure 委派资源管理加入订阅时，必须向服务提供商租户中的用户授予[读者](../../role-based-access-control/built-in-roles.md#reader)角色（或其他包括读者访问权限的内置角色）。

在客户的租户中：

1. 导航到[服务提供商页面](view-manage-service-providers.md)。
2. 选择“服务提供商产品/服务”。
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

## <a name="remove-access-to-a-delegation"></a>删除对委派的访问权限

默认情况下，客户租户中具有相应权限的用户可以在 Azure 门户的 "[服务提供程序" 页](view-manage-service-providers.md#add-or-remove-service-provider-offers)中删除服务提供程序的访问权限。 当他们执行此操作时，服务提供商的租户中的任何用户都将无法访问之前已委派的资源。

如果在为 Azure 委派的资源管理加入客户时具有[托管服务注册分配](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)的载入用户，则这些用户也将能够删除委派。

下面的示例显示了授予可包含在参数文件中的“托管服务注册分配删除角色”的分配：

```json
    "authorizations": [ 
        { 
            "principalId": "cfa7496e-a619-4a14-a740-85c5ad2063bb", 
            "principalIdDisplayName": "MSP Operators", 
            "roleDefinitionId": "91c1777a-f3dc-4fae-b103-61d183457e46" 
        } 
    ] 
```

具有此权限的用户可以通过以下的一种方法删除委托。

### <a name="azure-portal"></a>Azure 门户

1. 导航到[“我的客户”页面](view-manage-customers.md)。
2. 选择 "**委托**"。
3. 找到要删除的委派，然后选择显示在其行中的 "垃圾桶" 图标。

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory 

Login-AzAccount

# Select the subscription that is delegated - or contains the delegated resource group(s)

Select-AzSubscription -SubscriptionName "<subscriptionName>"

# Get the registration assignment

Get-AzManagedServicesAssignment -Scope "/subscriptions/{delegatedSubscriptionId}"

# Delete the registration assignment

Remove-AzManagedServicesAssignment -ResourceId "/subscriptions/{delegatedSubscriptionId}/providers/Microsoft.ManagedServices/registrationAssignments/{assignmentGuid}"
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory

az login

# Select the subscription that is delegated – or contains the delegated resource group(s)

az account set -s <subscriptionId/name>

# List registration assignments

az managedservices assignment list

# Delete the registration assignment

az managedservices assignment delete --assignment <id or full resourceId>
```

## <a name="next-steps"></a>后续步骤

- 了解[跨租户管理体验](../concepts/cross-tenant-management-experience.md)。
- 在 Microsoft Azure 门户中转到“我的客户”，以[查看和管理客户](view-manage-customers.md)。
