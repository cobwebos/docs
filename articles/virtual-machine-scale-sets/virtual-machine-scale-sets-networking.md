---
title: Azure 虚拟机规模集的网络
description: 如何为 Azure 虚拟机规模集配置一些更高级的网络属性。
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: networking
ms.date: 07/17/2017
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 46a12006274ca8516c936e37189c9233dde9b410
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83125190"
---
# <a name="networking-for-azure-virtual-machine-scale-sets"></a>Azure 虚拟机规模集的网络

通过门户部署 Azure 虚拟机规模集时，某些网络属性（例如带入站 NAT 规则的 Azure 负载均衡器）是默认设置的。 本文介绍如何使用部分较高级的可以对规模集配置的网络功能。

可以使用 Azure Resource Manager 模板配置本文介绍的所有功能。 此外，还为选定功能提供了 Azure CLI 和 PowerShell 示例。

## <a name="accelerated-networking"></a>加速网络
Azure 加速网络可以实现对虚拟机的单根 I/O 虚拟化 (SR-IOV)，从而提升网络性能。 若要详细了解如何使用加速网络，请查看适用于 [Windows](../virtual-network/create-vm-accelerated-networking-powershell.md) 或 [Linux](../virtual-network/create-vm-accelerated-networking-cli.md) 虚拟机的加速网络。 若要对规模集使用加速网络，请在规模集的 networkInterfaceConfigurations 设置中将 enableAcceleratedNetworking 设置为  true。 例如：

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

## <a name="azure-virtual-machine-scale-sets-with-azure-load-balancer"></a>带 Azure 负载均衡器的 Azure 虚拟机规模集

使用虚拟机规模集和负载均衡器时，应考虑以下事项：

* **多个虚拟机规模集不能使用同一负载均衡器**。
* **端口转发和入站 NAT 规则**：
  * 每个虚拟机规模集必须有一个入站 NAT 规则。
  * 创建规模集后，无法为负载均衡器的运行状况探测所用的负载均衡规则修改后端端口。 为了更改端口，可以通过更新 Azure 虚拟机规模集来删除运行状况探测，更新端口，然后重新配置运行状况探测。
  * 在负载均衡器的后端池中使用虚拟机规模集时，会自动创建默认的入站 NAT 规则。
* **负载均衡规则**：
  * 在负载均衡器的后端池中使用虚拟机规模集时，会自动创建默认的负载均衡规则。
* **出站规则**：
  *  若要为已被负载均衡规则引用的后端池创建出站规则，需要先在创建入站负载均衡规则时在门户中将“创建隐式出站规则”标记为“否”。  

  :::image type="content" source="./media/vmsslb.png" alt-text="创建负载均衡规则" border="true":::

可以使用以下方法部署一个包含现有 Azure 负载均衡器的虚拟机规模集。

