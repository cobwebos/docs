---
title: "Azure 虚拟机规模集的网络 | Microsoft Docs"
description: "Azure 虚拟机规模集的配置网络属性。"
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/06/2017
ms.author: guybo
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: 1c9487be5415d05a8699f458259d872591280d3d
ms.contentlocale: zh-cn
ms.lasthandoff: 07/11/2017


---
# Azure 虚拟机规模集的网络
<a id="networking-for-azure-virtual-machine-scale-sets" class="xliff"></a>

通过门户部署 Azure 虚拟机规模集时，某些网络属性（例如带入站 NAT 规则的 Azure 负载均衡器）是默认设置的。 本文介绍如何使用部分较高级的可以对规模集配置的网络功能。

可以使用 Azure Resource Manager 模板配置本文介绍的所有功能。 此外，还为选定功能提供了 Azure CLI 示例。 请使用 2017 年 7 月或更晚的 CLI 版本。 很快会添加更多的 CLI 和 PowerShell 示例。

## 加速网络
<a id="accelerated-networking" class="xliff"></a>
Azure [加速网络](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-create-vm-accelerated-networking)可以实现对虚拟机的单根 I/O 虚拟化 (SR-IOV)，从而提升网络性能。 若要对规模集使用加速网络，请在规模集的 networkInterfaceConfigurations 设置中将 enableAcceleratedNetworking 设置为 true。 例如：
```json
"networkProfile": {
    "networkInterfaceConfigurations": [
    {
      "name": "niconfig1",
      "properties": {
        "primary": true,
        "enableAcceleratedNetworking" : true,
        "ipConfigurations": [
          ...
        ]
      }
    }
   ]
}
```

## 创建引用现有 Azure 负载均衡器的规模集
<a id="create-a-scale-set-that-references-an-existing-azure-load-balancer" class="xliff"></a>
使用 Azure 门户创建规模集后，就大多数配置选项来说，都会创建新的负载均衡器。 如果创建需引用现有负载均衡器的规模集，可以使用 CLI。 以下示例脚本先创建负载均衡器，然后创建规模集来引用该均衡器：
```bash
az network lb create -g lbtest -n mylb --vnet-name myvnet --subnet mysubnet --public-ip-address-allocation Static --backend-pool-name mybackendpool

az vmss create -g lbtest -n myvmss --image Canonical:UbuntuServer:16.04-LTS:latest --admin-username negat --ssh-key-value /home/myuser/.ssh/id_rsa.pub --upgrade-policy-mode Automatic --instance-count 3 --vnet-name myvnet --subnet mysubnet --lb mylb --backend-pool-name mybackendpool

```

## 可配置的 DNS 设置
<a id="configurable-dns-settings" class="xliff"></a>
默认情况下，规模集采用其创建时所在的 VNET 和子网的特定 DNS 设置。 但是，你可以直接配置规模集的 DNS 设置。

### 通过可配置的 DNS 服务器创建规模集
<a id="creating-a-scale-set-with-configurable-dns-servers" class="xliff"></a>
若要通过 CLI 2.0 使用自定义 DNS 配置创建规模集，请将 --dns-servers 参数添加到 vmss create 命令中，后跟空格分隔的服务器 IP 地址。 例如：
```bash
--dns-servers 10.0.0.6 10.0.0.5
```
若要在 Azure 模板中配置自定义 DNS 服务器，请将 dnsSettings 属性添加到规模集的 networkInterfaceConfigurations 节。 例如：
```json
"dnsSettings":{
    "dnsServers":["10.0.0.6", "10.0.0.5"]
}
```

### 使用可配置的虚拟机域名创建规模集
<a id="creating-a-scale-set-with-configurable-virtual-machine-domain-names" class="xliff"></a>
若要通过 CLI 2.0 使用自定义 DNS 名称为虚拟机创建规模集，请将 --vm-domain-name 参数添加到 vmss create 命令中，后跟表示域名的字符串。

若要在 Azure 模板中设置域名，请将 dnsSettings 属性添加到规模集的 networkInterfaceConfigurations 节。 例如：

```json
"networkProfile": {
  "networkInterfaceConfigurations": [
    {
    "name": "nic1",
    "properties": {
      "primary": "true",
      "ipConfigurations": [
      {
        "name": "ip1",
        "properties": {
          "subnet": {
            "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
          },
          "publicIPAddressconfiguration": {
            "name": "publicip",
            "properties": {
            "idleTimeoutInMinutes": 10,
              "dnsSettings": {
                "domainNameLabel": "[parameters('vmssDnsName')]"
              }
            }
          }
        }
      }
    ]
    }
}
```

