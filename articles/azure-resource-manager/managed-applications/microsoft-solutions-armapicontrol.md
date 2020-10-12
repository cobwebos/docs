---
title: ArmApiControl UI 元素
description: 介绍了 Azure 门户的 Microsoft.Solutions.ArmApiControl UI 元素。 用于调用 API 操作。
author: tfitzmac
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: tomfitz
ms.openlocfilehash: bbe36e072d10b81c421331b2212d8b161afd2693
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87095973"
---
# <a name="microsoftcommonarmapicontrol-ui-element"></a>Microsoft.Common.ArmApiControl UI element

ArmApiControl 可让你从 Azure 资源管理器 API 操作获取结果。 使用结果填充其他控件中的动态内容。

## <a name="ui-sample"></a>UI 示例

此控件没有 UI。

## <a name="schema"></a>架构

下面的示例显示了此控件的架构：

```json
{
    "name": "testApi",
    "type": "Microsoft.Solutions.ArmApiControl",
    "request": {
        "method": "{HTTP-method}",
        "path": "{path-for-the-URL}",
        "body": {
            "key1": "val1",
            "key2": "val2"
        }
    }
}
```

## <a name="sample-output"></a>示例输出

控件的输出不会显示给用户。 相反，操作的结果将用于其他控件。

## <a name="remarks"></a>备注

- `request.method` 属性指定 HTTP 方法。 仅允许 `GET` 或 `POST`。
- `request.path` 属性指定 URL 的相对路径。 它可以是静态路径，也可以通过引用其他控件的输出值来动态构造。
- `request.body` 属性为可选。 使用它指定与请求一起发送的 JSON 正文。 正文可以是静态内容，也可以通过引用其他控件的输出值来动态构造。

## <a name="example"></a>示例

在下面的示例中，`providersApi` 元素调用一个 API 来获取提供程序对象的数组。

`providersDropDown` 元素的 `allowedValues` 属性配置为获取提供程序的名称。 它将显示在下拉列表中。

```json
{
    "name": "providersApi",
    "type": "Microsoft.Solutions.ArmApiControl",
    "request": {
        "method": "GET",
        "path": "[concat(subscription().id, '/providers/Microsoft.Network/expressRouteServiceProviders?api-version=2019-02-01')]"
    }
},
{
    "name": "providerDropDown",
    "type": "Microsoft.Common.DropDown",
    "label": "Provider",
    "toolTip": "The provider that offers the express route connection.",
    "constraints": {
        "allowedValues": "[map(steps('settings').providersApi.value, (item) => parse(concat('{\"label\":\"', item.name, '\",\"value\":\"', item.name, '\"}')))]",
        "required": true
    },
    "visible": true
}
```

有关使用 ArmApiControl 检查资源名称可用性的示例，请参阅 [Microsoft.Common.TextBox](microsoft-common-textbox.md)。

## <a name="next-steps"></a>后续步骤

* 有关创建 UI 定义的简介，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)。
* 有关 UI 元素中的公用属性的说明，请参阅 [CreateUiDefinition 元素](create-uidefinition-elements.md)。
