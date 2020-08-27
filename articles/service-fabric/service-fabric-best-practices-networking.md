---
title: Azure Service Fabric 网络最佳做法
description: 使用 Azure Service Fabric 管理网络连接的规则和设计注意事项。
author: chrpap
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: chrpap
ms.openlocfilehash: 0f25627c852befb03c2c32d741b8fe9b64cd4dc2
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "88948957"
---
# <a name="networking"></a>网络

在创建和管理 Azure Service Fabric 群集时，可以为节点和应用程序提供网络连接。 网络资源包括 IP 地址范围、虚拟网络、负载均衡器和网络安全组。 本文介绍针对这些资源的最佳做法。

查看 Azure [Service Fabric 网络模式](./service-fabric-patterns-networking.md)，了解如何创建使用以下功能的群集：现有的虚拟网络或子网、静态公共 IP 地址、仅限内部的负载均衡器，或者内部和外部负载均衡器。

## <a name="infrastructure-networking"></a>基础结构网络
通过在资源管理器模板中声明 *enableAcceleratedNetworking* 属性，使用加速网络最大化虚拟机的性能，以下代码片段是启用加速网络的虚拟机规模集 NetworkInterfaceConfigurations：

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
可以在[使用加速网络的 Linux](../virtual-network/create-vm-accelerated-networking-cli.md) 上以及[使用加速网络的 Windows](../virtual-network/create-vm-accelerated-networking-powershell.md) 上预配 Service Fabric 群集。

以下 Azure 虚拟机系列 SKU 支持加速网络：D/DSv2、D/DSv3、E/ESv3、F/FS、FSv2 以及 Ms/Mms。 已使用01/23/2019 上的 Standard_DS8_v3 SKU 为 Service Fabric Windows 群集成功测试加速网络，并对 Service Fabric Linux 群集使用01/29/2019 上的 Standard_DS12_v2。

若要在现有的 Service Fabric 群集上启用加速网络，需要首先[通过添加虚拟机规模集来横向扩展 Service Fabric 群集](./virtual-machine-scale-set-scale-node-type-scale-out.md)，以便执行以下操作：
1. 预配已启用加速网络的 NodeType
2. 将服务及其状态迁移到已预配并已启用加速网络的 NodeType

若要在现有的群集上启用加速网络，必须横向扩展基础结构，因为就地启用加速网络会导致停机，原因它需要可用性集中的所有虚拟机处于[停止和解除分配状态，然后才能在现有 NIC 上启用加速网络](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms)。

## <a name="cluster-networking"></a>群集网络

* Service Fabric 群集可以部署到现有的虚拟网络中，只需执行 [Service Fabric 网络模式](./service-fabric-patterns-networking.md)中介绍的步骤即可。

* 建议使用 (Nsg) 的网络安全组来限制节点类型的入站和出站流量。 确保在 NSG 中打开所需的端口。 

