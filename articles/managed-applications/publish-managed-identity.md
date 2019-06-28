---
title: 使用托管标识的 azure 托管应用程序
description: 了解如何配置托管应用程序使用托管标识。 托管标识可用于部署托管应用程序链接到现有资源授予托管应用程序管理 Azure 资源组外的托管的资源，并为活动日志提供操作的托管应用程序标识和在 Azure 中的其他服务。
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.reviewer: ''
ms.author: jobreen
author: jjbfour
ms.date: 05/13/2019
ms.openlocfilehash: be141e208016784b689262394798012c2212ba5b
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312236"
---
# <a name="azure-managed-application-with-managed-identity"></a>使用托管标识的 azure 托管应用程序

> [!NOTE]
> 为托管应用程序托管身份验证支持当前处于预览状态。 请使用 2018年-09-01-preview api 版本使用托管标识。

了解如何配置托管应用程序以包含托管标识。 托管标识可用于使客户可授予托管应用程序访问其他现有资源的权限。 标识由 Azure 平台托管，无需设置或转交任何机密。 有关管理的标识在 Azure Active Directory (AAD) 的详细信息，请参阅[托管于 Azure 资源的标识](../active-directory/managed-identities-azure-resources/overview.md)。

你的应用程序可以被授予两种类型的标识：

- 系统分配的标识与你的应用程序相绑定，如果删除应用，标识也会被删除  。 一个应用只能具有一个系统分配的标识。
- 一个**用户分配的标识**是独立的可分配给您的应用程序的 Azure 资源。 一个应用可以具有多个用户分配的标识。

## <a name="how-to-use-managed-identity"></a>如何使用托管标识

托管标识为托管应用程序可实现许多方案。 可以解决一些常见方案是：

- 部署托管应用程序链接到现有 Azure 资源。 一个示例在附加到托管应用程序中部署 Azure 虚拟机 (VM)[现有的网络接口](../virtual-network/virtual-network-network-interface-vm.md)。
- 授予对外部的 Azure 资源的托管应用程序和发布服务器访问权限**托管的资源组**。
- 活动日志和其他服务在 Azure 中的提供操作的托管应用程序标识。

## <a name="adding-managed-identity"></a>添加托管的标识

使用托管标识创建托管应用程序需要在 Azure 资源上设置其他属性。 下面的示例演示一个示例**标识**属性：

```json
{
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity": {}
    }
}
```

有两种常见方式创建托管应用程序与**标识**:[CreateUIDefinition.json](./create-uidefinition-overview.md)并[Azure 资源管理器模板](../azure-resource-manager/resource-group-authoring-templates.md)。 对于简单的单创建方案，CreateUIDefinition 应该用于启用托管标识，因为它提供了更丰富的体验。 但是，处理高级或复杂时需要的系统自动或多个托管应用程序部署，模板可用。

### <a name="using-createuidefinition"></a>使用 CreateUIDefinition

