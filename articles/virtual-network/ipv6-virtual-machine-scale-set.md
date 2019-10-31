---
title: 在 Azure 中部署具有 IPv6 的虚拟机规模集
titlesuffix: Azure Virtual Network
description: 本文介绍如何在 Azure 虚拟网络中部署具有 IPv6 的虚拟机规模集。
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: kumud
ms.openlocfilehash: b90910614bcd86a54198b1a0961a3378427ea87e
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73164986"
---
# <a name="deploy-virtual-machine-scale-sets-with-ipv6-in-azure-preview"></a>在 Azure 中部署具有 IPv6 的虚拟机规模集（预览）

本文介绍如何在 Azure 虚拟网络中部署具有双堆栈外部负载均衡器的双堆栈（IPv4 + IPv6）虚拟机规模集。 创建支持 IPv6 的虚拟机规模集的过程与创建[在此处](ipv6-configure-standard-load-balancer-template-json.md)所述的单个 vm 的过程几乎相同。 首先，您将会看到类似于单个 Vm 所述的步骤：
1.  创建 IPv4 和 IPv6 公共 Ip。
2.  创建双堆栈负载均衡器。  
3.  创建网络安全组（NSG）规则。  

唯一不同于单个 Vm 的步骤是创建使用虚拟机规模集资源的网络接口（NIC）配置： networkProfile/networkInterfaceConfigurations。 JSON 结构与用于单个 Vm 的 networkInterfaces/对象类似，其中添加了 NIC，并将 IPv4 IpConfiguration 设置为使用 **"primary"： true**特性的主接口，如下所示：下面的示例：

```json
          "networkProfile": {
            "networkInterfaceConfigurations": [
              {
                "name": "[variables('nicName')]",
                "properties": {
                  "primary": true,
          "networkSecurityGroup": {
            "id": "[resourceId('Microsoft.Network/networkSecurityGroups','VmssNsg')]"
          },                  
                  "ipConfigurations": [
                    {
                      "name": "[variables('ipConfigName')]",
                      "properties": {
                        "primary": true,
                        "subnet": {
                          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', 'MyvirtualNetwork','Mysubnet')]"
                        },
                        "privateIPAddressVersion":"IPv4",                       
                        "publicipaddressconfiguration": {
                          "name": "pub1",
                          "properties": {
                            "idleTimeoutInMinutes": 15
                          }
                        },
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', 'loadBalancer', 'bePool'))]"
                          }
                        ],
                        "loadBalancerInboundNatPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/inboundNatPools', 'loadBalancer', 'natPool')]"
                          }
                        ]
                      }
                    },
                    {
                      "name": "[variables('ipConfigNameV6')]",
                      "properties": {
                        "subnet": {
                          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets','MyvirtualNetwork','Mysubnet')]"
                        },
                        "privateIPAddressVersion":"IPv6",
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', 'loadBalancer','bePoolv6')]"
                          }
                        ],                        
                      }
                    }
                  ]
                }
              }
            ]
          }

```


## <a name="sample-virtual-machine-scale-set-template-json"></a>示例虚拟机规模集模板 JSON

若要使用双堆栈外部负载均衡器和虚拟网络视图[示例模板部署](https://azure.microsoft.com/resources/templates/ipv6-in-vnet-vmss/)双堆栈（IPv4 + IPv6）虚拟机规模集。
## <a name="next-steps"></a>后续步骤

若要了解有关 Azure 虚拟网络中 IPv6 支持的详细信息，请参阅[什么是适用于 Azure 虚拟网络的 ipv6？](ipv6-overview.md)。
