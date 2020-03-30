---
title: Azure 蓝图功能
description: 描述可用于 Azure 蓝图定义和分配中的蓝图工件的函数。
ms.date: 12/09/2019
ms.topic: reference
ms.openlocfilehash: 0aab2fe0511ccc11842d0e132a83d6e3f7fac27f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280672"
---
# <a name="functions-for-use-with-azure-blueprints"></a>与 Azure 蓝图一起使用的函数

Azure 蓝图提供了使蓝图定义更具动态功能的功能。 这些函数用于蓝图定义和蓝图工件。 资源管理器模板项目除了支持通过蓝图参数获取动态值外，还支持充分利用资源管理器函数。

支持以下功能：

- [工件](#artifacts)
- [Concat](#concat)
- [参数](#parameters)
- [资源组](#resourcegroup)
- [resourceGroups](#resourcegroups)
- [订阅](#subscription)

## <a name="artifacts"></a>项目

`artifacts(artifactName)`

返回使用该蓝图工件输出填充的属性的对象。

> [!NOTE]
> 无法`artifacts()`从资源管理器模板内部使用该函数。 该函数只能在蓝图定义 JSON 或工件 JSON 中使用，当使用 Azure PowerShell 或 REST API 作为[蓝图作为代码](https://github.com/Azure/azure-blueprints/blob/master/README.md)的一部分时，才可以使用该函数。

### <a name="parameters"></a>参数

| 参数 | 必选 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| 项目名称 |是 |字符串 |蓝图工件的名称。 |

### <a name="return-value"></a>返回值

输出属性的对象。 **输出**属性取决于要引用的蓝图工件的类型。 所有类型都遵循以下格式：

```json
{
  "outputs": {collectionOfOutputProperties}
}
```

#### <a name="policy-assignment-artifact"></a>策略分配项目

```json
{
    "outputs": {
        "policyAssignmentId": "{resourceId-of-policy-assignment}",
        "policyAssignmentName": "{name-of-policy-assignment}",
        "policyDefinitionId": "{resourceId-of-policy-definition}",
    }
}
```

#### <a name="resource-manager-template-artifact"></a>资源管理器模板项目

返回对象的**输出**属性在资源管理器模板中定义，并由部署返回。

#### <a name="role-assignment-artifact"></a>角色分配项目

```json
{
    "outputs": {
        "roleAssignmentId": "{resourceId-of-role-assignment}",
        "roleDefinitionId": "{resourceId-of-role-definition}",
        "principalId": "{principalId-role-is-being-assigned-to}",
    }
}
```

### <a name="example"></a>示例

包含以下示例输出属性的 ID _myTemplate 工件_的资源管理器模板项目：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    ...
    "outputs": {
        "myArray": {
            "type": "array",
            "value": ["first", "second"]
        },
        "myString": {
            "type": "string",
            "value": "my string value"
        },
        "myObject": {
            "type": "object",
            "value": {
                "myProperty": "my value",
                "anotherProperty": true
            }
        }
    }
}
```

从_myTemplateArtifact_示例检索数据的一些示例包括：

| 表达式 | 类型 | “值” |
|:---|:---|:---|
|`[artifacts("myTemplateArtifact").outputs.myArray]` | Array | \["第一"，"第二"\] |
|`[artifacts("myTemplateArtifact").outputs.myArray[0]]` | String | "第一" |
|`[artifacts("myTemplateArtifact").outputs.myString]` | String | "我的字符串值" |
|`[artifacts("myTemplateArtifact").outputs.myObject]` | Object | [ "我的财产"："我的价值"，"另一个财产"：真实 | |
|`[artifacts("myTemplateArtifact").outputs.myObject.myProperty]` | String | "我的价值" |
|`[artifacts("myTemplateArtifact").outputs.myObject.anotherProperty]` | Bool | True |

## <a name="concat"></a>concat

`concat(string1, string2, string3, ...)`

组合多个字符串值并返回串联的字符串。

### <a name="parameters"></a>参数

| 参数 | 必选 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| string1 |是 |字符串 |串联的第一个值。 |
| 其他参数 |否 |字符串 |按顺序排列的附加值 |

### <a name="return-value"></a>返回值

串联值的字符串。

### <a name="remarks"></a>备注

Azure 蓝图功能不同于 Azure 资源管理器模板功能，因为它仅适用于字符串。

### <a name="example"></a>示例

`concat(parameters('organizationName'), '-vm')`

## <a name="parameters"></a>parameters

`parameters(parameterName)`

返回蓝图参数值。 指定的参数名称必须在蓝图定义或蓝图工件中定义。

### <a name="parameters"></a>参数

| 参数 | 必选 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| parameterName |是 |字符串 |要返回的参数名称。 |

### <a name="return-value"></a>返回值

指定蓝图或蓝图工件参数的值。

### <a name="remarks"></a>备注

Azure 蓝图功能不同于 Azure 资源管理器模板功能，因为它仅适用于蓝图参数。

### <a name="example"></a>示例

在蓝图定义中定义参数_主体 Id：_

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "parameters": {
            "principalIds": {
                "type": "array",
                "metadata": {
                    "displayName": "Principal IDs",
                    "description": "This is a blueprint parameter that any artifact can reference. We'll display these descriptions for you in the info bubble. Supply principal IDs for the users,groups, or service principals for the RBAC assignment.",
                    "strongType": "PrincipalId"
                }
            }
        },
        ...
    }
}
```

然后使用_主体 Id_作为蓝图工`parameters()`件中的参数：

```json
{
    "type": "Microsoft.Blueprint/blueprints/artifacts",
    "kind": "roleAssignment",
    ...
    "properties": {
        "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
        "principalIds": "[parameters('principalIds')]",
        ...
    }
}
```

## <a name="resourcegroup"></a>resourceGroup

`resourceGroup()`

返回表示当前资源组的对象。

### <a name="return-value"></a>返回值

返回的对象采用以下格式：

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="remarks"></a>备注

Azure 蓝图功能不同于 Azure 资源管理器模板功能。 该`resourceGroup()`函数不能用于订阅级项目或蓝图定义。 它只能用于作为资源组工件的一部分的蓝图工件。

`resourceGroup()`函数的常见用途是创建与资源组项目位于同一位置的资源。

### <a name="example"></a>示例

要使用资源组的位置（在蓝图定义中设置或在分配期间作为另一个项目的位置），请声明蓝图定义中的资源组占位符对象。 在此示例中，_网络位置所有者_是资源组占位符的名称。

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "resourceGroups": {
            "NetworkingPlaceholder": {
                "location": "eastus"
            }
        }
    }
}
```

然后在以资源`resourceGroup()`组占位符对象的蓝图项目上下文中使用 函数。 在此示例中，模板项目部署到_网络位置所有者_资源组中，并提供参数_资源位置位置_动态填充与_网络位置所有者_资源组位置到模板。 _网络位置所有者_资源组的位置可以在蓝图定义上静态定义，也可以在分配期间动态定义。 在这两种情况下，模板项目都作为参数提供该信息，并用它来将资源部署到正确的位置。

```json
{
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "kind": "template",
  "properties": {
      "template": {
        ...
      },
      "resourceGroup": "NetworkingPlaceholder",
      ...
      "parameters": {
        "resourceLocation": {
          "value": "[resourceGroup().location]"
        }
      }
  }
}
```

## <a name="resourcegroups"></a>resourceGroups

`resourceGroups(placeholderName)`

返回表示指定资源组项目的对象。 与`resourceGroup()`需要工件上下文的 不同，此函数用于获取特定资源组占位符的属性，而该属性不在该资源组的上下文中。

### <a name="parameters"></a>参数

| 参数 | 必选 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| 占位符名称 |是 |字符串 |要返回的资源组项目名。 |

### <a name="return-value"></a>返回值

返回的对象采用以下格式：

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="example"></a>示例

要使用资源组的位置（在蓝图定义中设置或在分配期间作为另一个项目的位置），请声明蓝图定义中的资源组占位符对象。 在此示例中，_网络位置所有者_是资源组占位符的名称。

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "resourceGroups": {
            "NetworkingPlaceholder": {
                "location": "eastus"
            }
        }
    }
}
```

然后，`resourceGroups()`使用任何蓝图工件上下文中的函数来获取对资源组占位符对象的引用。 在此示例中，模板项目部署在_网络位置所有者_资源组之外，并提供参数_工件位置，_ 使用 _"网络位置"_ 资源组位置动态填充到模板。 _网络位置所有者_资源组的位置可以在蓝图定义上静态定义，也可以在分配期间动态定义。 在这两种情况下，模板项目都作为参数提供该信息，并用它来将资源部署到正确的位置。

```json
{
  "kind": "template",
  "properties": {
      "template": {
          ...
      },
      ...
      "parameters": {
        "artifactLocation": {
          "value": "[resourceGroups('NetworkingPlaceholder').location]"
        }
      }
  },
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "name": "myTemplate"
}
```

## <a name="subscription"></a>订阅

`subscription()`

返回有关当前蓝图分配订阅的详细信息。

### <a name="return-value"></a>返回值

返回的对象采用以下格式：

```json
{
    "id": "/subscriptions/{subscriptionId}",
    "subscriptionId": "{subscriptionId}",
    "tenantId": "{tenantId}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="example"></a>示例

使用订阅的显示名称和`concat()`函数创建作为参数_资源_传递给模板工件的命名约定。

```json
{
  "kind": "template",
  "properties": {
      "template": {
          ...
      },
      ...
      "parameters": {
        "resourceName": {
          "value": "[concat(subscription().displayName, '-vm')]"
        }
      }
  },
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "name": "myTemplate"
}
```

## <a name="next-steps"></a>后续步骤

- 了解[蓝图生命周期](../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../concepts/parameters.md)。
- 学习自定义[蓝图排序顺序](../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../concepts/resource-locking.md)。
- 了解如何[更新现有作业](../how-to/update-existing-assignments.md)。
- 在分配蓝图期间使用[常规故障排除时](../troubleshoot/general.md)解决问题。