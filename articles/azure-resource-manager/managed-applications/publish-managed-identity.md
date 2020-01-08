---
title: 具有托管标识的托管应用
description: 用托管标识配置托管应用程序，以便链接到现有资源，管理 Azure 资源，以及为活动日志提供操作标识。
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 05/13/2019
ms.openlocfilehash: dbf75262440474c5cb50a6d733ac7cba212b5f3f
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75651652"
---
# <a name="azure-managed-application-with-managed-identity"></a>具有托管标识的 Azure 托管应用程序

> [!NOTE]
> 托管应用程序的托管标识支持目前处于预览阶段。 请使用 2018-09-01-preview api 版本来利用托管标识。

了解如何将托管应用程序配置为包含托管标识。 托管标识可用于允许客户向托管应用程序授予对其他现有资源的访问权限。 标识由 Azure 平台托管，无需设置或转交任何机密。 有关 Azure Active Directory （AAD）中的托管标识的详细信息，请参阅[Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。

你的应用程序可以被授予两种类型的标识：

- 系统分配的标识与你的应用程序相绑定，如果删除应用，标识也会被删除。 一个应用只能具有一个系统分配的标识。
- **用户分配的标识**是可以分配给你的应用程序的独立 Azure 资源。 一个应用可以具有多个用户分配的标识。

## <a name="how-to-use-managed-identity"></a>如何使用托管标识

托管标识可实现托管应用程序的多种方案。 可以解决的常见方案包括：

- 部署链接到现有 Azure 资源的托管应用程序。 例如，在已连接到[现有网络接口](../../virtual-network/virtual-network-network-interface-vm.md)的托管应用程序中部署 Azure 虚拟机（VM）。
- 向托管的应用程序和发布者授予对**托管资源组**之外的 Azure 资源的访问权限。
- 为活动日志和 Azure 中的其他服务提供托管应用程序的操作标识。

## <a name="adding-managed-identity"></a>添加托管标识

使用托管标识创建托管应用程序需要在 Azure 资源上设置其他属性。 下面的示例显示了一个示例**标识**属性：

```json
{
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity": {}
    }
}
```

可以通过两种常见方法创建具有**标识**： [CreateUIDefinition](./create-uidefinition-overview.md)和[Azure 资源管理器模板](../templates/template-syntax.md)的托管应用程序。 对于简单的单个 create 方案，CreateUIDefinition 应该用于启用托管标识，因为它提供了更丰富的体验。 但是，在处理需要自动或多个托管应用程序部署的高级或复杂系统时，可以使用模板。

### <a name="using-createuidefinition"></a>使用 CreateUIDefinition

托管的应用程序可以通过[CreateUIDefinition](./create-uidefinition-overview.md)配置托管标识。 在 "[输出" 部分](./create-uidefinition-overview.md#outputs)中，可以使用键 `managedIdentity` 来替代托管应用程序模板的标识属性。 示例下面将在托管应用程序上启用**系统分配**的标识。 通过使用 CreateUIDefinition 元素向使用者请求输入，可以形成更复杂的标识对象。 这些输入可用于使用**用户分配的标识**构造托管应用程序。

```json
"outputs": {
    "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
}
```

#### <a name="when-to-use-createuidefinition-for-managed-identity"></a>何时使用 CreateUIDefinition 作为托管标识

下面是有关何时使用 CreateUIDefinition 在托管应用程序上启用托管标识的一些建议。

- 托管应用程序的创建过程通过 Azure 门户或 marketplace。
- 托管标识需要复杂的使用者输入。
- 在创建托管应用程序时需要托管标识。

#### <a name="systemassigned-createuidefinition"></a>SystemAssigned CreateUIDefinition

为托管应用程序启用 SystemAssigned 标识的基本 CreateUIDefinition。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
        ],
        "outputs": {
            "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
        }
    }
}
```

#### <a name="userassigned-createuidefinition"></a>UserAssigned CreateUIDefinition

基本 CreateUIDefinition，它使用**用户分配的标识**资源作为输入，并为托管应用程序启用 UserAssigned 标识。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "manageIdentity",
                "label": "Identity",
                "subLabel": {
                    "preValidation": "Manage Identities",
                    "postValidation": "Done"
                },
                "bladeTitle": "Identity",
                "elements": [
                    {
                        "name": "userAssignedText",
                        "type": "Microsoft.Common.TextBox",
                        "label": "User assigned managed identity",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity",
                        "visible": true
                    }
                ]
            }
        ],
        "outputs": {
            "managedIdentity": "[parse(concat('{\"Type\":\"UserAssigned\",\"UserAssignedIdentities\":{',string(steps('manageIdentity').userAssignedText),':{}}}'))]"
        }
    }
}
```

