---
title: Azure Service Fabric 节点类型与虚拟机规模集 | Microsoft Docs
description: 了解 Azure Service Fabric 节点类型如何与虚拟机规模集相关联，以及如何远程连接到规模集实例或群集节点。
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: chackdan
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: chackdan
ms.openlocfilehash: f33b25112b5c4ee77f1f7d2a419ffb8e926a27d9
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501363"
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Azure Service Fabric 节点类型与虚拟机规模集
[虚拟机规模集](/azure/virtual-machine-scale-sets)是一种 Azure 计算资源。 可使用规模集以集的形式部署和管理虚拟机集合。 Azure Service Fabric 群集中定义的每个节点类型均设置了独立的规模集。  规模集中每个虚拟机上均通过 Microsoft.Azure.ServiceFabric 虚拟机扩展安装了 Service Fabric 运行时。 可独立增加或减少每个节点类型、更改每个群集节点上运行的 OS SKU、打开不同的端口集，并使用不同的容量指标。

下图显示了具有名为 FrontEnd 和 BackEnd 的两个节点类型的群集。 每个节点类型具有五个节点。

![具有两个节点类型的群集][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>将虚拟机规模集实例映射到节点
如上图中所示，缩放集实例以实例 0 开始，然后以基数 1 递增。 编号反映在节点名称中。 例如，节点 BackEnd_0 是 BackEnd 规模集的实例 0。 此特定规模集具有五个实例，名称分别为 BackEnd_0、BackEnd_1、BackEnd_2、BackEnd_3、BackEnd_4。

扩展规模集时，将创建新的实例。 新的规模集实例名称通常是规模集名称加上下一个实例编号。 在本示例中，即 BackEnd_5。

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>将规模集负载均衡器映射到节点类型和规模集
如果已在 Azure 门户部署群集，或使用了示例 Azure 资源管理器模板，将列出资源组下所有的资源。 可看到每个规模集或节点类型的负载均衡器。 负载均衡器名称使用以下格式：LB-&lt;节点类型名称&gt;。 例如，下图中显示的 LB-sfcluster4doc-0：

![资源][Resources]

## <a name="service-fabric-virtual-machine-extension"></a>Service Fabric 虚拟机扩展
Service Fabric 虚拟机扩展用于将 Service Fabric 启动到 Azure 虚拟机以及配置节点安全性。

下面是 Service Fabric 虚拟机扩展的代码片段：

```json
"extensions": [
  {
    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
    "properties": {
      "type": "ServiceFabricLinuxNode",
      "autoUpgradeMinorVersion": true,
      "protectedSettings": {
        "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
       },
       "publisher": "Microsoft.Azure.ServiceFabric",
       "settings": {
         "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
         "nodeTypeRef": "[variables('vmNodeType0Name')]",
         "durabilityLevel": "Silver",
         "enableParallelJobs": true,
         "nicPrefixOverride": "[variables('subnet0Prefix')]",
         "dataPath": "D:\\\\SvcFab",
         "certificate": {
           "commonNames": [
             "[parameters('certificateCommonName')]"
           ],
           "x509StoreName": "[parameters('certificateStoreValue')]"
         }
       },
       "typeHandlerVersion": "1.1"
     }
   },
```

下面是属性说明：

| **名称** | **允许的值** | ** --- ** | **指导或简短说明** |
| --- | --- | --- | --- |
| name | string | --- | 扩展的唯一名称 |
| type | “ServiceFabricLinuxNode”或“ServiceFabricWindowsNode” | --- | 确定 OS Service Fabric 正启动到 |
| autoUpgradeMinorVersion | true 或 false | --- | 启用自动升级 SF 运行时次要版本的功能 |
| publisher | Microsoft.Azure.ServiceFabric | --- | Service Fabric 扩展发布者的名称 |
| clusterEndpont | string | --- | 管理终结点的 URI:PORT |
| nodeTypeRef | string | --- | nodeType 的名称 |
| durabilityLevel | 青铜、白银、黄金、白金 | --- | 允许用于暂停不可变 Azure 基础结构的时间 |
| enableParallelJobs | true 或 false | --- | 启用计算 ParallelJobs，例如在同一规模集中以并行方式删除 VM 和重启 VM |
| nicPrefixOverride | string | --- | 子网前缀，例如“10.0.0.0/24” |
| commonNames | string[] | --- | 已安装群集证书的公用名 |
| x509StoreName | string | --- | 已安装群集证书所在的存储的名称 |
| typeHandlerVersion | 1.1 | --- | 扩展版本。 建议使用 1.0 经典版扩展来升级到 1.1 |
| 数据路径 | string | --- | 用于保存 Service Fabric 系统服务和应用程序数据状态的驱动器路径。 

## <a name="next-steps"></a>后续步骤
* 请参阅[“随地部署”功能的概述及其与 Azure 托管群集的比较](service-fabric-deploy-anywhere.md)。
* 了解[群集安全性](service-fabric-cluster-security.md)。
* 到特定规模集实例的[远程连接](service-fabric-cluster-remote-connect-to-azure-cluster-node.md)
* 部署后在群集 VM 上[更新 RDP 端口范围值](./scripts/service-fabric-powershell-change-rdp-port-range.md)
* 为群集 VM [更改管理员用户名和密码](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md)

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
