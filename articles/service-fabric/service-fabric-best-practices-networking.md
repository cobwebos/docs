---
title: Azure Service Fabric 网络最佳做法
description: 使用 Azure Service Fabric 管理网络连接的规则和设计注意事项。
author: chrpap
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: chrpap
ms.openlocfilehash: b8db69792b31fd82646757423e669e39e8539d06
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91630696"
---
# <a name="networking"></a>网络

在创建和管理 Azure Service Fabric 群集时，可以为节点和应用程序提供网络连接。 网络资源包括 IP 地址范围、虚拟网络、负载均衡器和网络安全组。 本文介绍针对这些资源的最佳做法。

查看 Azure [Service Fabric 网络模式](./service-fabric-patterns-networking.md)，了解如何创建使用以下功能的群集：现有的虚拟网络或子网、静态公共 IP 地址、仅限内部的负载均衡器，或者内部和外部负载均衡器。

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
可以在[使用加速网络的 Linux](../virtual-network/create-vm-accelerated-networking-cli.md) 上以及[使用加速网络的 Windows](../virtual-network/create-vm-accelerated-networking-powershell.md) 上预配 Service Fabric 群集。

以下 Azure 虚拟机系列 SKU 支持加速网络：D/DSv2、D/DSv3、E/ESv3、F/FS、FSv2 以及 Ms/Mms。 加速网络已使用 Standard_DS8_v3 SKU 在 2019 年 1 月 23 日针对 Service Fabric Windows 群集成功进行测试，以及使用 Standard_DS12_v2 在 2019 年 1 月 29 日针对 Service Fabric Linux 群集成功进行测试。

若要在现有的 Service Fabric 群集上启用加速网络，需要首先[通过添加虚拟机规模集来横向扩展 Service Fabric 群集](./virtual-machine-scale-set-scale-node-type-scale-out.md)，以便执行以下操作：
1. 预配已启用加速网络的 NodeType
2. 将服务及其状态迁移到已预配并已启用加速网络的 NodeType

若要在现有的群集上启用加速网络，必须横向扩展基础结构，因为就地启用加速网络会导致停机，原因它需要可用性集中的所有虚拟机处于[停止和解除分配状态，然后才能在现有 NIC 上启用加速网络](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms)。

## <a name="cluster-networking"></a>群集网络

* Service Fabric 群集可以部署到现有的虚拟网络中，只需执行 [Service Fabric 网络模式](./service-fabric-patterns-networking.md)中介绍的步骤即可。

* 对于限制群集的入站和出站流量的节点类型，建议使用网络安全组 (NSG)。 确保在 NSG 中打开所需的端口。 

