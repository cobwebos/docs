---
title: Azure SizeSelector UI 元素 | Microsoft Docs
description: 介绍了 Azure 门户的 Microsoft.Compute.SizeSelector UI 元素。
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
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 9009d29e281ace179ad1dd2021c7cf35e3dc611a
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2018
ms.locfileid: "37084796"
---
# <a name="microsoftcomputesizeselector-ui-element"></a>Microsoft.Compute.SizeSelector UI 元素
一个用于为一个或多个虚拟机实例选择大小的控件。

## <a name="ui-sample"></a>UI 示例

用户会看到一个选择器，其中包含元素定义中的默认值。

![Microsoft.Compute.SizeSelector](./media/managed-application-elements/microsoft.compute.sizeselector.png)

选择控件后，用户会看到可用大小的展开视图。

![展开的 Microsoft.Compute.SizeSelector](./media/managed-application-elements/microsoft.compute.sizeselector-expanded.png)

## <a name="schema"></a>架构
```json
{
  "name": "element1",
  "type": "Microsoft.Compute.SizeSelector",
  "label": "Size",
  "toolTip": "",
  "recommendedSizes": [
    "Standard_D1",
    "Standard_D2",
    "Standard_D3"
  ],
  "constraints": {
    "allowedSizes": [],
    "excludedSizes": [],
    "numAvailabilityZonesRequired": 3,
    "zone": "3"
  },
  "options": {
    "hideDiskTypeFilter": false
  },
  "osPlatform": "Windows",
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "2012-R2-Datacenter"
  },
  "count": 2,
  "visible": true
}
```

## <a name="remarks"></a>备注
- `recommendedSizes` 应至少有一个大小。 第一个建议的大小将用作默认值。 可用大小列表未按建议状态排序。 用户可以选择要按建议状态排序的列。
- 如果某个建议的大小在所选位置中不可用，则会自动跳过该大小。 而且将使用下一个建议的大小。
- `constraints.allowedSizes` 和 `constraints.excludedSizes` 都是可选的，并且不能同时使用。 可以通过调用[为订阅列出可用虚拟机大小](/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region)来确定可用大小的列表。 将隐藏未在 `constraints.allowedSizes` 中指定的任何大小，会显示未在 `constraints.excludedSizes` 中指定的任何大小。
- 必须指定 `osPlatform`，它可以是 **Windows** 或 **Linux**。 它用来确定虚拟机的硬件成本。
- 对于第一方映像，将省略 `imageReference`，对于第三方映像，将提供此参数。 它用来确定虚拟机的软件成本。
- `count` 用来为元素设置合适的乘数。 它支持静态值（例如 **2**），也支持来自其他元素的动态值（例如 `[steps('step1').vmCount]`）。 默认值为 **1**。
- `numAvailabilityZonesRequired` 可以是 1、2 或 3。
- 默认情况下，`hideDiskTypeFilter` 为 **false**。 磁盘类型筛选器允许用户查看所有磁盘类型，或者只查看 SSD。

## <a name="sample-output"></a>示例输出
```json
"Standard_D1"
```

## <a name="next-steps"></a>后续步骤
* 有关创建 UI 定义的简介，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)。
* 有关 UI 元素中的公用属性的说明，请参阅 [CreateUiDefinition 元素](create-uidefinition-elements.md)。
