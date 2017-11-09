---
title: "Azure 托管应用程序 VirtualNetworkCombo UI 元素 | Microsoft Docs"
description: "介绍了 Azure 托管应用程序的 Microsoft.Network.VirtualNetworkCombo UI 元素"
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
ms.openlocfilehash: c17ef740dcc709b5b344c4e60ef997a948b2e5de
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2017
---
# <a name="microsoftnetworkvirtualnetworkcombo-ui-element"></a>Microsoft.Network.VirtualNetworkCombo UI 元素
一组用于选择新的或现有虚拟网络的控件。 [创建 Azure 托管应用程序](publish-service-catalog-app.md)时使用此元素。

## <a name="ui-sample"></a>UI 示例
![Microsoft.Network.VirtualNetworkCombo](./media/managed-application-elements/microsoft.network.virtualnetworkcombo.png)

- 在顶部的线框中，用户选择了一个新的虚拟网络，因此，用户可以自定义每个子网的名称和地址前缀。 在此情况下，配置子网是可选的。
- 在底部的线框中，用户选择了一个现有虚拟网络，因此，用户必须将部署模板所需的每个子网映射到现有子网。 在此情况下，配置子网是必需的。

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

## <a name="remarks"></a>备注
- 如果指定，则会根据用户订阅中的现有虚拟网络自动确定大小为 `defaultValue.addressPrefixSize` 的第一个非重叠地址前缀。
- `defaultValue.name` 和 `defaultValue.addressPrefixSize` 的默认值为 **null**。
- 必须指定 `constraints.minAddressPrefixSize`。 地址空间小于指定值的任何现有虚拟网络都不可供选择。
- 必须指定 `subnets`，并且必须为每个子网指定 `constraints.minAddressPrefixSize`。
- 创建新的虚拟网络时，会根据虚拟网络的地址前缀和相应的 `addressPrefixSize` 自动计算每个子网的地址前缀。
- 使用现有虚拟网络时，任何小于相应 `constraints.minAddressPrefixSize` 的子网都不可供选择。 另外，如果指定，则没有包含至少 `minAddressCount` 个可用地址的子网也不可供选择。
默认值为 **0**。 要确保可用地址是连续的，请将 `requireContiguousAddresses` 指定为 **true**。 默认值为 **true**。
- 不支持在现有虚拟网络中创建子网。
- 如果 `options.hideExisting` 为 **true**，则用户无法选择现有虚拟网络。 默认值为 **false**。

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

## <a name="next-steps"></a>后续步骤
* 有关托管应用程序的简介，请参阅 [Azure 托管应用程序概述](overview.md)。
* 有关创建 UI 定义的简介，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)。
* 有关 UI 元素中的公用属性的说明，请参阅 [CreateUiDefinition 元素](create-uidefinition-elements.md)。