* Service Fabric 系统服务所在的主节点类型不需通过外部负载均衡器公开，可以通过[内部负载均衡器](./service-fabric-patterns-networking.md#internal-only-load-balancer)公开

* 将[静态公共 IP 地址](./service-fabric-patterns-networking.md#static-public-ip-address-1)用于群集。

## <a name="network-security-rules"></a>网络安全规则

此处的基本规则是 Azure 托管 Service Fabric 群集的安全锁定的最低要求。 无法打开以下端口或允许列表 IP/URL 将阻止群集正常运行，并且可能不受支持。 使用此规则集时，严格需要使用 [自动 OS 映像升级](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)，否则需要打开其他端口。

### <a name="inbound"></a>入站 
|优先级   |名称               |端口        |协议  |源             |目标       |操作   
|---        |---                |---         |---       |---                |---               |---
|3900       |Azure              |19080       |TCP       |Internet           |VirtualNetwork    |允许
|3910       |客户端             |19000       |TCP       |Internet           |VirtualNetwork    |允许
|3920       |群集            |1025-1027   |TCP       |VirtualNetwork     |VirtualNetwork    |允许
|3930       |短暂          |49152-65534 |TCP       |VirtualNetwork     |VirtualNetwork    |允许
|3940       |应用程序        |20000-30000 |TCP       |VirtualNetwork     |VirtualNetwork    |允许
|3950       |SMB                |445         |TCP       |VirtualNetwork     |VirtualNetwork    |允许
|3960       |RDP                |3389-3488   |TCP       |Internet           |VirtualNetwork    |Deny
|3970       |SSH                |22          |TCP       |Internet           |VirtualNetwork    |Deny
|3980       |自定义终结点    |80          |TCP       |Internet           |VirtualNetwork    |允许
|4100       |阻止入站      |443         |任意       |任意                |任意               |Allow

有关入站安全规则的详细信息：

* **Azure**。 Service Fabric Explorer 使用此端口来浏览和管理群集，Service Fabric 资源提供程序还使用此端口来查询有关群集的信息，以便在 Azure 管理门户中显示。 如果无法从 Service Fabric 资源提供程序访问此端口，则你将在 Azure 门户中看到一条消息，例如 "找不到节点" 或 "UpgradeServiceNotReachable"，并且你的节点和应用程序列表将显示为空。 这意味着，如果想要在 Azure 管理门户中查看群集，则负载均衡器必须公开公共 IP 地址，并且 NSG 必须允许传入19080流量。  

* **客户端**。 REST/PowerShell/CLI 等 Api 的客户端连接终结点。 

* **群集**。 用于节点间通信;永远不应被阻止。

* **暂时**。 Service Fabric 使用其中的一部分端口作为应用程序端口，剩余的端口供 OS 使用。 它还会将此范围映射到 OS 中的现有范围，因此，可以使用此处的示例中提供的范围。 确保起始端口与结束端口至少相差 255。 如果此差过小，可能会遇到冲突，因为此范围与 OS 共享。 若要查看配置的动态端口范围，请运行 *netsh int ipv4 show 动态端口 tcp*。 Linux 群集不需要这些端口。

* **应用程序**。 应用程序端口范围的大小应足以满足应用程序的终结点要求。 此范围在计算机上的动态端口范围中应是独占的，即按配置中设置的 ephemeralPorts 范围。 Service Fabric 在需要新端口时使用这些端口，并负责为节点上的这些端口打开防火墙。

* **SMB**。 SMB 协议正在由 ImageStore 服务用于两种方案。 此端口是从 ImageStore 中的节点下载包以及在副本之间复制这些包所必需的。 

* **RDP**。 如果需要从 Internet 或 VirtualNetwork for jumpbox 方案，则可选。 

* **SSH**。 如果需要从 Internet 或 VirtualNetwork for jumpbox 方案，则可选。

* **自定义终结点**。 启用可访问 internet 的终结点的应用程序示例。

### <a name="outbound"></a>出站

|优先级   |名称               |端口        |协议  |源             |目标       |操作   
|---        |---                |---         |---       |---                |---               |---
|3900       |网络            |任意         |TCP       |VirtualNetwork     |VirtualNetwork    |允许
|3910       |资源提供程序  |443         |TCP       |VirtualNetwork     |ServiceFabric     |Allow
|3920       |升级            |443         |TCP       |VirtualNetwork     |Internet          |允许
|3950       |阻止出站     |任意         |任意       |任意                |任意               |拒绝

有关出站安全规则的详细信息：

* **网络**。 子网和其他虚拟网络的通信通道。

* **资源提供程序**。 由 UpgradeService 连接以便 Service Fabric 资源提供程序执行所有 ARM 部署。

* **升级**。 使用地址 download.microsoft.com 的升级服务获取 bits，这是安装、重新映像和运行时升级所需的。 服务运行的是动态 IP 地址。 在 "仅限内部" 负载均衡器的情况下，必须使用允许端口443的出站流量的规则将额外的外部负载平衡器添加到模板。 （可选）可以在安装成功后阻止此端口，但在这种情况下，必须将升级包分发到节点，或者需要在短时间内打开端口，之后需要手动升级。

使用带有 [NSG flow 日志](../network-watcher/network-watcher-nsg-flow-logging-overview.md) 和 [流量分析](../network-watcher/traffic-analytics.md) 的 Azure 防火墙跟踪安全锁定的问题。 ARM 模板 [Service Fabric 的 NSG](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) 是一个很好的示例。 


## <a name="application-networking"></a>应用程序网络

* 若要运行 Windows 容器工作负荷，请使用[开放网络模式](./service-fabric-networking-modes.md#set-up-open-networking-mode)，使服务到服务的通信更方便。

* 使用反向代理（例如 [Traefik](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/) 或 [Service Fabric 反向代理](./service-fabric-reverseproxy.md)）公开常用的应用程序端口，例如 80 或 443。

* 对于托管在无法从 Azure 云存储中拉取基本层的气隙计算机上的 Windows 容器，可通过在 Docker 守护程序中使用 [--allow-nondistributable-artifacts](/virtualization/windowscontainers/about/faq#how-do-i-make-my-container-images-available-on-air-gapped-machines) 标志来重写外部层行为。

## <a name="next-steps"></a>后续步骤

* 在运行 Windows Server 的 VM 或计算机上创建群集：[创建适用于 Windows Server 的 Service Fabric 群集](service-fabric-cluster-creation-for-windows-server.md)
* 在运行 Linux 的 VM 或计算机上创建群集：[创建 Linux 群集](service-fabric-cluster-creation-via-portal.md)
* 了解 [Service Fabric 支持选项](service-fabric-support.md)

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png
