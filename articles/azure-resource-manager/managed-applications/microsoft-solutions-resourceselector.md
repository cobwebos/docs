---
title: ResourceSelector UI 元素
description: 介绍了 Azure 门户的 Microsoft.Solutions.ResourceSelector UI 元素。 用于获取现有资源的列表。
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: d4b21f092733f6ebb3c2bf5aa06c1d2782323730
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87096511"
---
# <a name="microsoftsolutionsresourceselector-ui-element"></a>Microsoft.Solutions.ResourceSelector UI 元素

ResourceSelector 允许用户从订阅中选择现有资源。

## <a name="ui-sample"></a>UI 示例

![Microsoft.Solutions.ResourceSelector](./media/managed-application-elements/microsoft-solutions-resourceselector.png)

## <a name="schema"></a>架构

```json
{
    "name": "storageSelector",
    "type": "Microsoft.Solutions.ResourceSelector",
    "label": "Select storage accounts",
    "resourceType": "Microsoft.Storage/storageAccounts",
    "options": {
        "filter": {
            "subscription": "onBasics",
            "location": "onBasics"
        }
    }
}
```

## <a name="sample-output"></a>示例输出

```json
"name": "{resource-name}",
"id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{resource-provider-namespace}/{resource-type}/{resource-name}",
"location": "{deployed-location}"
```

## <a name="remarks"></a>备注

在 `resourceType` 属性中，为要在列表中显示的资源提供程序命名空间和资源类型名称。

`filter` 属性限制资源的可用选项。 可以按位置或订阅限制结果。 若要仅显示与“基本信息”中的选择匹配的资源，请使用 `onBasics`。 若要显示所有资源，请使用 `all`。 默认值为 `all`。

## <a name="next-steps"></a>后续步骤

* 有关创建 UI 定义的简介，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)。
* 有关 UI 元素中的公用属性的说明，请参阅 [CreateUiDefinition 元素](create-uidefinition-elements.md)。
