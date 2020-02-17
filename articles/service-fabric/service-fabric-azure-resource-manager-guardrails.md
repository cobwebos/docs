---
title: Service Fabric Azure 资源管理器 deployment guardrails
description: 本文概述通过 Azure 部署 Service Fabric 群集时的常见错误资源管理器以及如何避免这些错误。
services: service-fabric
documentationcenter: .net
author: peterpogorski
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: pepogors
ms.openlocfilehash: a61b0cf30ca46eb77837eb09d6a9a0b6f30e89a9
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368580"
---
# <a name="service-fabric-guardrails"></a>Service Fabric guardrails 
部署 Service Fabric 群集时，guardrails 已准备就绪，这将导致 Azure 资源管理器部署在无效群集配置的情况下失败。 以下部分概述了常见群集配置问题以及缓解这些问题所需的步骤。 

## <a name="durability-mismatch"></a>持续性不匹配
### <a name="overview"></a>概述
Service Fabric 节点类型的持续性值在 Azure 资源管理器模板的两个不同部分中定义。 虚拟机规模集资源的虚拟机规模集扩展部分和 Service Fabric 群集资源的 "节点类型" 部分。 这是因为这些部分中的持续性值匹配，否则资源部署将失败。

以下部分包含虚拟机规模集扩展持续性设置与 Service Fabric 节点类型持久性设置之间持续性不匹配的示例：  

**虚拟机规模集持续性设置**
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
* 虚拟机规模集持续性不匹配与当前 Service Fabric 节点类型持久性级别不匹配
* 虚拟机规模集持续性与目标 Service Fabric 节点类型持久性级别不匹配
* 虚拟机规模集持续性与当前 Service Fabric 持久性级别或目标 Service Fabric 节点类型持久性级别匹配 

### <a name="mitigation"></a>缓解操作
若要解决持久性不匹配问题，请按以上任何错误消息中所示：
1. 请在 Azure 资源管理器模板的 "虚拟机规模集扩展" 或 "Service Fabric 节点类型" 部分中更新持久性级别，以确保值匹配。
2. 用更新的值重新部署 Azure 资源管理器模板。


## <a name="seed-node-deletion"></a>删除种子节点 
### <a name="overview"></a>概述
Service Fabric 群集具有 "[可靠性层](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster)" 属性，该属性用于确定在群集的主节点类型上运行的系统服务副本数。 所需的副本数将确定必须在群集的主节点类型中维护的最小节点数。 如果主节点类型中的节点数低于可靠性层所需的最小值，则群集将变得不稳定。  

### <a name="error-messages"></a>错误消息 
已检测到种子节点删除操作，该操作将被拒绝。 
* 此操作只会导致 {0} 可能的种子节点保留在群集中，而 {1} 需要至少。
* 从 {1} 中删除 {0} 种子节点会导致群集因丢失种子节点仲裁而停止运行。 {2}可以删除的种子节点的最大数量。
 
### <a name="mitigation"></a>缓解操作 
确保主节点类型具有足够的虚拟机，以便在群集上指定可靠性。 如果虚拟机将虚拟机规模集的最小数量降到给定的可靠性层，将无法删除虚拟机。
* 如果正确指定了可靠性层，请确保主节点类型中有足够的节点作为可靠性层的需要。 
* 如果可靠性层不正确，请在启动任何虚拟机规模集操作之前启动对 Service Fabric 资源的更改，以便先降低可靠性级别，并等待其完成。
* 如果可靠性层为铜牌，请按照以下[步骤](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#manually-remove-vms-from-a-node-typevirtual-machine-scale-set)操作，将群集适度缩小。

## <a name="next-steps"></a>后续步骤
* 在运行 Windows Server 的 VM 或计算机上创建群集：[创建适用于 Windows Server 的 Service Fabric 群集](service-fabric-cluster-creation-for-windows-server.md)
* 在运行 Linux 的 VM 或计算机上创建群集：[创建 Linux 群集](service-fabric-cluster-creation-via-portal.md)
* 疑难解答 Service Fabric：[疑难解答指南](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)
