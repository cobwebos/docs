---
title: include 文件
description: include 文件
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 04/10/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 711b662c35b5f8fec96f1edee765696bc1028bf8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60550172"
---
### <a name="general-requirements"></a>一般要求

* VNet 必须与用于创建池的 Batch 帐户位于同一订阅和区域中。

* 使用 VNet 的池最多可以有 4096 个节点。

* 为池指定的子网必须提供足够的未分配 IP 地址来容纳面向该池的 VM 的数量；即，池的 `targetDedicatedNodes` 和 `targetLowPriorityNodes` 属性的总和。 如果子网没有足够的未分配 IP 地址，池将分配部分计算节点，并发生调整大小错误。 

* 需要通过为 VNet 提供服务的自定义 DNS 服务器解析 Azure 存储终结点。 具体而言，`<account>.table.core.windows.net`、`<account>.queue.core.windows.net` 和 `<account>.blob.core.windows.net` 形式的 URL 应当是可以解析的。 

其他 VNet 要求会有所不同，具体取决于 Batch 池是使用“虚拟机”配置还是使用“云服务”配置。 若要进行新的池部署（部署到 VNet 中），建议使用“虚拟机”配置。

### <a name="pools-in-the-virtual-machine-configuration"></a>“虚拟机”配置中的池

**支持的 VNet** - 仅限基于 Azure 资源管理器的 VNet

**子网 ID** - 通过 Batch API 指定子网时，请使用子网的资源标识符。 标识符的形式为：

  ```
  /subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.Network/virtualNetworks/{network}/subnets/{subnet}
  ```

**权限** - 检查在 VNet 的订阅或资源组上实施的安全策略或锁定是否限制用户管理 VNet 所需的权限。

**其他网络资源** - Batch 自动在包含 VNet 的资源组中分配其他网络资源。 对于每个 50 的专用的节点 （或每 20 个低优先级节点），Batch 分配的内存：1 个网络安全组 (NSG)、 1 个公共 IP 地址和 1 负载均衡器。 这些资源受订阅的[资源配额](../articles/azure-subscription-service-limits.md)限制。 对于大型池，可能需要为一个或多个此类资源请求增加配额。

#### <a name="network-security-groups"></a>网络安全组

子网必须允许来自 Batch 服务的入站通信，才能在计算节点上计划任务，必须允许出站通信，才能与 Azure 存储或其他资源通信。 对于“虚拟机”配置中的池，Batch 在附加到 VM 的网络接口 (NIC) 级别添加 NSG。 这些 NSG 自动配置允许以下流量的入站和出站规则：

* 端口 29876 和 29877 上来自 Batch 服务角色 IP 地址的入站 TCP 流量。 
* 端口 22（Linux 节点）或端口 3389（Windows 节点）上允许远程访问的入站 TCP 流量。 对于某些类型的多实例任务 （如 MPI) 在 Linux 上，你将需要还允许 SSH 端口 22 流量 ip 包含 Batch 计算节点的子网中。
* 任何端口上通往虚拟网络的出站流量。
* 任何端口上通往 Internet 的出站流量。

> [!IMPORTANT]
> 在 Batch 配置的 NSG 中修改或添加入站或出站规则时，请务必小心。 如果 NSG 拒绝与指定子网中的计算节点通信，则 Batch 服务会将计算节点的状态设置为“不可用”。

不需在子网级别指定 NSG，因为 Batch 会配置其自己的 NSG。 但是，如果指定的子网具有关联的网络安全组 (NSG) 和/或防火墙，则配置入站和出站安全规则，如以下各表中所示。 仅当你需要从外部源允许池 Vm 的远程访问配置入站的流量在端口 3389 上 (Windows) 或 22 (Linux)。 不配置它也可使用池 VM。 请注意，你将需要启用适用于 Linux 端口 22 上的虚拟网络子网流量，如果使用的某些类型的多实例任务，例如 MPI。

**入站安全规则**

| 源 IP 地址 | 源服务标记 | 源端口 | 目标 | 目标端口 | Protocol | 操作 |
| --- | --- | --- | --- | --- | --- | --- |
| 不适用 | `BatchNodeManagement` [服务标记](../articles/virtual-network/security-overview.md#service-tags) | * | 任意 | 29876-29877 | TCP | 允许 |
| 如果需要远程访问计算节点和/或 Linux 多实例任务的计算节点子网的用户源 Ip。 | 不适用 | * | 任意 | 3389 (Windows)、22 (Linux) | TCP | 允许 |

**出站安全规则**

| 源 | 源端口 | 目标 | 目标服务标记 | Protocol | 操作 |
| --- | --- | --- | --- | --- | --- |
| 任意 | 443 | [服务标记](../articles/virtual-network/security-overview.md#service-tags) | `Storage` （在与 Batch 帐户及 VNet 位于同一区域）  | 任意 | 允许 |

### <a name="pools-in-the-cloud-services-configuration"></a>“云服务”配置中的池

**支持的 VNet** - 仅限经典 VNet

**子网 ID** - 通过 Batch API 指定子网时，请使用子网的资源标识符。 标识符的形式为：

  ```
  /subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.ClassicVirtualNetwork /virtualNetworks/{network}/subnets/{subnet}
  ```

**权限** - `MicrosoftAzureBatch` 服务主体必须为指定的 VNet 提供 `Classic Virtual Machine Contributor` 基于角色的访问控制 (RBAC) 角色。

#### <a name="network-security-groups"></a>网络安全组

子网必须允许来自 Batch 服务的入站通信，才能在计算节点上计划任务，必须允许出站通信，才能与 Azure 存储或其他资源通信。

不需指定 NSG，因为 Batch 将入站通信配置为只能从 Batch IP 地址到池节点。 但是，如果指定的子网具有关联的 NSG 和/或防火墙，则配置入站和出站安全规则，如以下各表中所示。 如果 NSG 拒绝与指定子网中的计算节点通信，则 Batch 服务会将计算节点的状态设置为“不可用”。

在端口 3389 上配置的 Windows 的入站的流量，如果你需要允许到池节点的 RDP 访问。 不配置它也可使用池节点。

**入站安全规则**

| 源 IP 地址 | 源端口 | 目标 | 目标端口 | Protocol | 操作 |
| --- | --- | --- | --- | --- | --- |
任意 <br /><br />虽然这需要有效地“全部允许”，但 Batch 服务会在每个节点级别应用 ACL 规则，以筛选掉所有非 Batch 服务 IP 地址。 | * | 任意 | 10100、20100、30100 | TCP | 允许 |
| 可选的以允许 RDP 访问计算节点。 | * | 任意 | 3389 | TCP | 允许 |

**出站安全规则**

| 源 | 源端口 | 目标 | 目标端口 | Protocol | 操作 |
| --- | --- | --- | --- | --- | --- |
| 任意 | * | 任意 | 443  | 任意 | 允许 |