上面的 CreateUIDefinition 生成了一个创建用户体验，它具有用于使用者的文本框，以输入**用户分配的标识**AZURE 资源 ID。 生成的体验如下所示：

![示例用户分配的标识 CreateUIDefinition](./media/publish-managed-identity/user-assigned-identity.png)

### <a name="using-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板

> [!NOTE]
> 将为客户提供 Azure 门户创建体验的 Marketplace 托管应用程序模板。
> 对于这些方案，CreateUIDefinition 上的 `managedIdentity` 输出密钥必须用于启用标识。

还可以通过 Azure 资源管理器模板启用托管标识。 示例下面将在托管应用程序上启用**系统分配**的标识。 可以使用 Azure 资源管理器模板参数提供更复杂的标识对象，以提供输入。 这些输入可用于使用**用户分配的标识**构造托管应用程序。

#### <a name="when-to-use-azure-resource-manager-templates-for-managed-identity"></a>何时将 Azure 资源管理器模板用于托管标识

下面是有关何时使用 Azure 资源管理器模板在托管应用程序上启用托管标识的一些建议。

- 可以基于模板以编程方式部署托管应用程序。
- 预配托管的应用程序需要托管标识的自定义角色分配。
- 托管应用程序不需要 Azure 门户和 marketplace 创建流。

#### <a name="systemassigned-template"></a>SystemAssigned 模板

使用**系统分配**的标识部署托管应用程序的基本 Azure 资源管理器模板。

```json
"resources": [
    {
        "type": "Microsoft.Solutions/applications",
        "name": "[parameters('applicationName')]",
        "apiVersion": "2018-09-01-preview",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "properties": {
            "ManagedResourceGroupId": "[parameters('managedByResourceGroupId')]",
            "parameters": { }
        }
    }
]
```

### <a name="userassigned-template"></a>UserAssigned 模板

使用**用户分配的标识**部署托管应用程序的基本 Azure 资源管理器模板。

```json
"resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('managedIdentityName')]",
      "apiVersion": "2018-11-30",
      "location": "[parameters('location')]"
    },
    {
        "type": "Microsoft.Solutions/applications",
        "name": "[parameters('applicationName')]",
        "apiVersion": "2018-09-01-preview",
        "location": "[parameters('location')]",
        "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
                "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',parameters('managedIdentityName'))]": {}
            }
        },
        "properties": {
            "ManagedResourceGroupId": "[parameters('managedByResourceGroupId')]",
            "parameters": { }
        }
    }
]
```

## <a name="granting-access-to-azure-resources"></a>授予对 Azure 资源的访问权限

向托管应用程序授予标识后，便可以获得对现有 azure 资源的访问权限。 可以通过 Azure 门户中的访问控制（IAM）接口完成此过程。 可以搜索托管应用程序的名称或**用户分配的标识**，以添加角色分配。

![为托管应用程序添加角色分配](./media/publish-managed-identity/identity-role-assignment.png)

## <a name="linking-existing-azure-resources"></a>链接现有的 Azure 资源

> [!NOTE]
> 必须先[配置](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)**用户分配的标识**，然后才能部署托管应用程序。 此外，仅**marketplace**类型支持托管应用程序的链接资源部署。

托管标识还可用于部署需要在部署过程中访问现有资源的托管应用程序。 如果客户预配了托管应用程序，则可以添加**用户分配的标识**，以便向**maintemplate.json**部署提供额外的授权。

### <a name="authoring-the-createuidefinition-with-a-linked-resource"></a>使用链接资源创作 CreateUIDefinition

