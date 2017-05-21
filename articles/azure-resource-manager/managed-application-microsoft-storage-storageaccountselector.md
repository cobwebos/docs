---
title: "Azure 托管应用程序 StorageAccountSelector UI 元素 | Microsoft Docs"
description: "介绍了 Azure 托管应用程序的 Microsoft.Storage.StorageAccountSelector UI 元素"
services: azure-resource-manager
documentationcenter: na
author: tabrezm
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2017
ms.author: tabrezm;tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 2d64cfcd958f17171e9ef1a38777f202bdfda774
ms.contentlocale: zh-cn
ms.lasthandoff: 05/11/2017


---
# <a name="microsoftstoragestorageaccountselector-ui-element"></a>Microsoft.Storage.StorageAccountSelector UI 元素
一个用于选择新的或现有存储帐户的控件。

## <a name="ui-sample"></a>UI 示例
![Microsoft.Storage.StorageAccountSelector](./media/managed-application-elements/microsoft.storage.storageaccountselector.png)

## <a name="schema"></a>架构
```json
{
  "name": "element1",
  "type": "Microsoft.Storage.StorageAccountSelector",
  "label": "Storage account",
  "toolTip": "",
  "defaultValue": {
    "name": "storageaccount01",
    "type": "Premium_LRS"
  },
  "constraints": {
    "allowedTypes": [],
    "excludedTypes": []
  },
  "options": {
    "hideExisting": false
  },
  "visible": true
}
```

## <a name="remarks"></a>备注
- 如果指定，则会自动验证 `defaultValue.name` 的唯一性。 如果存储帐户名称不唯一，则用户必须指定一个不同的名称或选择现有存储帐户。
- `defaultValue.type` 的默认值为 **Premium_LRS**。
- 将隐藏未在 `constraints.allowedTypes` 中指定的任何类型，将显示未在 `constraints.excludedTypes` 中指定的任何类型。
`constraints.allowedTypes` 和 `constraints.excludedTypes` 都是可选的，并且不能同时使用。
- 如果 `options.hideExisting` 为 **true**，则用户无法选择现有存储帐户。 默认值为 **false**。


## <a name="sample-output"></a>示例输出
```json
{
  "name": "storageaccount01",
  "resourceGroup": "rg01",
  "type": "Premium_LRS",
  "newOrExisting": "new"
}
```

## <a name="next-steps"></a>后续步骤
* 有关创建 UI 定义的简介，请参阅 [CreateUiDefinition 入门](managed-application-createuidefinition-overview.md)。
* 有关 UI 元素中的公用属性的说明，请参阅 [CreateUiDefinition 元素](managed-application-createuidefinition-elements.md)。

