---
title: Service Fabric Azure 资源管理器 deployment guardrails |Microsoft Docs
description: 本文概述通过 Azure 部署 Service Fabric 群集时的常见错误资源管理器以及如何避免这些错误。
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: gamonroy
editor: ''
ms.assetid: 803c9c63-373a-4d6a-8ef2-ea97e16e88dd
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/30/2019
ms.author: pepogors
ms.openlocfilehash: 3ea6f850685a695644cfc3073fc939a58901658c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73828625"
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


### <a name="mitigation"></a>缓解措施
若要解决持久性不匹配问题，请按以上任何错误消息中所示：
1. 请在 Azure 资源管理器模板的 "虚拟机规模集扩展" 或 "Service Fabric 节点类型" 部分中更新持久性级别，以确保值匹配。
2. 用更新的值重新部署 Azure 资源管理器模板。

## <a name="next-steps"></a>后续步骤
* 在运行 Windows Server 的 VM 或计算机上创建群集：[创建适用于 Windows Server 的 Service Fabric 群集](service-fabric-cluster-creation-for-windows-server.md)
* 在运行 Linux 的 VM 或计算机上创建群集：[创建 Linux 群集](service-fabric-cluster-creation-via-portal.md)
* 疑难解答 Service Fabric：[疑难解答指南](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)
