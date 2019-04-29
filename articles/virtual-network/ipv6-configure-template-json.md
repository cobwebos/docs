---
title: 部署 Azure 虚拟网络-资源管理器模板 （预览版） 中的 IPv6 双堆栈应用程序
titlesuffix: Azure Virtual Network
description: 本文说明如何部署使用 Azure 资源管理器虚拟机模板的 Azure 虚拟网络中的 IPv6 双堆栈应用程序。
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: ae90bc4a12763803f38224d917c4644a68ae7d6b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130846"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure---template-preview"></a>部署 Azure-模板 （预览版） 中的 IPv6 双堆栈应用程序

本文提供了一系列适用于 Azure 资源管理器 VM 模板的部分使用的 IPv6 配置任务。 使用本文中所述的模板来部署双堆栈 （IPv4 + IPv6） 应用程序中包括一个双堆栈虚拟网络使用 IPv4 和 IPv6 子网，具有双 （IPv4 + IPv6） 前端配置，具有将一个双 IP 的 Nic 的 Vm 的负载均衡器的 Azure配置、 网络安全组和公共 Ip。 

## <a name="required-configurations"></a>所需的配置

搜索模板以查看它们应发生的位置中的模板部分。

### <a name="ipv6-addressspace-for-the-virtual-network"></a>虚拟网络的 IPv6 addressSpace

若要添加的模板部分：

```JSON
        "addressSpace": {
          "addressPrefixes": [
            "[variables('vnetv4AddressRange')]",
            "[variables('vnetv6AddressRange')]"    
```

### <a name="ipv6-subnet-within-the-ipv6-virtual-network-addressspace"></a>IPv6 虚拟网络 addressSpace 中的 IPv6 子网

若要添加的模板部分：
```JSON
          {
            "name": "V6Subnet",
            "properties": {
              "addressPrefix": "[variables('subnetv6AddressRange')]"
            }

```

### <a name="ipv6-configuration-for-the-nic"></a>NIC 的 IPv6 配置

若要添加的模板部分：
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

如果使用网络虚拟设备，添加 IPv6 路由的路由表中。 否则，此配置是可选的。

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

### <a name="ipv6-internet-access-for-the-virtual-network"></a>IPv6 Internet 访问的虚拟网络

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
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
        "publicIPAddressVersion": "IPv6"
      }
```

### <a name="ipv6-front-end-for-load-balancer"></a>IPv6 前端负载均衡器

```JSON
          {
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-back-end-address-pool-for-load-balancer"></a>IPv6 的负载均衡器的后端地址池

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

### <a name="ipv6-load-balancer-rules-to-associate-incoming-and-outgoing-ports"></a>IPv6 负载均衡器规则，用于将传入和传出端口相关联

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
单击[此处](https://azure.microsoft.com/resources/templates/ipv6-in-vnet/)部署中使用 Azure 资源管理器模板的 Azure 虚拟网络的 IPv6 双堆栈应用程序。

## <a name="next-steps"></a>后续步骤

您可以找到有关定价的详细信息[公共 IP 地址](https://azure.microsoft.com/pricing/details/ip-addresses/)，[网络带宽](https://azure.microsoft.com/pricing/details/bandwidth/)，或[负载均衡器](https://azure.microsoft.com/pricing/details/load-balancer/)。
