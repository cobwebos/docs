---
title: Azure MultiStorageAccountCombo UI 元素 | Microsoft Docs
description: 介绍了 Azure 门户的 Microsoft.Storage.MultiStorageAccountCombo UI 元素。
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: a4ec5a97f8655c0b5b53dea129d4648a05f6ef85
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2018
---
# <a name="microsoftstoragemultistorageaccountcombo-ui-element"></a>Microsoft.Storage.MultiStorageAccountCombo UI 元素
一组用于创建多个存储帐户的控件，名称以某个公用前缀开头。

## <a name="ui-sample"></a>UI 示例
![Microsoft.Storage.MultiStorageAccountCombo](./media/managed-application-elements/microsoft.storage.multistorageaccountcombo.png)

## <a name="schema"></a>架构
```json
{
  "name": "element1",
  "type": "Microsoft.Storage.MultiStorageAccountCombo",
  "label": {
    "prefix": "Storage account prefix",
    "type": "Storage account type"
  },
  "toolTip": {
    "prefix": "",
    "type": ""
  },
  "defaultValue": {
    "prefix": "sa",
    "type": "Premium_LRS"
  },
  "constraints": {
    "allowedTypes": [],
    "excludedTypes": []
  },
  "count": 2,
  "visible": true
}
```

## <a name="remarks"></a>备注
- 会将 `defaultValue.prefix` 的值与一个或多个整数相连接来生成存储帐户名称的序列。 例如，如果 `defaultValue.prefix` 为 **foobar** 并且 `count` 为 **2**，则会生成存储帐户名称 **foobar1** 和 **foobar2**。 将自动验证生成的存储帐户名称的唯一性。
- 存储帐户名称将基于 `count` 按字典顺序生成。 例如，如果 `count` 为 10，则存储帐户名称以 2 位整数（01、02、03，等等）结尾。
- `defaultValue.prefix` 的默认值为 **null**，`defaultValue.type` 的默认值为 **Premium_LRS**。
- 将隐藏未在 `constraints.allowedTypes` 中指定的任何类型，会显示未在 `constraints.excludedTypes` 中指定的任何类型。
`constraints.allowedTypes` 和 `constraints.excludedTypes` 都是可选的，并且不能同时使用。
- 除了生成存储帐户名称之外，`count` 还用于为元素设置合适的乘数。 它支持静态值（例如 **2**），也支持来自其他元素的动态值（例如 `[steps('step1').storageAccountCount]`）。 默认值为 **1**。

## <a name="sample-output"></a>示例输出
```json
{
  "prefix": "sa",
  "count": 2,
  "resourceGroup": "rg01",
  "type": "Premium_LRS"
}
```

## <a name="next-steps"></a>后续步骤
* 有关创建 UI 定义的简介，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)。
* 有关 UI 元素中的公用属性的说明，请参阅 [CreateUiDefinition 元素](create-uidefinition-elements.md)。