单个虚拟机 DNS 名称的输出将采用以下形式： 
```
<vmname><vmindex>.<specifiedVmssDomainNameLabel>
```

## 公共 IP 和负载均衡器池的 IPv6 预览
<a id="ipv6-preview-for-public-ips-and-load-balancer-pools" class="xliff"></a>
可以在 Azure 负载均衡器上配置 IPv6 公共 IP 地址，并将连接路由到虚拟机规模集后端池。 若要使用 IPv6（目前为预览版），请先创建 IPv6 公共地址资源。 例如：
```json
{
    "apiVersion": "2016-03-30",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[parameters('ipv6PublicIPAddressName')]",
    "location": "[parameters('location')]",
    "properties": {
        "publicIPAddressVersion": "IPv6",
        "publicIPAllocationMethod": "Dynamic",
        "dnsSettings": {
            "domainNameLabel": "[parameters('dnsNameforIPv6LbIP')]"
        }
    }
}
```
接下来，根据需要为 IPv4 和 IPv6 配置负载均衡器前端 IP 配置：

```json
"frontendIPConfigurations": [
    {
        "name": "LoadBalancerFrontEndIPv6",
        "properties": {
            "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('ipv6PublicIPAddressName'))]"
            }
        }
    }
]
```
定义所需的后端池：
```json
"backendAddressPools": [
    {
        "name": "BackendPoolIPv4"
    },
    {
        "name": "BackendPoolIPv6"
    }
]
```
定义任何负载均衡器规则：
```json
{
    "name": "LBRuleIPv6-46000",
    "properties": {
        "frontendIPConfiguration": {
            "id": "[variables('ipv6FrontEndIPConfigID')]"
        },
        "backendAddressPool": {
            "id": "[variables('ipv6LbBackendPoolID')]"
        },
        "protocol": "tcp",
        "frontendPort": 46000,
        "backendPort": 60001,
        "probe": {
            "id": "[variables('ipv4ipv6lbProbeID')]"
        }
    }
}
```
最后，引用规模集网络属性的 IPConfigurations 节中的 IPv6 池：
```json
{
    "name": "ipv6IPConfig",
    "properties": {
        "privateIPAddressVersion": "IPv6",
        "loadBalancerBackendAddressPools": [
            {
                "id": "[variables('ipv6LbBackendPoolID')]"
            }
        ]
    }
}
```

## 每个虚拟机的公共 IPv4
<a id="public-ipv4-per-virtual-machine" class="xliff"></a>
通常，Azure 规模集虚拟机不需要自己的公共 IP 地址。 大多数情况下，将公共 IP 地址关联到负载均衡器或单个虚拟机（又称 jumpbox）更经济，也更安全，后者随后会根据需要通过特定方式（例如，通过入站 NAT 规则）将传入连接路由到规模集虚拟机。

但某些情况下，确实需要规模集虚拟机拥有自己的公共 IP 地址。 例如，玩游戏时，主机需直接连接到云虚拟机进行游戏的物理处理。 再举例来说，虚拟机有时需在分布式数据库中跨区域进行外部互连。

### 使用公共 IP 为每个虚拟机创建规模集
<a id="creating-a-scale-set-with-public-ip-per-virtual-machine" class="xliff"></a>
若要通过 CLI 2.0 创建向每个虚拟机分配公共 IP 地址的规模集，请将 --public-ip-per-vm 参数添加到 vmss create 命令中。 

若要使用 Azure 模板创建规模集，请确保 Microsoft.Compute/virtualMachineScaleSets 资源的 API 版本至少为 2017-03-30，并将 publicIpAddressConfiguration JSON 属性添加到规模集的 ipConfigurations 节。 例如：

```json
"publicIpAddressConfiguration": {
    "name": "pub1",
    "properties": {
      "idleTimeoutInMinutes": 15
    }
}
```
示例模板：[201-vmss-public-ip-linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-public-ip-linux)

### 在规模集中查询虚拟机的公共 IP 地址
<a id="querying-the-public-ip-addresses-of-the-virtual-machines-in-a-scale-set" class="xliff"></a>
若要通过 CLI 2.0 列出分配到规模集虚拟机的公共 IP 地址，请使用 az vmss list-instance-public-ips 命令。

