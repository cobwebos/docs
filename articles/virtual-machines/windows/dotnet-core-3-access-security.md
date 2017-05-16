---
title: "Windows VM 的 Azure 模板中的访问权限和安全性 | Microsoft 文档"
description: "Azure 虚拟机 DotNet 核心教程"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: e671fc45-5e4d-40fd-aac5-290892713cc0
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/12/2017
ms.author: nepeters
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: db267e4374d8786e65078843e8bcc4225085d5c8
ms.contentlocale: zh-cn
ms.lasthandoff: 03/31/2017

---
# <a name="access-and-security-in-azure-resource-manager-templates-for-windows-vms"></a>适用于 Windows VM 的 Azure Resource Manager 模板中的访问权限和安全性

可能需要通过 Internet 或与 Azure 建立的 VPN/Express Route 连接才能访问托管在 Azure 中的应用程。 在音乐应用商店应用程序示例中，网站通过公共 IP 地址在 Internet 上提供访问。 建立访问方式后，应该保护对应用程序的连接，以及对虚拟机资源本身的访问。 这种访问安全性是通过网络安全组提供的。 

本文档详细说明如何在示例 Azure Resource Manager 模板中保护音乐应用商店应用程序。 所有依赖项和独特配置都已突出显示。 为了获得最佳体验，请将一个解决方案实例预先部署到 Azure 订阅，然后将它与 Azure Resource Manager 模板配合运行。 可在此处找到完整模板 - [Windows 上的音乐商店部署](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)。

## <a name="public-ip-address"></a>公共 IP 地址
若要提供对 Azure 资源的公共访问，可以使用公共 IP 地址。 可以使用静态或动态 IP 地址来配置公共 IP 地址。 如果使用动态地址，当虚拟机被停止和解除分配时，系统将删除该地址。 当计算机重新启动时，系统可能为它分配不同的公共 IP 地址。 若要防止 IP 地址更改，可以使用保留 IP 地址。 

可通过使用 Visual Studio 中的“添加新资源向导”或者在模板中插入有效 JSON，将公共 IP 地址添加到 Azure Resource Manager 模板中。 

单击以下链接可查看 Resource Manager 模板中的 JSON 示例 – [公共 IP 地址](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L110)。

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "[variables('publicIpAddressName')]",
  "location": "[resourceGroup().location]",
  "dependsOn": [],
  "tags": {
    "displayName": "public-ip"
  },
  "properties": {
    "publicIPAllocationMethod": "Dynamic",
    "dnsSettings": {
      "domainNameLabel": "[parameters('publicipaddressDnsName')]"
    }
  }
}
```

公共 IP 地址可与虚拟网络适配器或负载均衡器关联。 在本示例中，由于音乐应用商店网站的负载在多个虚拟机之间均衡，因此公共 IP 地址已附加到负载均衡器。

单击以下链接可查看 Resource Manager 模板中的 JSON 示例 – [公共 IP 地址与负载均衡器的关联](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L211)。

```json
"frontendIPConfigurations": [
  {
    "properties": {
      "publicIPAddress": {
        "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIpAddressName'))]"
      }
    },
    "name": "LoadBalancerFrontend"
  }
]
```

Azure 门户中显示的公共 IP 地址。 请注意，公共 IP 地址与负载均衡器而不是虚拟机关联。 本系列教程的下一篇文档详细介绍了网络负载均衡器。

![公共 IP 地址](./media/dotnet-core-3-access-security/pubip-win.png)

有关 Azure 公共 IP 地址的详细信息，请参阅 [Azure 中的 IP 地址](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)。

## <a name="network-security-group"></a>网络安全组
与 Azure 资源建立访问后，应该对此访问进行限制。 对于 Azure 虚拟机，可以使用网络安全组来实现保护访问的目的。 在音乐商店应用程序示例中，除了通过端口 80 进行的 http 访问和通过端口 3389 进行的 RDP 访问以外，所有对虚拟机的访问都受到限制。 可通过使用 Visual Studio 中的“添加新资源向导”或者在模板中插入有效 JSON，将网络安全组添加到 Azure Resource Manager 模板中。

单击以下链接可查看 Resource Manager 模板中的 JSON 示例 – [网络安全组](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L57)。

```json
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Network/networkSecurityGroups",
  "name": "[variables('networkSecurityGroup')]",
  "location": "[resourceGroup().location]",
  "tags": {
    "displayName": "network-security-group"
  },
  "properties": {
    "securityRules": [
      {
        "name": "http",
        "properties": {
          "description": "http endpoint",
          "protocol": "Tcp",
          "sourcePortRange": "*",
          "destinationPortRange": "80",
          "sourceAddressPrefix": "*",
          "destinationAddressPrefix": "*",
          "access": "Allow",
          "priority": 100,
          "direction": "Inbound"
        }
      },
      ........<truncated> 
    ]
  }
},
```

在本示例中，网络安全组与虚拟网络资源中声明的子网对象关联。 

单击以下链接可查看 Resource Manager 模板中的 JSON 示例 – [网络安全组与虚拟网络的关联](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-windows/azuredeploy.json#L143)。

```json
"subnets": [
  {
    "name": "[variables('subnetName')]",
    "properties": {
      "addressPrefix": "10.0.0.0/24",
      "networkSecurityGroup": {
        "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroup'))]"
      }
    }
  }
]
```

Azure 门户中的网络安全组如下所示。 请注意，NSG 可与子网和/或网络接口关联。 在本例中，NSG 与子网关联。 在此配置中，入站规则应用到与子网连接的所有虚拟机。

![网络安全组](./media/dotnet-core-3-access-security/nsg-win.png)

有关网络安全组的深入信息，请参阅[什么是网络安全组](https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/)。

## <a name="next-step"></a>后续步骤
<hr>

[步骤 3 - Azure Resource Manager 模板的可用性和缩放](dotnet-core-4-availability-scale.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)


