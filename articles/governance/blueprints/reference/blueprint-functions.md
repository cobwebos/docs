---
title: Azure 蓝图函数
description: 介绍可用于 Azure 蓝图定义和分配中的蓝图项目的函数。
ms.date: 12/09/2019
ms.topic: reference
ms.openlocfilehash: 0aab2fe0511ccc11842d0e132a83d6e3f7fac27f
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79280672"
---
# <a name="functions-for-use-with-azure-blueprints"></a>与 Azure 蓝图一起使用的函数

Azure 蓝图提供使蓝图定义更动态的函数。 这些函数可用于蓝图定义和蓝图项目。 资源管理器模板项目除了通过蓝图参数获取动态值外，还支持资源管理器函数的全部使用。

支持以下函数：

- [诸如](#artifacts)
- [concat](#concat)
- [参数](#parameters)
- [resourceGroup](#resourcegroup)
- [resourceGroups](#resourcegroups)
- [subscription](#subscription)

## <a name="artifacts"></a>项目

`artifacts(artifactName)`

返回使用该蓝图项目输出填充的属性的对象。

> [!NOTE]
> 不能从资源管理器模板内使用 `artifacts()` 函数。 此函数只能在蓝图定义 JSON 中或项目 JSON 中使用，当使用 Azure PowerShell 或 REST API 作为[蓝图作为代码](https://github.com/Azure/azure-blueprints/blob/master/README.md)的一部分来管理蓝图时。

### <a name="parameters"></a>parameters

| 参数 | 必选 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| artifactName |是 |字符串 |蓝图项目的名称。 |

### <a name="return-value"></a>返回值

输出属性的对象。 **输出**属性取决于所引用的蓝图项目的类型。 所有类型都遵循以下格式：

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

ID 为_myTemplateArtifact_的资源管理器模板项目，其中包含以下示例输出属性：

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

从_myTemplateArtifact_示例中检索数据的一些示例如下：

| 表达式 | 类型 | 值 |
|:---|:---|:---|
|`[artifacts("myTemplateArtifact").outputs.myArray]` | Array | \["first"、"second"\] |
|`[artifacts("myTemplateArtifact").outputs.myArray[0]]` | String | 1 |
|`[artifacts("myTemplateArtifact").outputs.myString]` | String | "我的字符串值" |
|`[artifacts("myTemplateArtifact").outputs.myObject]` | Object | {"t.myproperty"： "my value"，"anotherProperty"： true} |
|`[artifacts("myTemplateArtifact").outputs.myObject.myProperty]` | String | "my value" |
|`[artifacts("myTemplateArtifact").outputs.myObject.anotherProperty]` | Bool | True |

## <a name="concat"></a>concat

`concat(string1, string2, string3, ...)`

组合多个字符串值并返回串联的字符串。

### <a name="parameters"></a>parameters

| 参数 | 必选 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| string1 |是 |字符串 |串联的第一个值。 |
| 其他参数 |否 |字符串 |串联的顺序的其他值 |

### <a name="return-value"></a>返回值

串联值的字符串。

### <a name="remarks"></a>备注

Azure 蓝图函数不同于 Azure 资源管理器模板功能，因为它仅适用于字符串。

### <a name="example"></a>示例

`concat(parameters('organizationName'), '-vm')`

## <a name="parameters"></a>parameters

`parameters(parameterName)`

返回蓝图参数值。 指定的参数名称必须在蓝图定义或蓝图项目中定义。

### <a name="parameters"></a>parameters

| 参数 | 必选 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| parameterName |是 |字符串 |要返回的参数名称。 |

### <a name="return-value"></a>返回值

指定的蓝图或蓝图项目参数的值。

### <a name="remarks"></a>备注

Azure 蓝图函数不同于 Azure 资源管理器模板功能，因为它仅适用于蓝图参数。

### <a name="example"></a>示例

在蓝图定义中定义参数_principalIds_ ：

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

然后，将_principalIds_用作蓝图项目中 `parameters()` 的参数：

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

Azure 蓝图功能不同于 Azure 资源管理器模板功能。 `resourceGroup()` 函数不能在订阅级别项目或蓝图定义中使用。 它只能在属于资源组项目的蓝图项目中使用。

`resourceGroup()` 函数的一个常见用途是在与资源组项目相同的位置创建资源。

### <a name="example"></a>示例

若要使用资源组的位置，请在蓝图定义中或在分配期间设置为另一个项目的位置，在蓝图定义中声明资源组占位符对象。 在此示例中， _NetworkingPlaceholder_是资源组占位符的名称。

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

然后，在面向资源组占位符对象的蓝图项目的上下文中使用 `resourceGroup()` 函数。 在此示例中，模板项目部署到_NetworkingPlaceholder_资源组中，并提供_resourceLocation_动态填充到模板的_NetworkingPlaceholder_资源组位置的参数。 可以在蓝图定义上静态定义_NetworkingPlaceholder_资源组的位置，也可以在分配过程中动态定义该位置。 无论是哪种情况，模板项目都作为参数提供，并使用它将资源部署到正确的位置。

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

返回一个对象，该对象表示指定的资源组项目。 与要求项目上下文的 `resourceGroup()`不同，此函数用于获取特定资源组占位符的属性，而不是在该资源组的上下文中。

### <a name="parameters"></a>parameters

| 参数 | 必选 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| placeholderName |是 |字符串 |要返回的资源组项目的占位符名称。 |

### <a name="return-value"></a>返回值

返回的对象采用以下格式：

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="example"></a>示例

若要使用资源组的位置，请在蓝图定义中或在分配期间设置为另一个项目的位置，在蓝图定义中声明资源组占位符对象。 在此示例中， _NetworkingPlaceholder_是资源组占位符的名称。

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

然后，使用任何蓝图项目上下文中的 `resourceGroups()` 函数获取对资源组占位符对象的引用。 在此示例中，模板项目部署在_NetworkingPlaceholder_资源组外部，并提供 artifactLocation 动态填充到模板的_NetworkingPlaceholder_资源组位置的参数 。 可以在蓝图定义上静态定义_NetworkingPlaceholder_资源组的位置，也可以在分配过程中动态定义该位置。 无论是哪种情况，模板项目都作为参数提供，并使用它将资源部署到正确的位置。

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

返回有关当前蓝图分配的订阅的详细信息。

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

使用订阅的显示名称和 `concat()` 函数创建_作为参数名称_传递到模板项目的命名约定。

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
- 了解如何自定义[蓝图排序顺序](../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../concepts/resource-locking.md)。
- 了解如何[更新现有分配](../how-to/update-existing-assignments.md)。
- 使用[一般故障排除](../troubleshoot/general.md)在蓝图的分配期间解决问题。