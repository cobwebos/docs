---
title: 노드 유형과 가상 머신 확장 집합
description: 了解 Azure Service Fabric 节点类型与虚拟机规模集之间的关系，以及如何远程连接到规模集实例或群集节点。
ms.topic: conceptual
ms.date: 03/23/2018
ms.author: pepogors
ms.custom: sfrev
ms.openlocfilehash: e751b3dd9108d364c900bbd059dc89c1eb3770c4
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722333"
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Azure Service Fabric 노드 형식 및 가상 머신 확장 집합

[가상 머신 확장 집합](/azure/virtual-machine-scale-sets)은 Azure 컴퓨팅 리소스입니다. 확장 집합을 사용하여 가상 머신 컬렉션을 배포 및 관리할 수 있습니다. Azure Service Fabric 클러스터에서 정의한 각 노드 형식은 별도의 확장 집합을 설정합니다. Service Fabric 运行时安装在由*ServiceFabric*虚拟机扩展的规模集中的每个虚拟机上。 각 노드 형식을 독립적으로 확장 또는 축소하고, 각 클러스터 노드에서 실행되는 OS SKU를 변경하고, 다른 포트의 집합을 열고, 다른 용량 메트릭을 사용할 수 있습니다.

下图显示了一个具有两个节点类型的群集，分别名为*前端*和*后端*。 각 노드 형식에는 5개의 노드가 있습니다.

![두 가지 노드 유형이 있는 클러스터][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>노드에 가상 머신 확장 집합 인스턴스 매핑

위의 그림에서처럼 확장 집합 인스턴스는 인스턴스 0에서 시작한 다음 1씩 증가합니다. 노드 이름에 이러한 번호 매기기가 반영됩니다. 예를 들어 노드 BackEnd_0은BackEnd 확장 집합의 인스턴스 0입니다. 이 특정 VM 확장 집합에는 이름이 BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 및 BackEnd_4인 5개의 인스턴스가 있습니다.

확장 집합을 확대하는 경우 새 인스턴스가 생성됩니다. 새 확장 집합 인스턴스 이름은 일반적으로 확장 집합 이름 + 다음 인스턴스 번호입니다. 이 예제에서는 BackEnd_5입니다.

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>확장 집합 부하 분산 장치를 노드 형식 및 확장 집합에 매핑

Azure Portal에 클러스터를 배포했거나 샘플 Azure Resource Manager 템플릿을 사용한 경우 리소스 그룹의 모든 리소스가 목록에 열거됩니다. 각 확장 집합 또는 노드 형식에 대해 부하 분산 장치를 확인할 수 있습니다. 부하 분산 장치 이름은 **LB-&lt;노드 형식 이름&gt;** 형식을 사용합니다. 다음 그림에서처럼 LB-sfcluster4doc-0을 예로 들 수 있습니다.

![리소스][Resources]

## <a name="service-fabric-virtual-machine-extension"></a>Service Fabric 虚拟机扩展

Service Fabric 虚拟机扩展用于启动向 Azure 虚拟机 Service Fabric，并配置节点安全性。

下面是 Service Fabric 虚拟机扩展的代码段：

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

| **이름** | **허용되는 값** | **지침 또는 간단한 설명** |
| --- | --- | --- | --- |
| name | 문자열 | 扩展的唯一名称 |
| type | "ServiceFabricLinuxNode" 或 "ServiceFabricWindowsNode" | 标识要引导的操作系统 Service Fabric |
| autoUpgradeMinorVersion | true 또는 false | 启用 SF 运行时次要版本自动升级 |
| publisher | Microsoft.Azure.ServiceFabric | Service Fabric 扩展发布服务器的名称 |
| clusterEndpont | 문자열 | URI：端口到管理终结点 |
| nodeTypeRef | 문자열 | nodeType 的名称 |
| durabilityLevel | 青铜，银，黄金，白金 | 允许暂停不可变的 Azure 基础结构的时间 |
| enableParallelJobs | true 또는 false | 在同一规模集中并行启用计算 ParallelJobs，如删除 VM 和重新启动 VM |
| nicPrefixOverride | 문자열 | 子网前缀，如 "10.0.0.0/24" |
| commonNames | string[] | 已安装群集证书的公用名称 |
| x509StoreName | 문자열 | 安装了群集证书的存储的名称 |
| typeHandlerVersion | 1.1 | 扩展名的版本。 1.0 建议将经典版扩展升级到1。1 |
| 数据路径 | 문자열 | 用于保存 Service Fabric 系统服务和应用程序数据状态的驱动器路径。

## <a name="next-steps"></a>다음 단계

* ["어디에나 배포" 기능의 개요 및 Azure 관리된 클러스터와 비교](service-fabric-deploy-anywhere.md)를 참조하세요.
* [클러스터 보안](service-fabric-cluster-security.md)에 대해 알아보기
* 특정 확장 집합 인스턴스에 [원격으로 연결](service-fabric-cluster-remote-connect-to-azure-cluster-node.md)
* 배포 후에 클러스터 VM에서 [RDP 포트 범위 값 업데이트](./scripts/service-fabric-powershell-change-rdp-port-range.md)
* 클러스터 VM에 대한 [관리자 사용자 이름 및 암호 변경](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md)

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