也可使用 [Azure 资源浏览器](https://resources.azure.com)或者 Azure REST API 2017-03-30 或更高版本查询分配到规模集虚拟机的公共 IP 地址。

若要使用资源浏览器查看规模集的公共 IP 地址，请找到规模集下的 publicipaddresses 节。 例如：https://resources.azure.com/subscriptions/_your_sub_id_/resourceGroups/_your_rg_/providers/Microsoft.Compute/virtualMachineScaleSets/_your_vmss_/publicipaddresses

```
GET https://management.azure.com/subscriptions/{your sub ID}/resourceGroups/{RG name}/providers/Microsoft.Compute/virtualMachineScaleSets/{scale set name}/publicipaddresses?api-version=2017-03-30
```

示例输出：
```json
{
  "value": [
    {
      "name": "pub1",
      "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/pipvmss/virtualMachines/0/networkInterfaces/pipvmssnic/ipConfigurations/yourvmssipconfig/publicIPAddresses/pub1",
      "etag": "W/\"a64060d5-4dea-4379-a11d-b23cd49a3c8d\"",
      "properties": {
        "provisioningState": "Succeeded",
        "resourceGuid": "ee8cb20f-af8e-4cd6-892f-441ae2bf701f",
        "ipAddress": "13.84.190.11",
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Dynamic",
        "idleTimeoutInMinutes": 15,
        "ipConfiguration": {
          "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/0/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig"
        }
      }
    },
    {
      "name": "pub1",
      "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/3/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig/publicIPAddresses/pub1",
      "etag": "W/\"5f6ff30c-a24c-4818-883c-61ebd5f9eee8\"",
      "properties": {
        "provisioningState": "Succeeded",
        "resourceGuid": "036ce266-403f-41bd-8578-d446d7397c2f",
        "ipAddress": "13.84.159.176",
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Dynamic",
        "idleTimeoutInMinutes": 15,
        "ipConfiguration": {
          "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/3/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig"
        }
      }
    }
```

## 每个 NIC 多个 IP 地址
<a id="multiple-ip-addresses-per-nic" class="xliff"></a>
在规模集中，附加到 VM 的每个 NIC 可以有一个或多个关联的 IP 配置。 每个配置分配有一个专用 IP 地址。 每个配置还可以有一个关联的公共 IP 地址资源。 若要了解可以为一个 NIC 分配多少个 IP 地址，以及可以在一个 Azure 订阅中使用多少个公共 IP 地址，请参阅 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

## 每个虚拟机多个 NIC
<a id="multiple-nics-per-virtual-machine" class="xliff"></a>
每个虚拟机最多可以有 8 个 NIC，具体取决于虚拟机大小。 若要了解每个虚拟机的最大 NIC 数，请参阅 [VM 大小](../virtual-machines/windows/sizes.md)一文。 以下示例为规模集网络配置文件，显示每个虚拟机有多个 NIC 条目和多个公共 IP：
```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
        "name": "nic1",
        "properties": {
            "primary": "true",
            "ipConfigurations": [
            {
                "name": "ip1",
                "properties": {
                "subnet": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                },
                "publicipaddressconfiguration": {
                    "name": "pub1",
                    "properties": {
                    "idleTimeoutInMinutes": 15
                    }
                },
                "loadBalancerInboundNatPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                    }
                ],
                "loadBalancerBackendAddressPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                    }
                ]
                }
            }
            ]
        }
        },
        {
        "name": "nic2",
        "properties": {
            "primary": "false",
            "ipConfigurations": [
            {
                "name": "ip1",
                "properties": {
                "subnet": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                },
                "publicipaddressconfiguration": {
                    "name": "pub1",
                    "properties": {
                    "idleTimeoutInMinutes": 15
                    }
                },
                "loadBalancerInboundNatPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                    }
                ],
                "loadBalancerBackendAddressPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                    }
                ]
                }
            }
            ]
        }
        }
    ]
}
```

## 每个规模集的 NSG
<a id="nsg-per-scale-set" class="xliff"></a>
可以直接向规模集应用网络安全组，只需将引用添加到规模集虚拟机属性的网络接口配置节即可。

例如： 
```
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "nic1",
            "properties": {
                "primary": "true",
                "ipConfigurations": [
                    {
                        "name": "ip1",
                        "properties": {
                            "subnet": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                            }
                "loadBalancerInboundNatPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                                }
                            ],
                            "loadBalancerBackendAddressPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                                }
                            ]
                        }
                    }
                ],
                "networkSecurityGroup": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('nsgName'))]"
                }
            }
        }
    ]
}
```

## 后续步骤
<a id="next-steps" class="xliff"></a>
有关 Azure 虚拟网络的详细信息，请参阅[此文档](../virtual-network/virtual-networks-overview.md)。

