---
title: "使用 Azure 网络观察程序安全组视图分析网络安全性 - Azure CLI 1.0 | Microsoft Docs"
description: "本文将介绍如何使用 Azure CLI 1.0 通过安全组视图分析虚拟机安全性。"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: a986ff4f-7e0c-4994-95e1-4ac824986500
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 2c4c494dcc4fe1a85c5feb29506c35fb03066479
ms.contentlocale: zh-cn
ms.lasthandoff: 05/26/2017

---

# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-azure-cli-10"></a>使用 Azure CLI 1.0 通过安全组视图分析虚拟机安全性

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [CLI 1.0](network-watcher-security-group-view-cli-nodejs.md)
> - [CLI 2.0](network-watcher-security-group-view-cli.md)
> - [REST API](network-watcher-security-group-view-rest.md)

安全组视图返回已应用于虚拟机的已配置的有效网络安全规则。 此功能可用于审核和诊断已在 VM 上配置以确保正确允许或拒绝流量的网络安全组和规则。 在本文中，我们将说明如何使用 Azure CLI 检索虚拟机的已配置的有效安全规则

本文使用适用于 Windows、Mac 和 Linux 的跨平台 Azure CLI 1.0。 对于 CLI 支持，网络观察程序当前使用 Azure CLI 1.0。

## <a name="before-you-begin"></a>开始之前

此方案假定你已按照[创建网络观察程序](network-watcher-create.md)中的步骤创建网络观察程序。

## <a name="scenario"></a>方案

本文中介绍的方案检索给定虚拟机的已配置有效安全规则。

## <a name="get-a-vm"></a>获取 VM

需要虚拟机才能运行 `vm list` cmdlet。 以下命令列出资源组中的虚拟机：

```azurecli
azure vm list -g resourceGroupName
```

知道虚拟机后，可以使用 `vm show` cmdlet 获取其资源 ID：

```azurecli
azure vm show -g resourceGroupName -n virtualMachineName
```

## <a name="retrieve-security-group-view"></a>检索安全组视图

下一步是检索安全组视图结果。 添加“--json”标志将设置 json 中结果的格式。

```azurecli
azure network watcher security-group-view -g resourceGroupName -n networkWatcherName -t targetResourceId --json
```

## <a name="viewing-the-results"></a>查看结果

以下示例是返回的结果的缩短响应。 该结果显示虚拟机上所有已应用的有效安全规则，分为以下几组：**NetworkInterfaceSecurityRules**、**DefaultSecurityRules** 和 **EffectiveSecurityRules**。

```json
{
  "networkInterfaces": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testnic",
      "securityRuleAssociations": {
        "networkInterfaceAssociation": {
          "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testvm",
          "securityRules": [
            {
              "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/test-nsg/securityRules/default-allow-rdp",
              "protocol": "TCP",
              "sourcePortRange": "*",
              "destinationPortRange": "3389",
              "sourceAddressPrefix": "*",
              "destinationAddressPrefix": "*",
              "access": "Allow",
              "priority": 1000,
              "direction": "Inbound",
              "provisioningState": "Succeeded",
              "name": "default-allow-rdp",
              "etag": "W/\"00000000-0000-0000-0000-000000000000\""
            }
          ]
        },
        "defaultSecurityRules": [
          {
            "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/networkSecurityGroups//defaultSecurityRules/",
            "description": "Allow inbound traffic from all VMs in VNET",
            "protocol": "*",
            "sourcePortRange": "*",
            "destinationPortRange": "*",
            "sourceAddressPrefix": "VirtualNetwork",
            "destinationAddressPrefix": "VirtualNetwork",
            "access": "Allow",
            "priority": 65000,
            "direction": "Inbound",
            "provisioningState": "Succeeded",
            "name": "AllowVnetInBound"
          }
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>后续步骤

请访问[使用网络观察程序审核网络安全组 (NSG)](network-watcher-nsg-auditing-powershell.md)，了解如何自动执行网络安全组的验证。

请访问[安全组视图概述](network-watcher-security-group-view-overview.md)，了解有关应用于网络资源的安全规则的详细信息