托管应用程序可以使用通过托管的标识来配置[CreateUIDefinition.json](./create-uidefinition-overview.md)。 在中[输出部分](./create-uidefinition-overview.md#outputs)，密钥`managedIdentity`可用于重写的托管应用程序模板的标识属性。 将启用示例下面**系统分配**上托管应用程序的标识。 使用 CreateUIDefinition 元素要求输入使用者来形成更复杂的标识对象。 这些输入可用于构造与托管应用程序**用户分配的标识**。

```json
"outputs": {
    "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
}
```

#### <a name="when-to-use-createuidefinition-for-managed-identity"></a>何时使用托管标识 CreateUIDefinition

下面是有关何时使用 CreateUIDefinition 启用托管标识托管应用程序上的一些建议。

- 创建托管应用程序内通过 Azure 门户或 marketplace。
- 托管标识需要复杂的使用者输入。
- 在创建托管应用程序需要托管标识。

#### <a name="systemassigned-createuidefinition"></a>SystemAssigned CreateUIDefinition

为托管应用程序启用 SystemAssigned 标识基本 CreateUIDefinition。

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

采用基本 CreateUIDefinition**用户分配的标识**作为输入，并为托管应用程序启用 UserAssigned 标识的资源。

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

上述 CreateUIDefinition.json 生成具有使用者，以输入一个文本框创建用户体验**用户分配的标识**Azure 资源 id。 生成的体验如下所示：

![示例用户分配标识 CreateUIDefinition](./media/publish-managed-identity/user-assigned-identity.png)

### <a name="using-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板

> [!NOTE]
> 应用商店的托管应用程序模板会自动生成的客户通过 Azure 门户创建体验。
> 对于这些方案， `managedIdentity` CreateUIDefinition 上的输出密钥必须用于启用标识。

此外可以通过 Azure 资源管理器模板启用托管标识。 将启用示例下面**系统分配**上托管应用程序的标识。 使用 Azure 资源管理器模板的参数提供的输入来形成更复杂的标识对象。 这些输入可用于构造与托管应用程序**用户分配的标识**。

#### <a name="when-to-use-azure-resource-manager-templates-for-managed-identity"></a>何时使用托管标识的 Azure 资源管理器模板

下面是有关何时使用 Azure 资源管理器模板启用托管标识托管应用程序上的一些建议。

- 可以基于模板以编程方式部署托管应用程序。
- 托管标识的自定义角色分配所需预配托管应用程序。
- 托管应用程序不需要 Azure 门户和 marketplace 创建流程。

#### <a name="systemassigned-template"></a>SystemAssigned 模板

部署托管应用程序使用的基本 Azure 资源管理器模板**系统分配**标识。

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

部署托管应用程序使用的基本 Azure 资源管理器模板**用户分配的标识**。

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

一旦托管应用程序授予了标识，它可以授予对现有 azure 资源的访问。 此过程可通过 Azure 门户中的访问控制 (IAM) 界面。 托管应用程序的名称或**用户分配的标识**可搜索要添加的角色分配。

![添加托管应用程序的角色分配](./media/publish-managed-identity/identity-role-assignment.png)

## <a name="linking-existing-azure-resources"></a>链接现有的 Azure 资源

> [!NOTE]
> 一个**用户分配的标识**必须是[配置](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)部署托管应用程序之前。 此外，对于仅支持托管应用程序的链接的资源部署**marketplace**类型。

此外可以使用托管标识在其部署过程中需要对现有资源的访问的托管应用程序部署。 设置托管应用程序时由客户**用户分配的标识**可以添加以提供对其他授权**mainTemplate**部署。

### <a name="authoring-the-createuidefinition-with-a-linked-resource"></a>创作 CreateUIDefinition 与链接的资源

链接现有资源，这两个现有的 Azure 资源的托管应用程序部署时和一个**用户分配的标识**必须使用适用角色提供对该资源分配。

 示例需要两个输入的 CreateUIDefinition： 网络接口资源 ID 和用户分配的标识资源 id。

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

此 CreateUIDefinition.json 生成有两个字段创建用户体验。 第一个字段允许用户在 Azure 资源 ID 中输入要链接到托管应用程序部署的资源。 第二个是使用者输入**用户分配的标识**有权访问的链接的 Azure 资源的 Azure 资源 ID。 生成的体验如下所示：

![CreateUIDefinition 示例使用两个输入： 网络接口资源 ID 和用户分配的标识资源 ID](./media/publish-managed-identity/network-interface-cuid.png)

### <a name="authoring-the-maintemplate-with-a-linked-resource"></a>创作 mainTemplate 与链接的资源

除了更新 CreateUIDefinition，主模板也需要更新，以接受传入的链接的资源 id。 可以更新主模板，以通过添加一个新的参数来接受新的输出。 由于`managedIdentity`输出重写上生成的托管应用程序模板的值，它不会传递到主模板并不应包含在参数部分中。

将网络配置文件设置为现有的网络接口提供的 CreateUIDefinition 示例主模板。

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

### <a name="consuming-the-managed-application-with-a-linked-resource"></a>使用带有链接的资源的管理应用程序

创建托管应用程序包后，可以通过 Azure 门户来使用托管应用程序。 可以使用它之前，有几个先决条件步骤。

- 必须创建所需链接的 Azure 资源的实例。
- **用户分配的标识**必须是[创建和给定角色分配](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)到链接的资源。
- 现有链接的资源 ID 和**用户分配的标识**ID 提供给 CreateUIDefinition。

## <a name="accessing-the-managed-identity-token"></a>访问托管标识令牌

现在可以通过访问托管应用程序的令牌`listTokens`发布者租户中的 api。 示例请求可能如下所示：

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}/listTokens?api-version=2018-09-01-preview HTTP/1.1
```

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

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [如何使用自定义提供程序中配置托管应用程序](./custom-providers-overview.md)
