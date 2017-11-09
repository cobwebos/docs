---
title: "Azure 托管应用程序 Section UI 元素 | Microsoft Docs"
description: "介绍了 Azure 托管应用程序的 Microsoft.Common.Section UI 元素"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: 5a460fde88982c53a7ef3a1ec444d50c1c482fc4
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2017
---
# <a name="microsoftcommonsection-ui-element"></a>Microsoft.Common.Section UI 元素
一个将一个或多个元素分组到同一标题下的控件。 [创建 Azure 托管应用程序](publish-service-catalog-app.md)时使用此元素。

## <a name="ui-sample"></a>UI 示例
![Microsoft.Common.Section](./media/managed-application-elements/microsoft.common.section.png)

## <a name="schema"></a>架构
```json
{
  "name": "section1",
  "type": "Microsoft.Common.Section",
  "label": "Some section",
  "elements": [
    {
      "name": "element1",
      "type": "Microsoft.Common.TextBox",
      "label": "Some text box 1"
    },
    {
      "name": "element2",
      "type": "Microsoft.Common.TextBox",
      "label": "Some text box 2"
    }
  ],
  "visible": true
}
```

## <a name="remarks"></a>备注
- `elements` 必须至少包含一个元素，并且可以包含除了 `Microsoft.Common.Section` 之外的所有元素类型。
- 此元素不支持 `toolTip` 属性。

## <a name="sample-output"></a>示例输出
若要访问 `elements` 中的元素的输出值，请使用 [basics()](create-uidefinition-functions.md#basics) 或 [steps()](create-uidefinition-functions.md#steps) 函数和点表示法：

```json
basics('section1').element1
```

`Microsoft.Common.Section` 类型的元素本身没有输出值。

## <a name="next-steps"></a>后续步骤
* 有关托管应用程序的简介，请参阅 [Azure 托管应用程序概述](overview.md)。
* 有关创建 UI 定义的简介，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)。
* 有关 UI 元素中的公用属性的说明，请参阅 [CreateUiDefinition 元素](create-uidefinition-elements.md)。
