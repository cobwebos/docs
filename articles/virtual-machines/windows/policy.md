---
title: 通过 Azure 中 Windows VM 上的策略强制执行安全性 | Microsoft Docs
description: 如何将策略应用到 Azure 资源管理器 Windows 虚拟机
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 0b71ba54-01db-43ad-9bca-8ab358ae141b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: kasing
ms.openlocfilehash: fc95b57c244e855f6788b7f21fc9abc0eba3d44d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34654702"
---
# <a name="apply-policies-to-windows-vms-with-azure-resource-manager"></a>使用 Azure 资源管理器向 Windows VM 应用策略
通过使用策略，组织可以在整个企业中强制实施各种约定和规则。 强制实施所需行为有助于消除风险，同时为组织的成功做出贡献。 本文将介绍如何使用 Azure 资源管理器策略，为组织中的虚拟机定义相应行为。

有关策略的简介，请参阅[什么是 Azure 策略？](../../azure-policy/azure-policy-introduction.md)。

## <a name="permitted-virtual-machines"></a>允许的虚拟机
若要确保组织的虚拟机与应用程序兼容，可以限制获准操作系统。 在以下策略示例中，只允许创建 Windows Server 2012 R2 数据中心虚拟机：

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
                "MicrosoftWindowsServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageOffer",
              "in": [
                "WindowsServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageSku",
              "in": [
                "2012-R2-Datacenter"
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

使用通配符将上述策略修改为允许任何 Windows Server Datacenter 映像：

```json
{
  "field": "Microsoft.Compute/imageSku",
  "like": "*Datacenter"
}
```

使用 anyOf 将上述策略修改为允许任何 Windows Server 2012 R2 Datacenter 或更高版本的映像：

```json
{
  "anyOf": [
    {
      "field": "Microsoft.Compute/imageSku",
      "like": "2012-R2-Datacenter*"
    },
    {
      "field": "Microsoft.Compute/imageSku",
      "like": "2016-Datacenter*"
    }
  ]
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


## <a name="azure-hybrid-use-benefit"></a>Azure 混合使用权益

如果具有本地许可证，可在虚拟机上保存许可证费用。 如果没有许可证，应禁用此选项。 以下策略禁止使用 Azure 混合使用权益 (AHUB)：

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "in":[ "Microsoft.Compute/virtualMachines","Microsoft.Compute/VirtualMachineScaleSets"]
            },
            {
                "field": "Microsoft.Compute/licenseType",
                "exists": true
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
* 有关企业可如何使用 Resource Manager 有效管理订阅的指南，请参阅 [Azure 企业基架 - 出于合规目的监管订阅](/azure/architecture/cloud-adoption-guide/subscription-governance)。