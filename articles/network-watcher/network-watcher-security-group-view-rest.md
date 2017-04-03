---
title: "使用 Azure 网络观察程序安全组视图分析网络安全性 - REST API | Microsoft 文档"
description: "本文将介绍如何使用 PowerShell 通过安全组视图分析虚拟机安全性。"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: a2f418fe-f5d2-43ed-8dc3-df0ed2a4d4ac
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: fa79ab0ab80481c8969c6fa653e92b10985cab3b
ms.openlocfilehash: 80ae80d3243b531c7348a709aa4ad6e6ed401980
ms.lasthandoff: 03/31/2017

---

# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-rest-api"></a>使用 REST API 通过安全组视图分析虚拟机安全性

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [CLI](network-watcher-security-group-view-cli.md)
> - [REST API](network-watcher-security-group-view-rest.md)

安全组视图返回已应用于虚拟机的已配置的有效网络安全规则。 此功能可用于审核和诊断已在 VM 上配置以确保正确允许或拒绝流量的网络安全组和规则。 在本文中，我们将说明如何使用 REST API 检索应用于虚拟机的有效安全规则

## <a name="before-you-begin"></a>开始之前

在此方案中，你将调用网络观察程序 Rest API 获取虚拟机的安全组视图。 ARMclient 用于使用 PowerShell 调用 REST API。 按照 [Chocolatey 上的 ARMClient](https://chocolatey.org/packages/ARMClient) 在 chocolatey 上找到 ARMClient

此方案假定你已按照[创建网络观察程序](network-watcher-create.md)中的步骤创建网络观察程序。 此方案还假定要使用的是存在有效虚拟机的资源组。

## <a name="scenario"></a>方案

本文中介绍的方案检索给定虚拟机的已应用有效安全规则。

## <a name="log-in-with-armclient"></a>使用 ARMClient 登录

```PowerShell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>检索虚拟机

运行以下脚本返回虚拟机。以下代码需要变量：

- **subscriptionId** - 还可以使用 **Get-AzureRMSubscription** cmdlet 检索订阅 id。
- **resourceGroupName** - 包含虚拟机的资源组的名称。

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = '<resource group name>'

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

所需的信息是响应中类型 `Microsoft.Compute/virtualMachines` 下的 **id**，如以下示例中所示：

```json
...,
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft
.Network/networkInterfaces/{nicName}"
            }
          ]
        },
        "provisioningState": "Succeeded"
      },
      "resources": [
        {
          "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Com
pute/virtualMachines/{vmName}/extensions/CustomScriptExtension"
        }
      ],
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute
/virtualMachines/{vmName}",
      "name": "{vmName}"
    }
  ]
}
```

## <a name="get-security-group-view-for-virtual-machine"></a>获取虚拟机的安全组视图

以下示例请求目标虚拟机的安全组视图。 本示例的结果可用于比较规则和来源定义的安全性以查找配置漂移。

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"
$networkWatcherName = "<network watcher name>"
$targetUri = "<uri of target resource>" # Example: /subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.compute/virtualMachine/$vmName

$requestBody = @"
{
    'targetResourceId': '${targetUri}'

}
"@
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/securityGroupView?api-version=2016-12-01" $requestBody -verbose
```

## <a name="view-the-response"></a>查看响应

以下示例是从前一命令返回的响应。 该结果显示虚拟机上所有已应用的有效安全规则，分为以下几组：**NetworkInterfaceSecurityRules**、**DefaultSecurityRules** 和 **EffectiveSecurityRules**。

```json

{
  "networkInterfaces": [
    {
      "securityRuleAssociations": {
        "networkInterfaceAssociation": {
          "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
          "securityRules": [
            {
              "name": "default-allow-rdp",
              "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/securityRules/default-allow-rdp",
              "etag": "W/\"d4c411d4-0d62-49dc-8092-3d4b57825740\"",
              "properties": {
                "provisioningState": "Succeeded",
                "protocol": "TCP",
                "sourcePortRange": "*",
                "destinationPortRange": "3389",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 1000,
                "direction": "Inbound"
              }
            }
          ]
        },
        "defaultSecurityRules": [
          {
            "name": "AllowVnetInBound",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/defaultSecurityRules/",
            "properties": {
              "provisioningState": "Succeeded",
              "description": "Allow inbound traffic from all VMs in VNET",
              "protocol": "*",
              "sourcePortRange": "*",
              "destinationPortRange": "*",
              "sourceAddressPrefix": "VirtualNetwork",
              "destinationAddressPrefix": "VirtualNetwork",
              "access": "Allow",
              "priority": 65000,
              "direction": "Inbound"
            }
          },
          ...
        ],
        "effectiveSecurityRules": [
          {
            "name": "DefaultOutboundDenyAll",
            "protocol": "All",
            "sourcePortRange": "0-65535",
            "destinationPortRange": "0-65535",
            "sourceAddressPrefix": "*",
            "destinationAddressPrefix": "*",
            "access": "Deny",
            "priority": 65500,
            "direction": "Outbound"
          },
          ...
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>后续步骤

请访问[使用网络观察程序审核网络安全组 (NSG)](network-watcher-security-group-view-powershell.md)，了解如何自动执行网络安全组的验证。