* [使用 Azure 门户配置包含现有 Azure 负载均衡器的虚拟机规模集](https://docs.microsoft.com/azure/load-balancer/configure-vm-scale-set-portal)。
* [使用 Azure PowerShell 配置包含现有 Azure 负载均衡器的虚拟机规模集](https://docs.microsoft.com/azure/load-balancer/configure-vm-scale-set-powershell)。
* [使用 Azure CLI 配置包含现有 Azure 负载均衡器的虚拟机规模集](https://docs.microsoft.com/azure/load-balancer/configure-vm-scale-set-cli)。

## <a name="create-a-scale-set-that-references-an-application-gateway"></a>创建引用应用程序网关的规模集
若要创建使用应用程序网关的规模集，请在规模集的 ipConfigurations 节中引用应用程序网关的后端地址池，如此 ARM 模板配置所示：

```json
"ipConfigurations": [{
  "name": "{config-name}",
  "properties": {
  "subnet": {
    "id": "{subnet-id}"
  },
  "ApplicationGatewayBackendAddressPools": [{
    "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/applicationGateways/{gateway-name}/backendAddressPools/{pool-name}"
  }]
}]
```

>[!NOTE]
> 请注意，应用程序网关必须与规模集在同一虚拟网络中，但必须与规模集在不同子网中。


## <a name="configurable-dns-settings"></a>可配置的 DNS 设置
默认情况下，规模集采用其创建时所在的 VNET 和子网的特定 DNS 设置。 但是，可以直接配置规模集的 DNS 设置。

### <a name="creating-a-scale-set-with-configurable-dns-servers"></a>通过可配置的 DNS 服务器创建规模集
若要通过 Azure CLI 使用自定义 DNS 配置创建规模集，请将 --dns-servers 参数添加到 vmss create 命令中，后接空格分隔的服务器 IP 地址   。 例如：

```bash
--dns-servers 10.0.0.6 10.0.0.5
```

若要在 Azure 模板中配置自定义 DNS 服务器，请将 dnsSettings 属性添加到规模集的 networkInterfaceConfigurations 节。 例如：

```json
"dnsSettings":{
    "dnsServers":["10.0.0.6", "10.0.0.5"]
}
```

### <a name="creating-a-scale-set-with-configurable-virtual-machine-domain-names"></a>使用可配置的虚拟机域名创建规模集
若要通过 CLI 使用自定义 DNS 名称为虚拟机创建规模集，请将 --vm-domain-name 参数添加到 **virtual machine scale set create** 命令中，后接表示域名的字符串  。

若要在 Azure 模板中设置域名，请将 **dnsSettings** 属性添加到规模集的 **networkInterfaceConfigurations** 节。 例如：

```json
"networkProfile": {
  "networkInterfaceConfigurations": [
    {
    "name": "nic1",
    "properties": {
      "primary": true,
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

```output
<vm><vmindex>.<specifiedVmssDomainNameLabel>
```

## <a name="public-ipv4-per-virtual-machine"></a>每个虚拟机的公共 IPv4
通常，Azure 规模集虚拟机不需要自己的公共 IP 地址。 大多数情况下，将公共 IP 地址关联到负载均衡器或单个虚拟机（又称 jumpbox）更经济，也更安全，后者随后会根据需要通过特定方式（例如，通过入站 NAT 规则）将传入连接路由到规模集虚拟机。

但某些情况下，确实需要规模集虚拟机拥有自己的公共 IP 地址。 例如，玩游戏时，主机需直接连接到云虚拟机进行游戏的物理处理。 再举例来说，虚拟机有时需在分布式数据库中跨区域进行外部互连。

### <a name="creating-a-scale-set-with-public-ip-per-virtual-machine"></a>使用公共 IP 为每个虚拟机创建规模集
若要通过 CLI 创建向每个虚拟机分配公共 IP 地址的规模集，请将 --public-ip-per-vm 参数添加到 vmss create 命令中   。 

若要使用 Azure 模板创建规模集，请确保 Microsoft.Compute/virtualMachineScaleSets 资源的 API 版本至少为 **2017-03-30**，并将 **publicIpAddressConfiguration** JSON 属性添加到规模集的 ipConfigurations 节。 例如：

```json
"publicIpAddressConfiguration": {
    "name": "pub1",
    "properties": {
      "idleTimeoutInMinutes": 15
    }
}
```

示例模板：[201-vmss-public-ip-linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-public-ip-linux)

### <a name="querying-the-public-ip-addresses-of-the-virtual-machines-in-a-scale-set"></a>在规模集中查询虚拟机的公共 IP 地址
若要通过 CLI 列出分配到规模集虚拟机的公共 IP 地址，请使用 az vmss list-instance-public-ips 命令  。

若要使用 PowerShell 列出规模集的公共 IP 地址，请使用_Get-AzPublicIpAddress_ 命令。 例如：

```powershell
Get-AzPublicIpAddress -ResourceGroupName myrg -VirtualMachineScaleSetName myvmss
```

也可以通过直接引用公共 IP 地址配置的资源 ID 来查询公共 IP 地址。 例如：

```powershell
Get-AzPublicIpAddress -ResourceGroupName myrg -Name myvmsspip
```

也可通过查询 [Azure 资源浏览器](https://resources.azure.com)或者 Azure REST API **** 2017-03-30 或更高版本来显示分配到规模集虚拟机的公共 IP 地址。

若要查询 [Azure 资源浏览器](https://resources.azure.com)，请执行以下操作：

1. 在 Web 浏览器中打开 [Azure 资源浏览器](https://resources.azure.com)。
1. 展开左侧的“订阅”，方法是单击其旁边的 *+*。** 如果在“订阅”下只有一个项，则“订阅”可能已展开。**
1. 展开订阅。
1. 展开资源组。
1. 展开提供程序。**
1. 展开 *Microsoft.Compute*。
1. 展开 *virtualMachineScaleSets*。
1. 展开规模集。
1. 单击“publicipaddresses”。**

若要查询 Azure REST API，请执行以下操作：

```bash
GET https://management.azure.com/subscriptions/{your sub ID}/resourceGroups/{RG name}/providers/Microsoft.Compute/virtualMachineScaleSets/{scale set name}/publicipaddresses?api-version=2017-03-30
```

[Azure 资源浏览器](https://resources.azure.com)和 Azure REST API 的示例输出：

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

## <a name="multiple-ip-addresses-per-nic"></a>每个 NIC 多个 IP 地址
在规模集中，附加到 VM 的每个 NIC 可以有一个或多个关联的 IP 配置。 每个配置分配有一个专用 IP 地址。 每个配置还可以有一个关联的公共 IP 地址资源。 若要了解可以为一个 NIC 分配多少个 IP 地址，以及可以在一个 Azure 订阅中使用多少个公共 IP 地址，请参阅 [Azure 限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

## <a name="multiple-nics-per-virtual-machine"></a>每个虚拟机多个 NIC
每个虚拟机最多可以有 8 个 NIC，具体取决于虚拟机大小。 若要了解每个虚拟机的最大 NIC 数，请参阅 [VM 大小](../virtual-machines/windows/sizes.md)一文。 连接到 VM 实例的所有 NIC 都必须连接到同一虚拟网络。 NIC 可以连接到不同子网，但所有子网都必须属于同一虚拟网络。

以下示例为规模集网络配置文件，显示每个虚拟机有多个 NIC 条目和多个公共 IP：

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
        "name": "nic1",
        "properties": {
            "primary": true,
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
            "primary": false,
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

## <a name="nsg--asgs-per-scale-set"></a>每个规模集的 NSG 和 ASG
可以使用[网络安全组](../virtual-network/security-overview.md)通过安全规则来筛选 Azure 虚拟网络中出入 Azure 资源的流量。 可以通过[应用程序安全组](../virtual-network/security-overview.md#application-security-groups)来处理 Azure 资源的网络安全问题，并将其作为应用程序结构的扩展组合起来。

可以直接向规模集应用网络安全组，只需将引用添加到规模集虚拟机属性的网络接口配置节即可。

也可以直接为规模集指定应用程序安全组，只需将引用添加到规模集虚拟机属性的网络接口 IP 配置节即可。

例如：

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "nic1",
            "properties": {
                "primary": true,
                "ipConfigurations": [
                    {
                        "name": "ip1",
                        "properties": {
                            "subnet": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                            },
                            "applicationSecurityGroups": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/applicationSecurityGroups/', variables('asgName'))]"
                                }
                            ],
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

若要验证网络安全组是否与规模集相关联，请使用 `az vmss show` 命令。 下面的示例使用 `--query` 来筛选结果，只显示输出的相关部分。

```azurecli
az vmss show \
    -g myResourceGroup \
    -n myScaleSet \
    --query virtualMachineProfile.networkProfile.networkInterfaceConfigurations[].networkSecurityGroup

[
  {
    "id": "/subscriptions/.../resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/nsgName",
    "resourceGroup": "myResourceGroup"
  }
]
```

若要验证应用程序安全组是否与规模集相关联，请使用 `az vmss show` 命令。 下面的示例使用 `--query` 来筛选结果，只显示输出的相关部分。

```azurecli
az vmss show \
    -g myResourceGroup \
    -n myScaleSet \
    --query virtualMachineProfile.networkProfile.networkInterfaceConfigurations[].ipConfigurations[].applicationSecurityGroups

[
  [
    {
      "id": "/subscriptions/.../resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationSecurityGroups/asgName",
      "resourceGroup": "myResourceGroup"
    }
  ]
]
```



## <a name="next-steps"></a>后续步骤
有关 Azure 虚拟网络的详细信息，请参阅 [Azure 虚拟网络概述](../virtual-network/virtual-networks-overview.md)。
