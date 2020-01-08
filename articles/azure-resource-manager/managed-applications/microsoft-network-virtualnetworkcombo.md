---
title: Microsoft.network.virtualnetworkcombo UI 元素
description: 介绍了 Azure 门户的 Microsoft.Network.VirtualNetworkCombo UI 元素。
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 53c9653b44a6c9d26d49d37b351cf6000676e2d4
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75651964"
---
# <a name="microsoftnetworkvirtualnetworkcombo-ui-element"></a>Microsoft.Network.VirtualNetworkCombo UI 元素

一组用于选择新的或现有虚拟网络的控件。

## <a name="ui-sample"></a>UI 示例

当用户选取一个新的虚拟网络时，用户可以自定义每个子网的名称和地址前缀。 配置子网是可选的。

![Microsoft.Network.VirtualNetworkCombo 新](./media/managed-application-elements/microsoft.network.virtualnetworkcombo-new.png)

当用户选取一个现有虚拟网络时，用户必须将部署模板所需的每个子网映射到现有子网。 在此情况下，配置子网是必需的。

![Microsoft.Network.VirtualNetworkCombo 现有](./media/managed-application-elements/microsoft.network.virtualnetworkcombo-existing.png)

## <a name="schema"></a>架构

```json
{
  "name": "element1",
  "type": "Microsoft.Network.VirtualNetworkCombo",
  "label": {
    "virtualNetwork": "Virtual network",
    "subnets": "Subnets"
  },
  "toolTip": {
    "virtualNetwork": "",
    "subnets": ""
  },
  "defaultValue": {
    "name": "vnet01",
    "addressPrefixSize": "/16"
  },
  "constraints": {
    "minAddressPrefixSize": "/16"
  },
  "options": {
    "hideExisting": false
  },
  "subnets": {
    "subnet1": {
      "label": "First subnet",
      "defaultValue": {
        "name": "subnet-1",
        "addressPrefixSize": "/24"
      },
      "constraints": {
        "minAddressPrefixSize": "/24",
        "minAddressCount": 12,
        "requireContiguousAddresses": true
      }
    },
    "subnet2": {
      "label": "Second subnet",
      "defaultValue": {
        "name": "subnet-2",
        "addressPrefixSize": "/26"
      },
      "constraints": {
        "minAddressPrefixSize": "/26",
        "minAddressCount": 8,
        "requireContiguousAddresses": true
      }
    }
  },
  "visible": true
}
```

## <a name="sample-output"></a>示例输出

```json
{
  "name": "vnet01",
  "resourceGroup": "rg01",
  "addressPrefixes": ["10.0.0.0/16"],
  "newOrExisting": "new",
  "subnets": {
    "subnet1": {
      "name": "subnet-1",
      "addressPrefix": "10.0.0.0/24",
      "startAddress": "10.0.0.1"
    },
    "subnet2": {
      "name": "subnet-2",
      "addressPrefix": "10.0.1.0/26",
      "startAddress": "10.0.1.1"
    }
  }
}
```

## <a name="remarks"></a>备注

- 如果指定，则会根据用户订阅中的现有虚拟网络自动确定大小为 `defaultValue.addressPrefixSize` 的第一个非重叠地址前缀。
- `defaultValue.name` 和 `defaultValue.addressPrefixSize` 的默认值为 **null**。
- 必须指定 `constraints.minAddressPrefixSize`。 地址空间小于指定值的任何现有虚拟网络都不可供选择。
- 必须指定 `subnets`，并且必须为每个子网指定 `constraints.minAddressPrefixSize`。
- 创建新的虚拟网络时，会根据虚拟网络的地址前缀和相应的 `addressPrefixSize` 自动计算每个子网的地址前缀。
- 使用现有虚拟网络时，任何小于相应 `constraints.minAddressPrefixSize` 的子网都不可供选择。 另外，如果指定，则没有至少 `minAddressCount` 个可用地址的子网也不可供选择。 默认值为 **0**。 要确保可用地址是连续的，请将 `requireContiguousAddresses` 指定为 **true**。 默认值为 **true**。
- 不支持在现有虚拟网络中创建子网。
- 如果 `options.hideExisting` 为 **true**，则用户无法选择现有虚拟网络。 默认值是 **false**秒。

## <a name="next-steps"></a>后续步骤

* 有关创建 UI 定义的简介，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)。
* 有关 UI 元素中的公用属性的说明，请参阅 [CreateUiDefinition 元素](create-uidefinition-elements.md)。
