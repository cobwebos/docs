---
title: 包含托管标识的托管应用
description: 配置包含托管标识的托管应用程序，以链接到现有资源、管理 Azure 资源，以及为活动日志提供操作标识。
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 05/13/2019
ms.openlocfilehash: 277faa2d47df9fddd1762d90d9aa2fb5bf00d4df
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "82508117"
---
# <a name="azure-managed-application-with-managed-identity"></a>包含托管标识的 Azure 托管应用程序

> [!NOTE]
> 托管应用程序的托管标识支持目前为预览版。 请使用 2018-09-01-preview API 版本来利用托管标识。

了解如何将托管应用程序配置为包含托管标识。 使用托管标识可让客户向托管应用程序授予对其他现有资源的访问权限。 标识由 Azure 平台托管，无需设置或转交任何机密。 有关 Azure Active Directory (AAD) 中的托管标识的详细信息，请参阅 [Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。

你的应用程序可以被授予两种类型的标识：

- 系统分配的标识与你的应用程序相绑定，如果删除应用，标识也会被删除****。 一个应用只能具有一个系统分配的标识。
- **用户分配的标识**是可以分配给应用的独立 Azure 资源。 一个应用可以具有多个用户分配的标识。

## <a name="how-to-use-managed-identity"></a>如何使用托管标识

托管标识可以实现托管应用程序的多种方案。 可解决的常见方案包括：

- 部署链接到现有 Azure 资源的托管应用程序。 例如，在托管应用程序中部署已附加到[现有网络接口](../../virtual-network/virtual-network-network-interface-vm.md)的 Azure 虚拟机 (VM)。
- 向托管应用程序和发布者授予对**托管资源组**外部的 Azure 资源的访问权限。
- 为活动日志以及 Azure 中的其他服务提供托管应用程序的操作标识。

## <a name="adding-managed-identity"></a>添加托管标识

创建包含托管标识的托管应用程序需要在 Azure 资源上设置一个附加的属性。 下面演示了一个示例**标识**属性：

```json
{
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity": {}
    }
}
```

可通过两种常用方法创建包含**标识**的托管应用程序：[CreateUIDefinition.json](./create-uidefinition-overview.md) 和 [Azure 资源管理器模板](../templates/template-syntax.md)。 对于简单的单一创建方案，应使用 CreateUIDefinition 来启用托管标识，因为它提供更丰富的体验。 但是，在处理需要自动化部署或多个托管应用程序部署的高级系统或复杂系统时，可以使用模板。

### <a name="using-createuidefinition"></a>使用 CreateUIDefinition

可以通过 [CreateUIDefinition.json](./create-uidefinition-overview.md) 为托管应用程序配置托管标识。 在 [outputs 节](./create-uidefinition-overview.md#outputs)中，键 `managedIdentity` 可用于替代托管应用程序模板的标识属性。 以下示例将在托管应用程序中启用**系统分配的**标识。 可以通过使用 CreateUIDefinition 元素要求使用者提供输入，来构建更复杂的标识对象。 这些输入可用于构造包含**用户分配的标识**的托管应用程序。

```json
"outputs": {
    "managedIdentity": { "Type": "SystemAssigned" }
}
```

#### <a name="when-to-use-createuidefinition-for-managed-identity"></a>何时使用 CreateUIDefinition 来启用托管标识

下面是有关何时使用 CreateUIDefinition 在托管应用程序中启用托管标识的一些建议。

- 通过 Azure 门户或市场创建托管应用程序。
- 托管标识需要复杂的使用者输入。
- 创建托管应用程序时需要托管标识。

#### <a name="managed-identity-createuidefinition-control"></a>托管标识 CreateUIDefinition 控件

CreateUIDefinition 支持内置的[托管标识控件](./microsoft-managedidentity-identityselector.md)。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.0.1-preview",
  "parameters": {
    "basics": [],
    "steps": [
      {
        "name": "applicationSettings",
        "label": "Application Settings",
        "subLabel": {
          "preValidation": "Configure your application settings",
          "postValidation": "Done"
        },
        "bladeTitle": "Application Settings",
        "elements": [
          {
            "name": "appName",
            "type": "Microsoft.Common.TextBox",
            "label": "Managed application Name",
            "toolTip": "Managed application instance name",
            "visible": true
          },
          {
            "name": "appIdentity",
            "type": "Microsoft.ManagedIdentity.IdentitySelector",
            "label": "Managed Identity Configuration",
            "toolTip": {
              "systemAssignedIdentity": "Enable system assigned identity to grant the managed application access to additional existing resources.",
              "userAssignedIdentity": "Add user assigned identities to grant the managed application access to additional existing resources."
            },
            "defaultValue": {
              "systemAssignedIdentity": "Off"
            },
            "options": {
              "hideSystemAssignedIdentity": false,
              "hideUserAssignedIdentity": false,
              "readOnlySystemAssignedIdentity": false
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "applicationResourceName": "[steps('applicationSettings').appName]",
      "location": "[location()]",
      "managedIdentity": "[steps('applicationSettings').appIdentity]"
    }
  }
}
```

![托管标识 CreateUIDefinition](./media/publish-managed-identity/msi-cuid.png)

### <a name="using-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板

> [!NOTE]
> 将为经历 Azure 门户创建体验的客户自动生成市场托管应用程序模板。
> 对于这些方案，必须使用 CreateUIDefinition 中的 `managedIdentity` 输出键来启用标识。

也可以通过 Azure 资源管理器模板启用托管标识。 以下示例将在托管应用程序中启用**系统分配的**标识。 可以通过使用 Azure 资源管理器模板参数提供输入，来构建更复杂的标识对象。 这些输入可用于构造包含**用户分配的标识**的托管应用程序。

#### <a name="when-to-use-azure-resource-manager-templates-for-managed-identity"></a>何时使用 Azure 资源管理器模板来启用托管标识

下面是有关何时使用 Azure 资源管理器模板在托管应用程序中启用托管标识的一些建议。

- 可以基于模板以编程方式部署托管应用程序。
- 预配托管应用程序需要托管标识的自定义角色分配。
- 托管应用程序不需要 Azure 门户和市场创建流。

#### <a name="systemassigned-template"></a>SystemAssigned 模板

用于部署包含**系统分配的标识**的托管应用程序的基本 Azure 资源管理器模板。

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

用于部署包含**用户分配的标识**的托管应用程序的基本 Azure 资源管理器模板。

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

向托管应用程序授予标识后，便可以获得对现有 Azure 资源的访问权限。 可以通过 Azure 门户中的“访问控制(IAM)”界面完成此过程。 可以搜索托管应用程序或**用户分配的标识**的名称来添加角色分配。

![为托管应用程序添加角色分配](./media/publish-managed-identity/identity-role-assignment.png)

## <a name="linking-existing-azure-resources"></a>链接现有的 Azure 资源

> [!NOTE]
> 在部署托管应用程序之前，必须[配置](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)**用户分配的标识**。 此外，托管应用程序的链接资源部署仅支持**市场**类型。

托管标识还可用于部署在部署期间需要访问现有资源的托管应用程序。 如果客户预配了托管应用程序，可以添加**用户分配的标识**以在 **mainTemplate** 部署中提供更多的授权。

### <a name="authoring-the-createuidefinition-with-a-linked-resource"></a>使用链接资源创作 CreateUIDefinition

将托管应用程序的部署链接到现有资源时，必须提供现有的 Azure 资源，以及在该资源中具有适用角色分配的**用户分配的标识**。

 需要两个输入的示例 CreateUIDefinition：网络接口资源 ID 和用户分配的标识资源 ID。

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

此 CreateUIDefinition.json 生成包含两个字段的“创建用户”体验。 第一个字段可让用户输入要链接到托管应用程序部署的资源的 Azure 资源 ID。 第二个字段可让使用者输入有权访问所链接 Azure 资源的**用户分配的标识** Azure 资源 ID。 生成的体验如下所示：

![包含两个输入的示例 CreateUIDefinition：网络接口资源 ID 和用户分配的标识资源 ID](./media/publish-managed-identity/network-interface-cuid.png)

### <a name="authoring-the-maintemplate-with-a-linked-resource"></a>使用链接资源创作 mainTemplate

除了更新 CreateUIDefinition 以外，还需要更新主要模板才能接受传入的链接资源 ID。 可以通过添加新的参数来更新主要模板，以接受新的输出。 由于 `managedIdentity` 输出将替代生成的托管应用程序模板中的值，因此不会将其传递到主要模板，并且不应将其包含在 parameters 节中。

一个示例主要模板，用于在 CreateUIDefinition 提供的现有网络接口中设置网络配置文件。

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

### <a name="consuming-the-managed-application-with-a-linked-resource"></a>使用包含链接资源的托管应用程序

创建托管应用程序包后，可通过 Azure 门户使用该托管应用程序。 需要完成几个前提步骤才能使用该应用程序。

- 必须创建所需链接 Azure 资源的实例。
- 必须[创建**用户分配的标识**并为其提供链接资源的角色分配](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)。
- 将现有的链接资源 ID 和**用户分配的标识** ID 提供给 CreateUIDefinition。

## <a name="accessing-the-managed-identity-token"></a>访问托管标识令牌

现在，可以在发布者租户中通过 `listTokens` API 访问托管应用程序的令牌。 示例请求可能如下所示：

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

参数 | 必须 | 说明
---|---|---
authorizationAudience | 否** | 目标资源的应用 ID URI。 它也是颁发的令牌的 `aud`（受众）声明。 默认值为“https://management.azure.com/”
userAssignedIdentities | 否** | 要检索其令牌的用户分配托管标识的列表。 如果未指定，`listTokens` 将返回系统分配的托管标识的令牌。


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

响应将在 `value` 属性下包含令牌数组：

参数 | 说明
---|---
access_token | 请求的访问令牌。
expires_in | 访问令牌的有效秒数。
expires_on | 访问令牌过期的时间范围。 此值以从纪元算起的秒数表示。
not_before | 访问令牌生效的时间范围。 此值以从纪元算起的秒数表示。
authorizationAudience | 请求其访问令牌的 `aud`（受众）。 这与 `listTokens` 请求中提供的值相同。
ResourceId | 颁发的令牌的 Azure 资源 ID。 此值为托管应用程序 ID 或用户分配的标识 ID。
token_type | 令牌的类型。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [如何使用自定义提供程序配置托管应用程序](../custom-providers/overview.md)
