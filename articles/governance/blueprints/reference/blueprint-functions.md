---
title: Azure 蓝图函数
description: 介绍可用于 Azure 蓝图定义和分配中的蓝图项目的函数。
ms.date: 08/27/2020
ms.topic: reference
ms.openlocfilehash: fe984ee7664b0d50fb891d946f9f40a200ccce09
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89050754"
---
# <a name="functions-for-use-with-azure-blueprints"></a>与 Azure 蓝图一起使用的函数

Azure 蓝图提供了使蓝图定义更加动态的函数。 这些函数用于蓝图定义和蓝图项目。 除了通过蓝图参数获取动态值外，Azure 资源管理器模板 (ARM 模板) 项目支持完整使用资源管理器函数。

支持以下函数：

- [项目](#artifacts)
- [concat](#concat)
- [参数](#parameters)
- [resourceGroup](#resourcegroup)
- [resourceGroups](#resourcegroups)
- [subscription](#subscription)

## <a name="artifacts"></a>项目

`artifacts(artifactName)`

返回由蓝图项目输出填充的属性对象。

> [!NOTE]
> `artifacts()`不能从 ARM 模板内使用该函数。 当使用 Azure PowerShell 或 REST API 作为 [Blueprints-as-code](https://github.com/Azure/azure-blueprints/blob/master/README.md) 的一部分来管理蓝图时，此函数只能在蓝图定义 JSON 或项目 JSON 中使用。

### <a name="parameters"></a>参数

| 参数 | 必选 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| artifactName |是 |字符串 |蓝图项目的名称。 |

### <a name="return-value"></a>返回值

输出属性的对象。 “输出”属性依赖于所引用的蓝图项目的类型。 所有类型都遵循以下格式：

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

#### <a name="arm-template-artifact"></a>ARM 模板项目

返回对象的 **输出** 属性是在 ARM 模板内定义的，由部署返回。

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

带有 ID _myTemplateArtifact_ 的 ARM 模板项目，其中包含以下示例输出属性：

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

从“myTemplateArtifact”示例检索数据的一些示例如下：

| 表达式 | 类型 | 值 |
|:---|:---|:---|
|`[artifacts("myTemplateArtifact").outputs.myArray]` | Array | \["first", "second"\] |
|`[artifacts("myTemplateArtifact").outputs.myArray[0]]` | 字符串 | "first" |
|`[artifacts("myTemplateArtifact").outputs.myString]` | 字符串 | "my string value" |
|`[artifacts("myTemplateArtifact").outputs.myObject]` | 对象 | { "myproperty": "my value", "anotherProperty": true } |
|`[artifacts("myTemplateArtifact").outputs.myObject.myProperty]` | 字符串 | "my value" |
|`[artifacts("myTemplateArtifact").outputs.myObject.anotherProperty]` | Bool | True |

## <a name="concat"></a>concat

`concat(string1, string2, string3, ...)`

组合多个字符串值并返回串联的字符串。

### <a name="parameters"></a>参数

| 参数 | 必选 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| string1 |是 |字符串 |串联的第一个值。 |
| 其他参数 |否 |字符串 |按顺序排列的串联的其他值 |

### <a name="return-value"></a>返回值

串联值的字符串。

### <a name="remarks"></a>备注

Azure 蓝图函数不同于 ARM 模板功能，因为它仅适用于字符串。

### <a name="example"></a>示例

`concat(parameters('organizationName'), '-vm')`

## <a name="parameters"></a>parameters

`parameters(parameterName)`

返回蓝图参数值。 指定的参数名称必须在蓝图定义或蓝图项目中定义。

### <a name="parameters"></a>参数

| 参数 | 必选 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| parameterName |是 |字符串 |要返回的参数名称。 |

### <a name="return-value"></a>返回值

指定的蓝图或蓝图项目参数的值。

### <a name="remarks"></a>备注

Azure 蓝图函数不同于 ARM 模板功能，因为它仅适用于蓝图参数。

### <a name="example"></a>示例

在蓝图定义中定义参数“principalIds”：

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
                    "description": "This is a blueprint parameter that any artifact can reference. We'll display these descriptions for you in the info bubble. Supply principal IDs for the users,groups, or service principals for the Azure role assignment.",
                    "strongType": "PrincipalId"
                }
            }
        },
        ...
    }
}
```

然后使用“principalIds”作为蓝图项目中 `parameters()` 的参数：

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

Azure 蓝图函数不同于 ARM 模板功能。 `resourceGroup()` 函数不能在订阅级项目或蓝图定义中使用。 它只能在作为资源组项目一部分的蓝图项目中使用。

`resourceGroup()` 函数的一个常见用途是在与资源组项目相同的位置中创建资源。

### <a name="example"></a>示例

若要使用在蓝图定义中或分配期间设置的资源组位置作为另一个项目的位置，请在蓝图定义中声明一个资源组占位符对象。 在本例中，“NetworkingPlaceholder”是资源组占位符的名称。

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

然后，在面向资源组占位符对象的蓝图项目的上下文中使用 `resourceGroup()` 函数。 在本例中，模板项目部署到“NetworkingPlaceholder”资源组中，并提供参数“resourceLocation”，动态填充到模板的“NetworkingPlaceholder”资源组位置  。 “NetworkingPlaceholder”资源组的位置可以在蓝图定义上静态定义，也可以在分配期间动态定义。 在这两种情况下，模板项目都会将该信息作为参数提供，并使用它将资源部署到正确的位置。

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

返回表示指定资源组项目的对象。 与需要项目上下文的 `resourceGroup()` 不同，此函数用于在不在资源组上下文中时获取特定资源组占位符的属性。

### <a name="parameters"></a>参数

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

若要使用在蓝图定义中或分配期间设置的资源组位置作为另一个项目的位置，请在蓝图定义中声明一个资源组占位符对象。 在本例中，“NetworkingPlaceholder”是资源组占位符的名称。

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

然后使用任何蓝图项目上下文中的 `resourceGroups()` 函数来获取对资源组占位符对象的引用。 在本例中，模板项目部署到“NetworkingPlaceholder”资源组外，并提供参数“artifactLocation”，动态填充到模板的“NetworkingPlaceholder”资源组位置  。 “NetworkingPlaceholder”资源组的位置可以在蓝图定义上静态定义，也可以在分配期间动态定义。 在这两种情况下，模板项目都会将该信息作为参数提供，并使用它将资源部署到正确的位置。

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

使用订阅的显示名称和 `concat()` 函数创建作为参数“resourceName”传递给模板项目的命名约定。

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