* Service Fabric 系统服务所在的主节点类型不需通过外部负载均衡器公开，可以通过[内部负载均衡器](./service-fabric-patterns-networking.md#internal-only-load-balancer)公开

* 将[静态公共 IP 地址](./service-fabric-patterns-networking.md#static-public-ip-address-1)用于群集。

## <a name="network-security-rules"></a>网络安全规则

此处的基本规则是 Azure 托管的 Service Fabric 群集的安全锁定的最低要求。 无法打开以下端口或批准 IP/URL 将会阻止群集正常运行，并且可能不受支持。 设置此规则后，严格要求使用[自动 OS 映像升级](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)，否则需要打开其他端口。

### <a name="inbound"></a>入站 
|优先级   |名称               |端口        |协议  |源             |目标       |操作   
|---        |---                |---         |---       |---                |---               |---
|3900       |Azure              |19080       |TCP       |Internet           |VirtualNetwork    |允许
|3910       |客户端             |19000       |TCP       |Internet           |VirtualNetwork    |允许
|3920       |群集            |1025-1027   |TCP       |VirtualNetwork     |VirtualNetwork    |允许
|3930       |临时          |49152-65534 |TCP       |VirtualNetwork     |VirtualNetwork    |允许
|3940       |应用程序        |20000-30000 |TCP       |VirtualNetwork     |VirtualNetwork    |允许
|3950       |SMB                |445         |TCP       |VirtualNetwork     |VirtualNetwork    |允许
|3960       |RDP                |3389-3488   |TCP       |Internet           |VirtualNetwork    |拒绝
|3970       |SSH                |22          |TCP       |Internet           |VirtualNetwork    |拒绝
|3980       |自定义终结点    |80          |TCP       |Internet           |VirtualNetwork    |允许
|4100       |阻止入站      |443         |任意       |任意                |任意               |Allow

有关入站安全规则的更多信息：

* **Azure**。 Service Fabric Explorer 使用此端口浏览和管理群集，Service Fabric 资源提供程序也使用此端口查询有关群集的信息，以便在 Azure 管理门户中显示。 如果无法从 Service Fabric 资源提供程序访问此端口，你将在 Azure 门户中看到“找不到节点”或“UpgradeServiceNotReachable”等消息，并且节点和应用程序列表将显示为空。 这意味着，如果想通过 Azure 管理门户查看群集，负载均衡器必须公开一个公共 IP 地址，而且 NSG 必须允许传入 19080 流量。  

* 客户端。 API 的客户端连接终结点，例如 REST/PowerShell/CLI。 

* **群集**。 用于节点间通信；绝不应被阻止。

* 临时。 Service Fabric 使用其中的一部分端口作为应用程序端口，剩余的端口供 OS 使用。 它还会将此范围映射到 OS 中的现有范围，因此，无论出于何种目的，你都可以使用此处示例中指定的范围。 确保起始端口与结束端口至少相差 255。 如果此差过小，可能会遇到冲突，因为此范围与 OS 共享。 若要查看配置的动态端口范围，请运行 netsh int ipv4 show dynamic port tcp。 Linux 群集不需要这些端口。

* **应用程序**。 应用程序端口范围的大小应足以满足应用程序的终结点要求。 此范围在计算机上的动态端口范围中应是独占的，即按配置中设置的 ephemeralPorts 范围。 每当需要新端口时，Service Fabric 将使用这些端口，并负责为节点上的这些端口打开防火墙。

* SMB。 ImageStore 服务在两个场景中使用 SMB 协议。 节点需要此端口才能从 ImageStore 下载包，以及在副本之间复制这些包。 

* RDP。 可选（如果对于 jumpbox 场景，Internet 或 VirtualNetwork 需要 RDP）。 

* **SSH**。 可选（如果对于 jumpbox 场景，Internet 或 VirtualNetwork 需要 SSH）。

* 自定义终结点。 应用程序启用可访问 Internet 的终结点的示例。

### <a name="outbound"></a>出站

|优先级   |名称               |端口        |协议  |源             |目标       |操作   
|---        |---                |---         |---       |---                |---               |---
|3900       |网络            |任意         |TCP       |VirtualNetwork     |VirtualNetwork    |允许
|3910       |资源提供程序  |443         |TCP       |VirtualNetwork     |ServiceFabric     |Allow
|3920       |升级            |443         |TCP       |VirtualNetwork     |Internet          |允许
|3950       |阻止出站     |任意         |任意       |任意                |任意               |拒绝

有关出站安全规则的更多信息：

* **网络**。 子网和其他虚拟网络的通信通道。

* 资源提供程序。 通过 UpgradeService 进行连接，用于通过 Service Fabric 资源提供程序执行所有 ARM 部署。

* **升级**。 访问地址 download.microsoft.com 以获取位的升级服务，这对于设置、重建映像和运行时升级是必需的。 该服务使用动态 IP 地址运行。 在“仅内部”负载均衡器的场景中，必须使用允许端口 443 出站流量的规则，将附加的外部负载均衡器添加到模板。 或者，可以在成功设置后阻止此端口，但在这种情况下，必须将升级包分发到节点，或者该端口必须在短时间内处于打开状态，然后需要手动升级。

将 Azure 防火墙与 [NSG 流日志](../network-watcher/network-watcher-nsg-flow-logging-overview.md)和[流量分析](../network-watcher/traffic-analytics.md)配合使用，以跟踪与安全锁定有关的问题。 ARM 模板[具有 NSG 的 Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) 是一个良好的着手示例。 


## <a name="application-networking"></a>应用程序网络

* 若要运行 Windows 容器工作负荷，请使用[开放网络模式](./service-fabric-networking-modes.md#set-up-open-networking-mode)，使服务到服务的通信更方便。

* 使用反向代理（例如 [Traefik](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/) 或 [Service Fabric 反向代理](./service-fabric-reverseproxy.md)）公开常用的应用程序端口，例如 80 或 443。

* 对于托管在无法从 Azure 云存储中拉取基本层的气隙计算机上的 Windows 容器，可通过在 Docker 守护程序中使用 [--allow-nondistributable-artifacts](/virtualization/windowscontainers/about/faq#how-do-i-make-my-container-images-available-on-air-gapped-machines) 标志来重写外部层行为。

## <a name="next-steps"></a>后续步骤

* 在运行 Windows Server 的 VM 或计算机上创建群集：[创建适用于 Windows Server 的 Service Fabric 群集](service-fabric-cluster-creation-for-windows-server.md)
* 在运行 Linux 的 VM 或计算机上创建群集：[创建 Linux 群集](service-fabric-cluster-creation-via-portal.md)
* 了解 [Service Fabric 支持选项](service-fabric-support.md)

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png
