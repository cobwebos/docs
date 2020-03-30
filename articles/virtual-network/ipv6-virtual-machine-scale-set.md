---
title: 在 Azure 中部署使用 IPv6 的虚拟机规模集
titlesuffix: Azure Virtual Network
description: 本文介绍如何在 Azure 虚拟网络中部署使用 IPv6 的虚拟机规模集。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73164986"
---
# <a name="deploy-virtual-machine-scale-sets-with-ipv6-in-azure-preview"></a>在 Azure 中部署使用 IPv6 的虚拟机缩放集（预览）

本文介绍如何在 Azure 虚拟网络中部署使用双堆栈外部负载均衡器的双堆栈 (IPv4 + IPv6) 虚拟机规模集。 创建支持 IPv6 的虚拟机规模集的过程，与[此处](ipv6-configure-standard-load-balancer-template-json.md)所述的创建单个 VM 的过程基本相同。 您将从类似于各个 VM 描述的步骤开始：
1.  创建 IPv4 和 IPv6 公共 IP。
2.  创建双堆栈负载均衡器。  
3.  创建网络安全组 (NSG) 规则。  

只有一个步骤与创建单个 VM 不同，那就是创建使用虚拟机规模集资源的网络接口 (NIC) 配置：networkProfile/networkInterfaceConfigurations。 JSON 结构类似于用于单个 VM 的网络/网络接口对象，增加了使用 **"主"** 将 NIC 和 IPv4 Ip配置设置为主接口：真实属性，如下例所示：

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

若要部署使用双堆栈外部负载均衡器和虚拟网络的双堆栈 (IPv4 + IPv6) 虚拟机规模集，请查看[此处](https://azure.microsoft.com/resources/templates/ipv6-in-vnet-vmss/)的示例模板。
## <a name="next-steps"></a>后续步骤

要了解有关 Azure 虚拟网络中 IPv6 支持的更多信息，请参阅[什么是 Azure 虚拟网络的 IPv6？](ipv6-overview.md)