将托管应用程序的部署链接到现有资源时，必须提供现有的 Azure 资源和具有该资源上适用的角色分配的**用户分配的标识**。

 需要两个输入的示例 CreateUIDefinition：网络接口资源 ID 和用户分配的标识资源 id。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Compute.MultiVm",
    "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "managedApplicationSetting",
                "label": "Managed Application Settings",
                "subLabel": {
                    "preValidation": "Managed Application Settings",
                    "postValidation": "Done"
                },
                "bladeTitle": "Managed Application Settings",
                "elements": [
                    {
                        "name": "networkInterfaceId",
                        "type": "Microsoft.Common.TextBox",
                        "label": "network interface resource id",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Network/networkInterfaces/existingnetworkinterface",
                        "toolTip": "Must represent the identity as an Azure Resource Manager resource identifer format ex. /subscriptions/sub1/resourcegroups/myGroup/providers/Microsoft.Network/networkInterfaces/networkinterface1",
                        "visible": true
                    },
                    {
                        "name": "userAssignedId",
                        "type": "Microsoft.Common.TextBox",
                        "label": "user assigned identity resource id",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity",
                        "toolTip": "Must represent the identity as an Azure Resource Manager resource identifer format ex. /subscriptions/sub1/resourcegroups/myGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/identity1",
                        "visible": true
                    }
                ]
            }
        ],
        "outputs": {
            "existingNetworkInterfaceId": "[steps('managedApplicationSetting').networkInterfaceId]",
            "managedIdentity": "[parse(concat('{\"Type\":\"UserAssigned\",\"UserAssignedIdentities\":{',string(steps('managedApplicationSetting').userAssignedId),':{}}}'))]"
        }
    }
}
```

此 CreateUIDefinition 生成包含两个字段的创建用户体验。 第一个字段允许用户在 Azure 资源 ID 中输入链接到托管应用程序部署的资源。 第二种情况是，使用者输入**用户分配的标识**azure 资源 ID，该 ID 有权访问链接的 azure 资源。 生成的体验如下所示：

![示例 CreateUIDefinition，其中包含两个输入：网络接口资源 ID 和用户分配的标识资源 ID](./media/publish-managed-identity/network-interface-cuid.png)

### <a name="authoring-the-maintemplate-with-a-linked-resource"></a>使用链接资源创作 Maintemplate.json

除了更新 CreateUIDefinition，主模板还需要更新，以接受传入链接的资源 ID。 可以通过添加新的参数更新主模板以接受新的输出。 由于 `managedIdentity` 输出将覆盖生成的托管应用程序模板上的值，因此不会将其传递到主模板，且不应包含在 parameters 节中。

将网络配置文件设置为 CreateUIDefinition 提供的现有网络接口的示例主模板。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "existingNetworkInterfaceId": { "type": "string" }
    },
    "variables": {
    },
    "resources": [
        {
            "apiVersion": "2016-04-30-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "myLinkedResourceVM",
            "location": "[resourceGroup().location]",
            "properties": {
                …,
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[parameters('existingNetworkInterfaceId')]"
                        }
                    ]
                }
            }
        }
    ]
}
```

### <a name="consuming-the-managed-application-with-a-linked-resource"></a>将托管应用程序用于链接资源

创建托管应用程序包后，可通过 Azure 门户使用托管应用程序。 在可以使用之前，有几个先决条件步骤。

- 必须创建所需链接的 Azure 资源的实例。
- 必须创建**用户分配的标识** [，并将角色分配](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)给链接的资源。
- 将为 CreateUIDefinition 提供现有链接的资源 ID 和**用户分配的标识**id。

## <a name="accessing-the-managed-identity-token"></a>访问托管标识令牌

现在可以通过发布者租户中的 `listTokens` api 访问托管应用程序的令牌。 示例请求可能如下所示：

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}/listTokens?api-version=2018-09-01-preview HTTP/1.1

{
    "authorizationAudience": "https://management.azure.com/",
    "userAssignedIdentities": [
        "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{userAssignedIdentityName}"
    ]
}
```

请求正文参数：

参数 | 需要 | Description
---|---|---
authorizationAudience | 否 | 目标资源的应用 ID URI。 它还是已颁发令牌的 `aud` （受众）声明。 默认值为 "https://management.azure.com/"
userAssignedIdentities | 否 | 要为其检索令牌的用户分配的托管标识的列表。 如果未指定，`listTokens` 将为系统分配的托管标识返回令牌。


示例响应可能如下所示：

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json

{
    "value": [
        {
            "access_token": "eyJ0eXAi…",
            "expires_in": "2…",
            "expires_on": "1557…",
            "not_before": "1557…",
            "authorizationAudience": "https://management.azure.com/",
            "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}",
            "token_type": "Bearer"
        }
    ]
}
```

响应将包含 `value` 属性下的标记数组：

参数 | Description
---|---
access_token | 请求的访问令牌。
expires_in | 访问令牌有效的秒数。
expires_on | 访问令牌过期的时间范围。 这表示为 epoch 中的秒数。
not_before | 访问令牌生效时的时间跨度。 这表示为 epoch 中的秒数。
authorizationAudience | 访问令牌请求的 `aud` （受众）。 这与 `listTokens` 请求中提供的内容相同。
resourceId | 已颁发令牌的 Azure 资源 ID。 这是托管应用程序 ID 或用户分配的标识 ID。
token_type | 令牌的类型。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [如何使用自定义提供程序配置托管应用程序](../custom-providers/overview.md)
