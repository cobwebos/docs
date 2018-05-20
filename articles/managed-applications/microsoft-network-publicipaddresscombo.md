---
title: Azure PublicIpAddressCombo UI 元素 | Microsoft Docs
description: 介绍了 Azure 门户的 Microsoft.Network.PublicIpAddressCombo UI 元素。
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
ms.date: 04/30/2018
ms.author: tomfitz
ms.openlocfilehash: c308b6626f9c37b3928107c4c03e9e0a5da12e6f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="microsoftnetworkpublicipaddresscombo-ui-element"></a>Microsoft.Network.PublicIpAddressCombo UI 元素
一组用于选择新的或现有公用 IP 地址的控件。

## <a name="ui-sample"></a>UI 示例
![Microsoft.Network.PublicIpAddressCombo](./media/managed-application-elements/microsoft.network.publicipaddresscombo.png)

- 如果用户针对公用 IP 地址选择了“None”，则域名标签文本框会被隐藏。
- 如果用户选择了一个现有公用 IP 地址，则域名标签文本框会被禁用。 它的值是所选 IP 地址的域名标签。
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

## <a name="remarks"></a>备注
- 如果 `constraints.required.domainNameLabel` 设置为 **true**，则用户在创建新的公用 IP 地址时必须提供域名标签。 没有标签的现有公用 IP 地址不可供选择。
- 如果 `options.hideNone` 设置为 **true**，则用于为公用 IP 地址选择 **None** 的选项会被隐藏。 默认值为 **false**。
- 如果 `options.hideDomainNameLabel` 设置为 **true**，则域名标签的文本框会被隐藏。 默认值为 **false**。
- 如果 `options.hideExisting` 为 true，则用户无法选择现有的公用 IP 地址。 默认值为 **false**。
- 对于 `zone`，仅指定区域的公共 IP 地址或区域复原公共 IP 地址可用。

## <a name="sample-output"></a>示例输出
如果用户未选择公用 IP 地址，则应当生成以下输出：
```json
{
  "newOrExistingOrNone": "none"
}
```

如果用户选择了新的或现有 IP 地址，则应当生成以下输出：
```json
{
  "name": "ip01",
  "resourceGroup": "rg01",
  "domainNameLabel": "mydomain",
  "publicIPAllocationMethod": "Dynamic",
  "newOrExistingOrNone": "new"
}
```
- 当 `options.hideNone` 指定为 **true** 时，`newOrExistingOrNone` 将仅具有值 **new** 或 **existing**。
- 当 `options.hideDomainNameLabel` 指定为 **true** 时，将取消声明 `domainNameLabel`。

## <a name="next-steps"></a>后续步骤
* 有关创建 UI 定义的简介，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)。
* 有关 UI 元素中的公用属性的说明，请参阅 [CreateUiDefinition 元素](create-uidefinition-elements.md)。
