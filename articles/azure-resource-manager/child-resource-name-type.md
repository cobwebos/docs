---
title: Azure 资源管理器模板中的子资源的名称和类型
description: 介绍如何在 Azure 资源管理器模板中设置子资源的名称和类型。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: tomfitz
ms.openlocfilehash: bd1c38547866c34eb5c4808ed5b185b50fa44a01
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035559"
---
# <a name="set-name-and-type-for-child-resources"></a>设置子资源的名称和类型

子资源是仅存在于另一资源的上下文中的资源。 例如, 在没有[虚拟机](/azure/templates/microsoft.compute/2019-03-01/virtualmachines)的情况下,[虚拟机扩展](/azure/templates/microsoft.compute/2019-03-01/virtualmachines/extensions)不存在。 扩展资源是虚拟机的子资源。

在资源管理器模板中, 可以在父资源内或父资源之外指定子资源。 下面的示例演示了父资源的资源属性中包含的子资源。

```json
"resources": [
  {
    <parent-resource>
    "resources": [
      <child-resource>
    ]
  }
]
```

下一个示例演示父资源之外的子资源。 如果父资源未部署在同一个模板中, 或者如果要使用[copy](resource-group-create-multiple.md)来创建多个子资源, 则可以使用此方法。

```json
"resources": [
  {
    <parent-resource>
  },
  {
    <child-resource>
  }
]
```

你为资源名称和类型提供的值根据子资源是在父资源内部还是外部定义的而有所不同。

## <a name="within-parent-resource"></a>父资源内

在父资源类型中定义时, 可以将类型和名称值的格式设置为一个不带斜杠的单词。

```json
"type": "{child-resource-type}",
"name": "{child-resource-name}",
```

以下示例显示了一个虚拟网络和一个子网。 请注意, 子网包含在虚拟网络的资源阵列中。 名称设置为**Subnet1** , 类型设置为**子网**。 子资源被标记为依赖于父资源, 因为父资源必须存在, 然后才能部署子资源。

```json
"resources": [
  {
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    },
    "resources": [
      {
        "apiVersion": "2018-10-01",
        "type": "subnets",
        "location": "[parameters('location')]",
        "name": "Subnet1",
        "dependsOn": [
          "VNet1"
        ],
        "properties": {
          "addressPrefix": "10.0.0.0/24"
        }
      }
    ]
  }
]
```

完整资源类型仍为 " **virtualNetworks/子网**"。 不提供**virtualNetworks/** , 因为它是从父资源类型中假定的。

子资源名称设置为**Subnet1** , 但完整名称包括父名称。 不提供**VNet1** , 因为它是从父资源中假定的。

## <a name="outside-parent-resource"></a>父资源外

在父资源外定义时, 可以将类型和带斜杠的格式设置为包含父类型和名称。

```json
"type": "{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}",
"name": "{parent-resource-name}/{child-resource-name}",
```

以下示例显示了在根级别定义的虚拟网络和子网。 请注意, 此子网不包含在虚拟网络的资源阵列中。 名称设置为**VNet1/Subnet1** , 类型设置为 " **virtualNetworks/子网**"。 子资源被标记为依赖于父资源, 因为父资源必须存在, 然后才能部署子资源。

```json
"resources": [
  {
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    }
  },
  {
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Network/virtualNetworks/subnets",
    "location": "[parameters('location')]",
    "name": "VNet1/Subnet1",
    "dependsOn": [
      "VNet1"
    ],
    "properties": {
      "addressPrefix": "10.0.0.0/24"
    }
  }
]
```

## <a name="next-steps"></a>后续步骤

* 若要了解有关创建 Azure 资源管理器模板的信息，请参阅[创作模板](resource-group-authoring-templates.md)。 

* 若要了解引用资源时资源名称的格式, 请参阅[reference 函数](resource-group-template-functions-resource.md#reference)。
