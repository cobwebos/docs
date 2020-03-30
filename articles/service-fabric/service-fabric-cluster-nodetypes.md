---
title: 节点类型和虚拟机规模集
description: 了解 Azure 服务结构节点类型如何与虚拟机缩放集相关，以及如何远程连接到规模集实例或群集节点。
ms.topic: conceptual
ms.date: 03/23/2018
ms.author: pepogors
ms.custom: sfrev
ms.openlocfilehash: 37d4c27d3033545c523cefc2f317073af531f095
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78199710"
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Azure Service Fabric 节点类型与虚拟机规模集

[虚拟机规模集](/azure/virtual-machine-scale-sets)是一种 Azure 计算资源。 可使用规模集以集的形式部署和管理虚拟机集合。 在 Azure Service Fabric 群集中定义的每个节点类型都完全设置一个比例集：多个节点类型不能由同一比例集备份，并且一个节点类型不应（在大多数情况下）由多个比例集备份。 例外情况是[，在节点类型垂直缩放](service-fabric-best-practices-capacity-scaling.md#vertical-scaling-considerations)的罕见情况下，当您暂时具有两个具有相同`nodeTypeRef`值的比例集，而副本从原始比例集迁移到升级的比例集时。

服务交换矩阵运行时安装在*Microsoft.Azure.ServiceFabric*虚拟机扩展设置的规模中的每台虚拟机上。 可独立增加或减少每个节点类型、更改每个群集节点上运行的 OS SKU、打开不同的端口集，并使用不同的容量指标。

下图显示了具有两个节点类型的群集，称为*FrontEnd*和*BackEnd*。 每个节点类型具有五个节点。

![具有两个节点类型的群集][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>将虚拟机规模集实例映射到节点

如上图中所示，缩放集实例以实例 0 开始，然后以基数 1 递增。 编号反映在节点名称中。 例如，节点 BackEnd_0 是 BackEnd 规模集的实例 0。 此特定规模集具有五个实例，名称分别为 BackEnd_0、BackEnd_1、BackEnd_2、BackEnd_3、BackEnd_4。

扩展规模集时，将创建新的实例。 新的规模集实例名称通常是规模集名称加上下一个实例编号。 在本示例中，即 BackEnd_5。

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>将规模集负载均衡器映射到节点类型和规模集

如果已在 Azure 门户部署群集，或使用了示例 Azure 资源管理器模板，将列出资源组下所有的资源。 可看到每个规模集或节点类型的负载均衡器。 负载均衡器名称使用以下格式：LB-&lt;节点类型名称&gt;****。 例如，下图中显示的 LB-sfcluster4doc-0：

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

| **名称** | **允许的值** | **指导或简短描述** |
| --- | --- | --- | --- |
| name | 字符串 | 扩展的唯一名称 |
| type | "服务结构Linux节点"或"服务结构窗口节点" | 确定 OS Service Fabric 正启动到 |
| autoUpgradeMinorVersion | True 或 False | 启用自动升级 SF 运行时次要版本的功能 |
| 发布者 | Microsoft.Azure.ServiceFabric | 服务结构扩展发布者的名称 |
| clusterEndpont | 字符串 | 管理终结点的 URI:PORT |
| nodeTypeRef | 字符串 | 节点类型的名称 |
| durabilityLevel | 青铜、白银、黄金、白金 | 允许暂停不可变 Azure 基础结构的时间 |
| enableParallelJobs | True 或 False | 启用计算 ParallelJobs，例如在同一规模集中以并行方式删除 VM 和重启 VM |
| nicPrefixOverride | 字符串 | 子网前缀，例如“10.0.0.0/24” |
| commonNames | string[] | 已安装群集证书的公用名 |
| x509StoreName | 字符串 | 已安装群集证书所在的存储的名称 |
| typeHandlerVersion | 1.1 | 扩展版本。 建议使用 1.0 经典版扩展来升级到 1.1 |
| dataPath | 字符串 | 用于保存 Service Fabric 系统服务状态和应用程序数据的驱动器路径。

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
