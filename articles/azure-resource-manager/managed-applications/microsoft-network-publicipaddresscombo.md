---
title: Microsoft.network.publicipaddresscombo UI 元素
description: 介绍了 Azure 门户的 Microsoft.Network.PublicIpAddressCombo UI 元素。
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 0393673663df8f3ca580ff34e16bee910b955f8d
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75651912"
---
# <a name="microsoftnetworkpublicipaddresscombo-ui-element"></a>Microsoft.Network.PublicIpAddressCombo UI 元素

一组用于选择新的或现有公共 IP 地址的控件。

## <a name="ui-sample"></a>UI 示例

![Microsoft.Network.PublicIpAddressCombo](./media/managed-application-elements/microsoft.network.publicipaddresscombo.png)

- 如果用户针对公共 IP 地址选择了“None”，则域名标签文本框会被隐藏。
- 如果用户选择了一个现有公共 IP 地址，则域名标签文本框会被禁用。 它的值是所选 IP 地址的域名标签。
- 域名后缀（例如 westus.cloudapp.azure.com）会基于所选位置自动更新。

## <a name="schema"></a>架构

```json
{
  "name": "element1",
  "type": "Microsoft.Network.PublicIpAddressCombo",
  "label": {
    "publicIpAddress": "Public IP address",
    "domainNameLabel": "Domain name label"
  },
  "toolTip": {
    "publicIpAddress": "",
    "domainNameLabel": ""
  },
  "defaultValue": {
    "publicIpAddressName": "ip01",
    "domainNameLabel": "mydomain"
  },
  "constraints": {
    "required": {
      "domainNameLabel": true
    }
  },
  "options": {
    "hideNone": false,
    "hideDomainNameLabel": false,
    "hideExisting": false,
    "zone": 3
  },
  "visible": true
}
```

## <a name="sample-output"></a>示例输出

如果用户未选择公共 IP 地址，则控件返回以下输出：

```json
{
  "newOrExistingOrNone": "none"
}
```

如果用户选择了新的或现有 IP 地址，则控件返回以下输出：

```json
{
  "name": "ip01",
  "resourceGroup": "rg01",
  "domainNameLabel": "mydomain",
  "publicIPAllocationMethod": "Dynamic",
  "sku": "Basic",
  "newOrExistingOrNone": "new"
}
```

- 当 `options.hideNone` 指定为 **true** 时，`newOrExistingOrNone` 将仅具有值 **new** 或 **existing**。
- 当 `options.hideDomainNameLabel` 指定为 **true** 时，将取消声明 `domainNameLabel`。

## <a name="remarks"></a>备注

- 如果 `constraints.required.domainNameLabel` 设置为 **true**，则用户在创建新的公共 IP 地址时必须提供域名标签。 没有标签的现有公共 IP 地址不可供选择。
- 如果 `options.hideNone` 设置为 **true**，则用于为公共 IP 地址选择 **None** 的选项会被隐藏。 默认值是 **false**秒。
- 如果 `options.hideDomainNameLabel` 设置为 **true**，则域名标签的文本框会被隐藏。 默认值是 **false**秒。
- 如果 `options.hideExisting` 为 true，则用户无法选择现有的公共 IP 地址。 默认值是 **false**秒。
- 对于 `zone`，仅指定区域的公共 IP 地址或区域复原公共 IP 地址可用。

## <a name="next-steps"></a>后续步骤

* 有关创建 UI 定义的简介，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)。
* 有关 UI 元素中的公用属性的说明，请参阅 [CreateUiDefinition 元素](create-uidefinition-elements.md)。
