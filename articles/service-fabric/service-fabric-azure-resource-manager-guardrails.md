---
title: Service Fabric Azure 资源管理器部署准则
description: 本文概述通过 Azure 资源管理器部署 Service Fabric 群集时常犯的错误以及如何避免它们。
services: service-fabric
documentationcenter: .net
author: peterpogorski
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: pepogors
ms.openlocfilehash: a61b0cf30ca46eb77837eb09d6a9a0b6f30e89a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368580"
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

### <a name="mitigation"></a>缓解操作
若要修复上述任何错误消息所指示的持久性不匹配问题，请执行以下操作：
1. 更新 Azure 资源管理器模板的虚拟机规模集扩展或 Service Fabric 节点类型部分中的持久性级别，确保这些值匹配。
2. 使用更新的值重新部署 Azure 资源管理器模板。


## <a name="seed-node-deletion"></a>种子节点删除 
### <a name="overview"></a>概述
Service Fabric 群集具有[一个可靠性层](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster)属性，用于确定在群集的主节点类型上运行的系统服务的副本数。 所需的副本数将确定必须在群集的主节点类型中维护的最小节点数。 如果主节点类型中的节点数低于可靠性层所需的最小值，群集将变得不稳定。  

### <a name="error-messages"></a>错误消息 
已检测到种子节点删除操作，并将被拒绝。 
* 此操作将仅{0}导致潜在的种子节点保留在群集中，而{1}至少需要种子节点。
* 删除{0}种子节点{1}将导致群集因种子节点仲裁丢失而关闭。 一次可以删除的最大种子节点数为{2}。
 
### <a name="mitigation"></a>缓解操作 
确保主节点类型有足够的虚拟机，以保证群集上指定的可靠性。 如果虚拟机将设置为低于给定可靠性层的最小节点数，则无法删除虚拟机。
* 如果正确指定了可靠性层，请确保主节点类型中有足够的节点，这是可靠性层所需的。 
* 如果可靠性层不正确，请先对 Service Fabric 资源启动更改以降低可靠性级别，然后再启动任何虚拟机缩放集操作，然后等待它完成。
* 如果可靠性层为"青铜"，请按照[以下步骤](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#manually-remove-vms-from-a-node-typevirtual-machine-scale-set)正常缩小群集。

## <a name="next-steps"></a>后续步骤
* 在运行 Windows Server 的 VM 或计算机上创建群集：[创建适用于 Windows Server 的 Service Fabric 群集](service-fabric-cluster-creation-for-windows-server.md)
* 在运行 Linux 的 VM 或计算机上创建群集：[创建 Linux 群集](service-fabric-cluster-creation-via-portal.md)
* 故障排除服务结构：[故障排除指南](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)
