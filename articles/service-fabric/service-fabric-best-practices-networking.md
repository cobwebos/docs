---
title: Azure Service Fabric 网络最佳做法
description: 使用 Azure Service Fabric 管理网络连接的最佳实践和设计注意事项。
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: de2a74ad2d61de18d2150b72be3251e5b5583f2e
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2019
ms.locfileid: "75551788"
---
# <a name="networking"></a>联网

在创建和管理 Azure Service Fabric 群集时，可以为节点和应用程序提供网络连接。 网络资源包括 IP 地址范围、虚拟网络、负载均衡器和网络安全组。 本文介绍针对这些资源的最佳做法。

查看 Azure [Service Fabric 网络模式](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking)，了解如何创建使用以下功能的群集：现有虚拟网络或子网、静态公共 IP 地址、仅限内部的负载均衡器或内部和外部负载均衡器。

## <a name="infrastructure-networking"></a>基础结构网络
使用加速网络使虚拟机的性能最大化，方法是：在资源管理器模板中声明 enableAcceleratedNetworking 属性。以下代码片段是关于虚拟机规模集 NetworkInterfaceConfigurations 的，后者可以启用加速网络：

```json
"networkInterfaceConfigurations": [
  {
    "name": "[concat(variables('nicName'), '-0')]",
    "properties": {
      "enableAcceleratedNetworking": true,
      "ipConfigurations": [
        {
        <snip>
        }
      ],
      "primary": true
    }
  }
]
```
可以在[使用加速网络的 Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) 上以及[使用加速网络的 Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) 上预配 Service Fabric 群集。

Azure 虚拟机系列 Sku 支持加速网络： D/DSv2、D/DSv3、E/ESv3、F/FS、FSv2 和 Ms/Mms。 加速网络已使用 Standard_DS8_v3 SKU 在 2019 年 1 月 23 日针对 Service Fabric Windows 群集成功进行测试，以及使用 Standard_DS12_v2 在 2019 年 1 月 29 日针对 Service Fabric Linux 群集成功进行测试。

若要在现有的 Service Fabric 群集上启用加速网络，需要首先[通过添加虚拟机规模集来横向扩展 Service Fabric 群集](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out)，以便执行以下操作：
1. 预配已启用加速网络的 NodeType
2. 将服务及其状态迁移到已预配并已启用加速网络的 NodeType

若要在现有的群集上启用加速网络，必须横向扩展基础结构，因为就地启用加速网络会导致停机，原因它需要可用性集中的所有虚拟机处于[停止和解除分配状态，然后才能在现有 NIC 上启用加速网络](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms)。

## <a name="cluster-networking"></a>群集网络

* Service Fabric 群集可以部署到现有的虚拟网络中，只需执行 [Service Fabric 网络模式](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking)中介绍的步骤即可。

* 对于限制群集的入站和出站流量的节点类型，建议使用网络安全组 (NSG)。 确保在 NSG 中打开所需的端口。 例如： ![Service Fabric NSG 规则][NSGSetup]

* Service Fabric 系统服务所在的主节点类型不需通过外部负载均衡器公开，可以通过[内部负载均衡器](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#internal-only-load-balancer)公开

* 将[静态公共 IP 地址](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#static-public-ip-address-1)用于群集。

## <a name="application-networking"></a>应用程序网络

* 若要运行 Windows 容器工作负荷，请使用[开放网络模式](https://docs.microsoft.com/azure/service-fabric/service-fabric-networking-modes#set-up-open-networking-mode)，使服务到服务的通信更方便。

* 使用反向代理（例如 [Traefik](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/) 或 [Service Fabric 反向代理](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy)）公开常用的应用程序端口，例如 80 或 443。

* 对于在无法从 Azure 云存储中拉取基本层的空气有空隙的计算机上托管的 Windows 容器，请使用 Docker 后台程序中的[--nondistributable](https://docs.microsoft.com/virtualization/windowscontainers/about/faq#how-do-i-make-my-container-images-available-on-air-gapped-machines)标志替代外部层行为。

## <a name="next-steps"></a>后续步骤

* 在运行 Windows Server 的 VM 或计算机上创建群集：[创建适用于 Windows Server 的 Service Fabric 群集](service-fabric-cluster-creation-for-windows-server.md)
* 在运行 Linux 的 VM 或计算机上创建群集：[创建 Linux 群集](service-fabric-cluster-creation-via-portal.md)
* 了解 [Service Fabric 支持选项](service-fabric-support.md)

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png
