---
title: 将客户加入 Azure Lighthouse
description: 了解如何将客户加入 Azure Lighthouse，从而允许使用 Azure 委派的资源管理通过自己的租户访问和管理其资源。
ms.date: 09/24/2020
ms.topic: how-to
ms.openlocfilehash: 0b941c82c2ba0e98f524587f5ef4c4ecf86249eb
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91336541"
---
# <a name="onboard-a-customer-to-azure-lighthouse"></a>将客户加入 Azure Lighthouse

本文介绍你作为服务提供商如何将客户加入 Azure Lighthouse。 当你执行此操作时，可以使用 [Azure 委派的资源管理](../concepts/azure-delegated-resource-management.md)通过你自己的 Azure Active Directory (Azure AD) 租户来访问和管理客户的委托资源 (订阅和/或资源组) 。

> [!TIP]
> 尽管我们指的是本主题中的服务提供商和客户，但 [管理多个租户的企业](../concepts/enterprise.md) 可以使用相同的过程来设置 Azure Lighthouse 并合并其管理体验。

你可以为多个客户重复执行载入过程。 当具有适当权限的用户登录到你的管理租户时，该用户可以在客户租户范围内获得授权，以执行管理操作，而无需登录到每个单独的客户租户。

若要跟踪你对客户互动的影响并获得认可，请将你的 Microsoft 合作伙伴网络 (MPN) ID 与至少一个有权访问你加入的每个订阅的用户帐户相关联。 需要在服务提供商租户中执行此关联。 建议在租户中创建一个与你的 MPN ID 相关联的服务主体帐户，并在每次加入客户时包含该服务主体。 有关详细信息，请参阅 [将合作伙伴 ID 链接到委托资源的伙伴获得的信用额度。

> [!NOTE]
> 当客户购买托管服务产品) /服务时，也可以载入 Azure Lighthouse， (公开 [发布到 Azure Marketplace](publish-managed-services-offers.md)。 你还可以在发布到 Azure Marketplace 的产品/服务中使用此处所述的载入过程。

载入过程要求从服务提供商的租户和客户的租户中执行操作。 上述所有步骤均可参见本文。

## <a name="gather-tenant-and-subscription-details"></a>收集租户和订阅详细信息

要载入客户的租户，必须具备有效的 Azure 订阅。 需了解以下信息：

- 服务提供商租户的租户 ID（要在其中管理客户的资源）
- 客户租户的租户 ID（具有由服务提供商管理的资源）
- 客户租户中由服务提供商管理的每个特定订阅的订阅 ID（或包含将由服务提供商管理的资源组的订阅 ID）。

如果尚未准备好这些 ID 值，可通过以下方式之一进行检索。 确保在部署中使用这些确切的值。

### <a name="azure-portal"></a>Azure 门户

可将鼠标悬停在 Azure 门户右上方的帐户名称上，或者选择“切换目录”来查看租户 ID。 要选择并复制租户 ID，请从门户中搜索“Azure Active Directory”，然后选择“属性”并复制“目录 ID”字段中显示的值 。 要在客户租户中查找订阅 ID，请搜索“订阅”，然后选择相应的订阅 ID。

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

作为服务提供商，你可能想要为单个客户执行多个任务，这需要针对不同范围的不同访问权限。 你可以根据需要定义任意数量的授权，以便为租户中的用户分配适当的 [基于角色的访问控制 (RBAC) 内置角色](../../role-based-access-control/built-in-roles.md) 。

为了更轻松地进行管理，我们建议为每个角色使用 Azure AD 用户组。 这样，你可以灵活地在组中添加或删除单个用户，这样就无需重复载入过程来进行用户更改。 可以将角色分配给服务主体，这对于自动化方案非常有用。

定义授权时，请确保遵循最低权限原则，使用户仅具有完成工作所需的权限。 有关支持的角色的指导和信息，请参阅 [Azure Lighthouse 方案中的租户、用户和角色](../concepts/tenants-users-roles.md)。

> [!IMPORTANT]
> 若要为 Azure AD 组添加权限，“组类型”必须是“安全性”而不是“Office 365”  。 此选项是在创建组时选择的。 有关详细信息，请参阅[使用 Azure Active Directory 创建基本组并添加成员](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。

若要定义授权，需要知道要向其授予访问权限的服务提供商租户中的每个用户、用户组或服务主体的 ID 值。 还需知道要分配的每个内置角色的角色定义 ID。 如果尚未获得这些 ID，可以从服务提供商租户内运行以下命令来检索它们。

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
(Get-AzADGroup -DisplayName '<yourGroupName>').id

# To retrieve the objectId for an Azure AD user
(Get-AzADUser -UserPrincipalName '<yourUPN>').id

# To retrieve the objectId for an SPN
(Get-AzADApplication -DisplayName '<appDisplayName>' | Get-AzADServicePrincipal).Id

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
> 我们建议在加入客户时分配[托管服务注册分配删除角色](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)，这样租户中的用户之后可以[删除对委派的访问权限](remove-delegation.md)（如果需要）。 如果未分配此角色，则只能由客户租户中的用户删除委派资源。

## <a name="create-an-azure-resource-manager-template"></a>创建 Azure 资源管理器模板

若要加入客户，需要使用以下信息为你的产品/服务创建 [Azure 资源管理器](../../azure-resource-manager/index.yml)模板。 **MspOfferName**和**mspOfferDescription**值将在 Azure 门户的 "[服务提供商" 页](view-manage-service-providers.md)中对客户可见。

|字段  |定义  |
|---------|---------|
|**mspOfferName**     |描述此定义的名称。 此值将作为产品/服务的标题显示给客户，并且必须是唯一值。        |
|**mspOfferDescription**     |产品/服务的简短说明（例如“Contoso VM 管理产品/服务”）。      |
|**managedByTenantId**     |租户 ID。          |
|**authorizations**     |租户中用户/组/SPN 的 **principalId** 值，每个值都带有一个 **principalIdDisplayName**（帮助客户了解授权的目的）并且已映射到内置 **roleDefinitionId** 值以指定访问级别。      |

加入过程需要 Azure 资源管理器模板（在[示例存储库](https://github.com/Azure/Azure-Lighthouse-samples/)中提供）以及相应的参数文件（可修改此文件，使其与你的配置匹配并定义你的授权）。

> [!IMPORTANT]
> 此处所述的过程需要对每个要载入的订阅进行单独部署，即使你是在同一客户租户中加入订阅。 如果要在同一客户租户中加入不同订阅中的多个资源组，也需要单独部署。 但是，可在一个部署中载入单个订阅中的多个资源组。
>
> 对于应用于同一订阅（或订阅内的资源组）的多个产品/服务，还需要单独部署。 所应用的每个产品/服务必须使用不同的 **mspOfferName**。

所选的模板取决于你是要加入整个订阅，还是要加入订阅中的一个资源组或多个资源组。 我们还提供了一个模板，可供购买了你发布 Azure 市场的托管服务产品/服务的客户使用；如果你偏向于按此方式载入其资源，则可使用它。

|加入此内容  |使用此 Azure 资源管理器模板  |修改此参数文件 |
|---------|---------|---------|
|订阅   |[delegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|资源组   |[rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|订阅内的多个资源组   |[multipleRgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|订阅（使用发布到 Azure 市场的产品/服务时）   |[marketplaceDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!TIP]
> 虽然无法在一个部署中载入整个管理组，但可以 [在管理组级别部署策略](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-delegate-management-groups)。 此策略将检查管理组中的每个订阅是否已委派给指定的管理租户，如果不是，则将基于你提供的值创建分配。

以下示例显示了一个修改后的 **delegatedResourceManagement.parameters.json** 文件，该文件可用于加入订阅。 资源组参数文件（位于 [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/rg-delegated-resource-management) 文件夹）很类似，但还带有一个 rgName 参数，它用于标识要载入的特定资源组。

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

上面示例中的最后一个授权添加了具有用户访问管理员角色 (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9) 的 principalId。 分配此角色时，必须包含 delegatedRoleDefinitionIds 属性和一个/多个内置角色。 在此授权中创建的用户能够将这些内置角色分配到客户租户中的 [托管标识](../../active-directory/managed-identities-azure-resources/overview.md) ，这是 [部署可修正的策略](deploy-policy-remediation.md)所必需的。  用户还可以创建支持事件。  通常与用户访问管理员角色关联的其他权限均不适用于此用户。

## <a name="deploy-the-azure-resource-manager-templates"></a>部署 Azure 资源管理器模板

更新参数文件后，客户租户中的用户必须在其租户中部署 Azure 资源管理器模板。 需要为每个要加入 (的订阅或包含要在其中进行登记) 资源组的每个订阅执行单独的部署。

> [!IMPORTANT]
> 此部署必须由客户租户中的非来宾帐户完成，该帐户具有 (载入订阅的 [所有者内置角色](../../role-based-access-control/built-in-roles.md#owner) ，或者包含正在载入的资源组) 。 若要查看所有可以委托订阅的用户，客户租户中的用户可以在 Azure 门户中选择订阅，打开“访问控制(IAM)”，然后[查看具有“所有者”角色的所有用户](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription)。 
>
> 如果订阅是通过[云解决方案提供商 (CSP) 计划](../concepts/cloud-solution-provider.md)创建的，则在服务提供商租户中具有[管理员代理](/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles)角色的任何用户都可以执行部署。

部署可在 Azure 门户、使用 PowerShell 或使用 Azure CLI 完成，如下所示。

### <a name="azure-portal"></a>Azure 门户

1. 在 [GitHub](https://github.com/Azure/Azure-Lighthouse-samples/)存储库中，选择要使用的模板旁边的 " **部署到 Azure** " 按钮。 Azure 门户中会打开模板。
1. 输入 **Msp 产品名称**、 **msp 提议说明**、 **租户 Id 管理**和 **授权**的值。 如果需要，可以选择 " **编辑参数** " `mspOfferName` ，以 `mspOfferDescription` `managedbyTenantId` `authorizations` 在参数文件中直接输入、、和的值。 请确保更新这些值，而不是使用模板中的默认值。
1. 依次选择 " **查看" 和 "创建**"，然后选择 " **创建**"。

几分钟后，你会看到部署已完成的通知。

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateFile <pathToTemplateFile> `
                 -TemplateParameterFile <pathToParameterFile> `
                 -Verbose

# Deploy Azure Resource Manager template that is located externally
New-AzSubscriptionDeployment -Name <deploymentName> `
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

当客户订阅已成功载入到 Azure Lighthouse 时，服务提供商的租户中的用户将可以查看该订阅及其资源 (如果已通过上述过程向其授予对该订阅的访问权限，则可以使用相应权限) 单独或作为 Azure AD 组的成员。 要确认此信息，请查看确保订阅以下列方式之一显示。  

### <a name="azure-portal"></a>Azure 门户

在服务提供商的租户中：

1. 导航到[“我的客户”页面](view-manage-customers.md)。
2. 选择“客户”。
3. 确认可使用在资源管理器模板中提供的产品/服务名称查看订阅。

> [!IMPORTANT]
> 若要在 " [我的客户](view-manage-customers.md)" 中查看委派的订阅，必须已向服务提供商的租户中的用户授予 " [读取](../../role-based-access-control/built-in-roles.md#reader) 者" 角色 (或另一个内置角色，此角色在载入订阅时) 包含读者访问权限。

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

## <a name="next-steps"></a>后续步骤

- 了解[跨租户管理体验](../concepts/cross-tenant-management-experience.md)。
- 在 Microsoft Azure 门户中转到“我的客户”，以[查看和管理客户](view-manage-customers.md)。
- 了解如何[删除对以前加入的委托的访问权限](remove-delegation.md)。
