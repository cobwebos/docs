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
ms.openlocfilehash: a5f8735df2b230de2b0ddcdcccff09430bada9e3
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64684694"
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Azure Service Fabric 节点类型与虚拟机规模集
[虚拟机规模集](/azure/virtual-machine-scale-sets)是一种 Azure 计算资源。 可使用规模集以集的形式部署和管理虚拟机集合。 Azure Service Fabric 群集中定义的每个节点类型均设置了独立的规模集。  在规模集中 Microsoft.Azure.ServiceFabric 虚拟机扩展的每个虚拟机上安装的 Service Fabric 运行时。 可独立增加或减少每个节点类型、更改每个群集节点上运行的 OS SKU、打开不同的端口集，并使用不同的容量指标。

下图显示了具有名为 FrontEnd 和 BackEnd 的两个节点类型的群集。 每个节点类型具有五个节点。

![具有两个节点类型的群集][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>将虚拟机规模集实例映射到节点
如上图中所示，缩放集实例以实例 0 开始，然后以基数 1 递增。 编号反映在节点名称中。 例如，节点 BackEnd_0 是 BackEnd 规模集的实例 0。 此特定规模集具有五个实例，名称分别为 BackEnd_0、BackEnd_1、BackEnd_2、BackEnd_3、BackEnd_4。

扩展规模集时，将创建新的实例。 新的规模集实例名称通常是规模集名称加上下一个实例编号。 在本示例中，即 BackEnd_5。

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>将规模集负载均衡器映射到节点类型和规模集
如果已在 Azure 门户部署群集，或使用了示例 Azure 资源管理器模板，将列出资源组下所有的资源。 可看到每个规模集或节点类型的负载均衡器。 负载均衡器名称使用以下格式：**LB-&lt;节点类型名称&gt;**。 例如，下图中显示的 LB-sfcluster4doc-0：

![资源][Resources]

## <a name="service-fabric-virtual-machine-extension"></a>Service Fabric 虚拟机扩展
Service Fabric 虚拟机扩展用于启动 Service Fabric 与 Azure 虚拟机，并配置节点安全性。

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

属性说明如下：

| **名称** | **允许的值** | ** --- ** | **指导或简短说明** |
| --- | --- | --- | --- |
| 名称 | string | --- | 扩展插件的的唯一名称 |
| type | "ServiceFabricLinuxNode"或者"ServiceFabricWindowsNode | --- | 标识 OS Service Fabric 引导到 |
| autoUpgradeMinorVersion | true 或 false | --- | 启用自动升级的 SF 运行时的次要版本 |
| 发布者 | Microsoft.Azure.ServiceFabric | --- | Service Fabric 扩展发布服务器的名称 |
| clusterEndpont | string | --- | URI:PORT 到管理终结点 |
| nodeTypeRef | string | --- | 节点类型的名称 |
| durabilityLevel | 铜卡、 silver、 金卡、 白金卡 | --- | 允许暂停不可变的 Azure 基础结构的时间 |
| enableParallelJobs | true 或 false | --- | 启用像删除 VM 并重启 VM 在相同的规模集的并行计算 ParallelJobs |
| nicPrefixOverride | string | --- | 如"10.0.0.0/24"子网前缀 |
| commonNames | string[] | --- | 已安装的群集证书的公用名 |
| x509StoreName | string | --- | 已安装的群集证书所在的位置的存储区的名称 |
| typeHandlerVersion | 1.1 | --- | 扩展的版本。 建议使用扩展的 1.0 经典版升级到 1.1 |

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
