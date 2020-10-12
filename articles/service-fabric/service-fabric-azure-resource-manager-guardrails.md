---
title: Service Fabric Azure 资源管理器部署准则
description: 本文概述通过 Azure 资源管理器部署 Service Fabric 群集时常犯的错误以及如何避免它们。
services: service-fabric
documentationcenter: .net
author: peterpogorski
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: pepogors
ms.openlocfilehash: aa1499c57ead28bfcee90a2f224ef9c3bb1d7f58
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86247818"
---
# <a name="service-fabric-guardrails"></a>Service Fabric 准则 
部署 Service Fabric 群集时，需遵循相应准则，使用有效的群集配置通过 Azure 资源管理器进行部署，否则就会失败。 以下部分概述了常见的群集配置问题，以及解决这些问题所需的步骤。 

## <a name="durability-mismatch"></a>持久性不匹配
### <a name="overview"></a>概述
Service Fabric 节点类型的持久性值在 Azure 资源管理器模板的两个不同部分定义。 这两个部分是虚拟机规模集资源的虚拟机规模集扩展部分，以及 Service Fabric 群集资源的节点类型部分。 这两个部分中的持久性值必须匹配，否则部署会失败。

以下部分包含一个示例，该示例展示了虚拟机规模集扩展持久性设置和 Service Fabric 节点类型持久性设置之间存在的持久性不匹配情况：  

**虚拟机规模集持久性设置**
```json 
{
  "extensions": [
    {
      "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
      "properties": {
        "type": "ServiceFabricNode",
        "publisher": "Microsoft.Azure.ServiceFabric",
        "settings": {
          "durabilityLevel": "Silver",
        }
      }
    }
  ]
}
```

**Service Fabric 节点类型持久性设置** 
```json
{
  "nodeTypes": [
    {
      "name": "[variables('vmNodeType0Name')]",
      "durabilityLevel": "Bronze",
      "isPrimary": true,
      "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    }
  ]
}
```

### <a name="error-messages"></a>错误消息
* 虚拟机规模集持久性与当前的 Service Fabric 节点类型持久性级别不匹配
* 虚拟机规模集持久性与目标 Service Fabric 节点类型持久性级别不匹配
* 虚拟机规模集持久性与当前的 Service Fabric 持久性级别或目标 Service Fabric 节点类型持久性级别不匹配 

### <a name="mitigation"></a>缓解措施
若要修复上述任何错误消息所指示的持久性不匹配问题，请执行以下操作：
1. 更新 Azure 资源管理器模板的虚拟机规模集扩展或 Service Fabric 节点类型部分中的持久性级别，确保这些值匹配。
2. 使用更新的值重新部署 Azure 资源管理器模板。


## <a name="seed-node-deletion"></a>删除种子节点 
### <a name="overview"></a>概述
Service Fabric 群集提供一个[可靠性层](./service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster)属性，用于确定主节点类型的群集上运行的系统服务副本数。 所需副本数将确定必须在主节点类型的群集中维护的最小节点数。 如果主节点类型中的节点数小于可靠性层所需的最小数目，则群集将变得不稳定。  

### <a name="error-messages"></a>错误消息 
已检测到种子节点删除操作，将拒绝该操作。 
* 此操作会导致只有 {0} 个潜在种子节点保留在群集中，而所需的最小数目是 {1}。
* 从 {1} 中删除 {0} 种子节点会导致群集因种子节点仲裁丢失而关闭。 每次最多可以删除的种子节点数为 {2} 个。
 
### <a name="mitigation"></a>缓解措施 
确保主节点类型具有足够的虚拟机，以实现群集中指定的可靠性。 如果删除某个虚拟机会导致虚拟机规模集的节点数小于给定可靠性层的最小节点数，将无法执行此删除操作。
* 如果正确指定了可靠性层，请确保在主节点类型中提供可靠性层所需的足够节点。 
* 如果可靠性层不正确，请在 Service Fabric 资源中发起更改以便先降低可靠性级别，然后启动任何虚拟机规模集操作并等待操作完成。
* 如果可靠性层为“铜级”，请按照[这些步骤](./service-fabric-cluster-scale-in-out.md#manually-remove-vms-from-a-node-typevirtual-machine-scale-set)适度横向缩减群集。

## <a name="next-steps"></a>后续步骤
* 在运行 Windows Server 的 VM 或计算机上创建群集：[创建适用于 Windows Server 的 Service Fabric 群集](service-fabric-cluster-creation-for-windows-server.md)
* 在运行 Linux 的 VM 或计算机上创建群集：[创建 Linux 群集](service-fabric-cluster-creation-via-portal.md)
* Service Fabric 故障排除：[故障排除指南](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)
