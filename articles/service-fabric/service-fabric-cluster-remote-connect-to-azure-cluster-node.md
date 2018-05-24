---
title: 远程连接 Azure Service Fabric 群集节点 | Microsoft Docs
description: 了解如何远程连接到规模集实例（Service Fabric 群集节点）。
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: aljo
ms.openlocfilehash: 3c7b3626db0e38d28513d4665a83dd7155663034
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
---
# <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>远程连接到虚拟机规模集实例或群集节点
在 Azure 中运行的 Service Fabric 群集中，定义的每个群集节点类型都会[设置虚拟机单独规模](service-fabric-cluster-nodetypes.md)。  可以远程连接到特定规模集实例（或群集节点）。  不同于单实例 VM，规模集实例本身没有虚拟 IP 地址。 可能很难找到可用来远程连接到特定实例的 IP 地址和端口。

若要查找可用于远程连接到特定实例的 IP 地址和端口，请完成以下步骤。

1. 通过获取远程桌面协议 (RDP) 的入站 NAT 规则，查找节点类型的虚拟 IP 地址。

    首先，获取已定义为 `Microsoft.Network/loadBalancers` 资源定义的一部分的入站 NAT 规则值。
    
    在 Azure 门户中的负载均衡器页上，选择“设置” > “入站 NAT 规则”。 可获得可用来远程连接到第一个规模集实例的 IP 地址和端口。 
    
    ![负载均衡][LBBlade]
    
    在下图中，IP 地址和端口为 104.42.106.156 和 3389。
    
    ![NAT 规则][NATRules]

2. 查找可用来远程连接到特定规模集实例或节点的端口。

    将规模集实例映射到节点。 使用规模集信息确定要使用的确切端口。
    
    将按照与规模集实例相匹配的升序分配端口。 对于之前的 FrontEnd 节点类型示例中，下表列出了 5 个节点实例各自的端口。 向规模集实例应用相同的映射。
    
    | 虚拟机规模集实例 | **端口** |
    | --- | --- |
    | FrontEnd_0 |3389 |
    | FrontEnd_1 |3390 |
    | FrontEnd_2 |3391 |
    | FrontEnd_3 |3392 |
    | FrontEnd_4 |3393 |
    | FrontEnd_5 |3394 |

3. 远程连接到特定规模集实例。

    下图演示了如何使用远程桌面连接连接 FrontEnd_1 规模集实例：
    
    ![远程桌面连接][RDP]


有关后续步骤，请阅读以下文章：
* 请参阅[“随地部署”功能的概述及其与 Azure 托管群集的比较](service-fabric-deploy-anywhere.md)。
* 了解[群集安全性](service-fabric-cluster-security.md)。
* 部署后在群集 VM 上[更新 RDP 端口范围值](./scripts/service-fabric-powershell-change-rdp-port-range.md)
* 为群集 VM [更改管理员用户名和密码](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md)

<!--Image references-->
[LBBlade]: ./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/LBBlade.png
[NATRules]: ./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/NATRules.png
[RDP]: ./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/RDP.png
