---
title: 在 Azure 虚拟网络中使用标准负载均衡器部署 IPv6 双重堆栈应用程序-资源管理器模板 (预览版)
titlesuffix: Azure Virtual Network
description: 本文介绍如何使用 Azure 资源管理器 VM 模板在 Azure 虚拟网络中部署具有标准负载均衡器的 IPv6 双重堆栈应用程序。
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 07/15/2019
ms.author: kumud
ms.openlocfilehash: fa39285eea14856db1bceba9e90f92b19afabfd0
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295409"
---
# <a name="deploy-an-ipv6-dual-stack-application-with-standard-load-balancer-in-azure---template-preview"></a>使用 Azure 模板 (预览版) 中的标准负载均衡器部署 IPv6 双重堆栈应用程序

本文提供了 Azure 配置任务的列表, 其中包含适用于的部分 Azure 资源管理器 VM 模板。 使用本文中所述的模板在 Azure 中部署一个双堆栈 (IPv4 + IPv6) 应用程序, 该应用程序包含带有 IPv4 和 IPv6 子网的双堆栈虚拟网络、带有双重 (IPv4 + IPv6) 前端配置的负载均衡器、具有具有双 IP 的 Nic 的 Vm配置、网络安全组和公共 Ip。 

## <a name="required-configurations"></a>所需配置

在模板中搜索模板节, 以查看它们应该出现的位置。

### <a name="ipv6-addressspace-for-the-virtual-network"></a>虚拟网络的 IPv6 addressSpace

要添加的模板部分:

```JSON
        "addressSpace": {
          "addressPrefixes": [
            "[variables('vnetv4AddressRange')]",
            "[variables('vnetv6AddressRange')]"    
```

### <a name="ipv6-subnet-within-the-ipv6-virtual-network-addressspace"></a>IPv6 虚拟网络中的 IPv6 子网 addressSpace

要添加的模板部分:
```JSON
          {
            "name": "V6Subnet",
            "properties": {
              "addressPrefix": "[variables('subnetv6AddressRange')]"
            }

```

### <a name="ipv6-configuration-for-the-nic"></a>NIC 的 IPv6 配置

要添加的模板部分:
```JSON
          {
            "name": "ipconfig-v6",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
          "privateIPAddressVersion":"IPv6",
              "subnet": {
                "id": "[variables('v6-subnet-id')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(resourceId('Microsoft.Network/loadBalancers','loadBalancer'),'/backendAddressPools/LBBAP-v6')]"
                }
```

### <a name="ipv6-network-security-group-nsg-rules"></a>IPv6 网络安全组 (NSG) 规则

```JSON
          {
            "name": "default-allow-rdp",
            "properties": {
              "description": "Allow RDP",
              "protocol": "Tcp",
              "sourcePortRange": "33819-33829",
              "destinationPortRange": "5000-6000",
              "sourceAddressPrefix": "ace:cab:deca:deed::/64",
              "destinationAddressPrefix": "cab:ace:deca:deed::/64",
              "access": "Allow",
              "priority": 1003,
              "direction": "Inbound"
            }
```

## <a name="conditional-configuration"></a>条件配置

如果你使用的是网络虚拟设备, 请将 IPv6 路由添加到路由表中。 否则, 此配置是可选的。

```JSON
    {
      "type": "Microsoft.Network/routeTables",
      "name": "v6route",
      "apiVersion": "[variables('ApiVersion')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "routes": [
          {
            "name": "v6route",
            "properties": {
              "addressPrefix": "ace:cab:deca:deed::/64",
              "nextHopType": "VirtualAppliance",
              "nextHopIpAddress": "deca:cab:ace:f00d::1"
            }
```

## <a name="optional-configuration"></a>可选配置

### <a name="ipv6-internet-access-for-the-virtual-network"></a>虚拟网络的 IPv6 Internet 访问

```JSON
{
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-public-ip-addresses"></a>IPv6 公共 IP 地址

```JSON
    {
      "apiVersion": "[variables('ApiVersion')]",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "lbpublicip-v6",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard"
      },
      "properties": {
        "publicIPAllocationMethod": "Static",
        "publicIPAddressVersion": "IPv6"
      }
```

### <a name="ipv6-front-end-for-load-balancer"></a>负载均衡器的 IPv6 前端

```JSON
          {
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-back-end-address-pool-for-load-balancer"></a>负载均衡器的 IPv6 后端地址池

```JSON
              "backendAddressPool": {
                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', 'loadBalancer'), '/backendAddressPools/LBBAP-v6')]"
              },
              "protocol": "Tcp",
              "frontendPort": 8080,
              "backendPort": 8080
            },
            "name": "lbrule-v6"
```

### <a name="ipv6-load-balancer-rules-to-associate-incoming-and-outgoing-ports"></a>用于关联传入和传出端口的 IPv6 负载均衡器规则

```JSON
          {
            "name": "ipconfig-v6",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
          "privateIPAddressVersion":"IPv6",
              "subnet": {
                "id": "[variables('v6-subnet-id')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(resourceId('Microsoft.Network/loadBalancers','loadBalancer'),'/backendAddressPools/LBBAP-v6')]"
                }
```

## <a name="sample-vm-template-json"></a>示例 VM 模板 JSON
若要使用 Azure 资源管理器模板在 Azure 虚拟网络中部署 IPv6 双重堆栈应用程序, 请在[此处](https://azure.microsoft.com/resources/templates/ipv6-in-vnet-stdlb/)查看示例模板。

## <a name="next-steps"></a>后续步骤

你可以找到有关[公共 IP 地址](https://azure.microsoft.com/pricing/details/ip-addresses/)、[网络带宽](https://azure.microsoft.com/pricing/details/bandwidth/)或[负载均衡器](https://azure.microsoft.com/pricing/details/load-balancer/)定价的详细信息。
