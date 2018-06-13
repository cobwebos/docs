---
title: 设置 IP 寻址以在通过 Azure Site Recovery 故障转移后连接到辅助本地站点 | Microsoft Docs
description: 说明如何设置 IP 寻址以在故障转移 Azure Site Recovery 后连接到辅助本地站点中的 VM。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 02/12/2018
ms.author: rayne
ms.openlocfilehash: 531705bc704b3366c1c670ecf07c809ade67bc55
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
ms.locfileid: "29378672"
---
# <a name="set-up-ip-addressing-to-connect-to-a-secondary-on-premises-site-after-failover"></a>设置 IP 寻址以在故障转移后连接到辅助本地站点

在 System Center Virtual Machine Manager (VMM) 云中将 Hyper-V VM 故障转移到辅助站点后，需要能够连接到副本 VM。 本文可帮助执行此操作。 

## <a name="connection-options"></a>连接选项

故障转移后，有几种方法来处理副本 VM 的 IP 寻址： 

- **故障转移后保留相同的 IP 地址**：在此方案中，复制的 VM 具有与主 VM 相同的 IP 地址。 这简化了故障转移后的网络相关问题，但需要某些基础结构工作。
- **故障转移后使用不同的 IP 地址**：在此方案中，VM 会在故障转移后获取新的 IP 地址。 
 

## <a name="retain-the-ip-address"></a>保留 IP 地址

如果希望在故障转移到辅助站点后保留主站点中的 IP 地址，可以：

- 部署主站点和辅助站点之间的外延式子网。
- 执行从主站点到辅助站点的完整子网故障转移。 需要更新路由以指示 IP 地址的新位置。


### <a name="deploy-a-stretched-subnet"></a>部署外延式子网

在外延式配置中，子网可在主站点和辅助站点中同时使用。 在外延式子网中，将计算机及其 IP（第 3 层）地址配置移动到辅助站点时，网络会自动将流量路由到新位置。 

- 从第 2 层（数据链路层）的角度来看，需要可以管理外延式 VLAN 的网络设备。
- 通过延伸 VLAN，潜在容错域也会延伸至这两个站点。 这将成为单一故障点。 尽管可能性不大，但在此方案中你可能无法隔离事件，如广播风暴。 


### <a name="fail-over-a-subnet"></a>故障转移子网

可以故障转移整个子网以获得外延式子网的优势，但实际上不对其进行延伸。 在此解决方案中，可在源或目标站点中使用子网，但不可同时使用。

- 为了能够在故障转移时保留 IP 地址空间，可以编程方式安排路由器基础结构，将子网从一个站点移到另一个站点。
- 发生故障转移时，子网将与关联的 VM 一起移动。
- 此方法的主要缺点是，在出现故障时，必须移动整个子网。

#### <a name="example"></a>示例

下面是一个完整的子网故障转移示例。 

- 故障转移前，主站点具有在子网 192.168.1.0/24 中运行的应用程序。
- 在故障转移期间，此子网中的所有 VM 都将故障转移到辅助站点，并保留其 IP 地址。 
- 必须修改所有站点之间的路由，以反映子网 192.168.1.0/24 中的所有 VM 现都已移至辅助站点这一事实。

下图说明故障转移前后的子网：


故障转移前

![在故障转移之前](./media/hyper-v-vmm-networking/network-design2.png)

故障转移后

![在故障转移之后](./media/hyper-v-vmm-networking/network-design3.png)

故障转移后，Site Recovery 为 VM 上的每个网络接口分配 IP 地址。 将为每个 VM 实例从相关网络中的静态 IP 地址池中分配地址。

- 如果辅助站点的 IP 地址池与源站点的 IP 地址池相同，Site Recovery 将向副本 VM 分配与源 VM 相同的 IP 地址。 IP 地址保留在 VMM 中，但未设置为 Hyper-V 主机上的故障转移 IP 地址。 Hyper-V 主机上的故障转移 IP 地址会在故障转移之前设置。
- 如果相同 IP 地址不可用，Site Recovery 将分配该池中的另一可用 IP 地址。
- 如果 VM 使用 DHCP，则 Site Recovery 不管理 IP 地址。 需要检查确定辅助站点上的 DHCP 服务器可分配与源站点相同范围的地址。

### <a name="validate-the-ip-address"></a>验证 IP 地址

为 VM 启用保护后，可以使用以下示例脚本来验证分配给 VM 的地址。 此 IP 地址将被设为故障转移 IP 地址，并在故障转移期间分配给 VM：

    ```
    $vm = Get-SCVirtualMachine -Name <VM_NAME>
    $na = $vm[0].VirtualNetworkAdapters>
    $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
    $ip.address 
    ```

## <a name="use-a-different-ip-address"></a>使用不同 IP 地址

在此方案中，将更改故障转移的 VM 的 IP 地址。 此解决方案的缺点是需要维护。  DNS 和缓存条目可能需要更新。 这可能导致停机时间，可按以下方式进行缓解：

- 对 Intranet 应用程序使用低 TTL 值。
- 在 Site Recovery 恢复计划中使用以下脚本及时更新 DNS 服务器。 如果使用动态 DNS 注册，则不需要该脚本。

    ```
    param(
    string]$Zone,
    [string]$name,
    [string]$IP
    )
    $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
    $newrecord = $record.clone()
    $newrecord.RecordData[0].IPv4Address  =  $IP
    Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord
    ```
    
### <a name="example"></a>示例 

在此示例中，主站点和辅助站点的 IP 地址不同，并且存在一个第三站点，可通过该站点访问托管于主站点或辅助站点上的应用程序。

- 故障转移之前，应用托管于主站点上的子网 192.168.1.0/24。
- 故障转移之后，应用在辅助站点的子网 172.16.1.0/24 中配置。
- 所有三个站点均可以互相访问。
- 故障转移后，应用将在恢复子网中还原。
- 在此方案中，无需故障转移整个子网，并且无需进行重新配置 VPN 或网络路由的更改。 故障转移和部分 DNS 更新会确保应用程序仍然可供访问。
- 如果 DNS 配置为允许动态更新，则 VM 会在故障转移后启动时使用新的 IP 地址自行注册。

故障转移前

![不同的 IP 地址 - 故障转移前](./media/hyper-v-vmm-networking/network-design10.png)

故障转移后

![不同的 IP 地址 - 故障转移后](./media/hyper-v-vmm-networking/network-design11.png)


## <a name="next-steps"></a>后续步骤

[运行故障转移](hyper-v-vmm-failover-failback.md)

