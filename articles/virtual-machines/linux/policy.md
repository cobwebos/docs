---
title: 在 Azure 中采用 Linux VM 上的策略强制执行安全措施 | Microsoft Docs
description: 如何将策略应用到 Azure 资源管理器 Linux 虚拟机
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 06778ab4-f8ff-4eed-ae10-26a276fc3faa
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: singhkay
ms.openlocfilehash: 12066fe622ec3ed2eded74ecf7b791689ed873d5
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="apply-policies-to-linux-vms-with-azure-resource-manager"></a>使用 Azure 资源管理器向 Linux VM 应用策略
通过使用策略，组织可以在整个企业中强制实施各种约定和规则。 强制实施所需行为有助于消除风险，同时为组织的成功做出贡献。 本文将介绍如何使用 Azure 资源管理器策略，为组织中的虚拟机定义相应行为。

有关策略的简介，请参阅[什么是 Azure 策略？](../../azure-policy/azure-policy-introduction.md)。

## <a name="permitted-virtual-machines"></a>允许的虚拟机
若要确保组织的虚拟机与应用程序兼容，可以限制获准操作系统。 在以下策略示例中，只允许创建 Ubuntu 14.04.2-LTS 虚拟机。

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "in": [
          "Microsoft.Compute/disks",
          "Microsoft.Compute/virtualMachines",
          "Microsoft.Compute/VirtualMachineScaleSets"
        ]
      },
      {
        "not": {
          "allOf": [
            {
              "field": "Microsoft.Compute/imagePublisher",
              "in": [
                "Canonical"
              ]
            },
            {
              "field": "Microsoft.Compute/imageOffer",
              "in": [
                "UbuntuServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageSku",
              "in": [
                "14.04.2-LTS"
              ]
            },
            {
              "field": "Microsoft.Compute/imageVersion",
              "in": [
                "latest"
              ]
            }
          ]
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

使用通配符将上述策略修改为允许任何 Ubuntu LTS 映像： 

```json
{
  "field": "Microsoft.Compute/virtualMachines/imageSku",
  "like": "*LTS"
}
```

若要了解策略字段，请参阅[策略别名](../../azure-policy/policy-definition.md#aliases)。

## <a name="managed-disks"></a>托管磁盘

如果需要使用托管磁盘，请使用以下策略：

```json
{
  "if": {
    "anyOf": [
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/virtualMachines"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/osDisk.uri",
            "exists": true
          }
        ]
      },
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/VirtualMachineScaleSets"
          },
          {
            "anyOf": [
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osDisk.vhdContainers",
                "exists": true
              },
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osdisk.imageUrl",
                "exists": true
              }
            ]
          }
        ]
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="images-for-virtual-machines"></a>虚拟机映像

出于安全考虑，可要求在环境中部署仅已批准的自定义映像。 可以指定包含已批准映像的资源组，或特定已批准映像。

下例需要来自已批准资源组的映像：

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "in": [
                    "Microsoft.Compute/virtualMachines",
                    "Microsoft.Compute/VirtualMachineScaleSets"
                ]
            },
            {
                "not": {
                    "field": "Microsoft.Compute/imageId",
                    "contains": "resourceGroups/CustomImage"
                }
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
} 
```

下例指定已批准的映像 ID：

```json
{
    "field": "Microsoft.Compute/imageId",
    "in": ["{imageId1}","{imageId2}"]
}
```

## <a name="virtual-machine-extensions"></a>虚拟机扩展

建议禁止某些扩展类型的使用情况。 例如，扩展名可能与某些自定义虚拟机映像不兼容。 下例演示如何阻止特定扩展。 该示例使用发布者和类型来确定要阻止的扩展。

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Compute/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                "equals": "Microsoft.Compute"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/type",
                "equals": "{extension-type}"

      }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```


## <a name="next-steps"></a>后续步骤
* 定义策略规则后（如上述示例所示），需要创建策略定义并将其分配给作用域。 作用域可以是订阅、资源组或资源。 若要分配策略，请参阅[使用 Azure 门户分配和管理资源策略](../../azure-policy/assign-policy-definition.md)、[使用 PowerShell 分配策略](../../azure-policy/assign-policy-definition-ps.md)或[使用 Azure CLI 分配策略](../../azure-policy/assign-policy-definition-cli.md)。
* 有关资源策略的简介，请参阅[什么是 Azure 策略？](../../azure-policy/azure-policy-introduction.md)。
* 有关企业可如何使用 Resource Manager 有效管理订阅的指南，请参阅 [Azure 企业基架 - 出于合规目的监管订阅](../../azure-resource-manager/resource-manager-subscription-governance.md)。
