---
title: include 文件
description: include 文件
services: batch
documentationcenter: ''
author: JnHs
manager: evansma
editor: ''
ms.service: batch
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.date: 06/16/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 3e4bca058f554f60dfa5c237633d1fecf06dfea7
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2020
ms.locfileid: "87507365"
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

`/subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.Network/virtualNetworks/{network}/subnets/{subnet}`

**权限** - 检查在 VNet 的订阅或资源组上实施的安全策略或锁定是否限制用户管理 VNet 所需的权限。

**其他网络资源** - Batch 自动在包含 VNet 的资源组中分配其他网络资源。

> [!IMPORTANT]
> 对于每 100 个专用或低优先级节点，Batch 会分配：1 个网络安全组 (NSG)、1 个公共 IP 地址、1 个负载均衡器。 这些资源受订阅的[资源配额](../articles/azure-resource-manager/management/azure-subscription-service-limits.md)限制。 对于大型池，可能需要为一个或多个此类资源请求增加配额。

#### <a name="network-security-groups-batch-default"></a>网络安全组：Batch 默认值

子网必须允许来自 Batch 服务的入站通信，才能在计算节点上计划任务，必须允许出站通信，才能根据工作负荷需求与 Azure 存储或其他资源通信。 对于“虚拟机”配置中的池，Batch 在附加到计算节点的网络接口 (NIC) 级别添加 NSG。 这些 NSG 配置了以下附加规则：

* 端口 29876 和 29877 上来自 Batch 服务 IP 地址（对应于 `BatchNodeManagement` 服务标记）的入站 TCP 流量。
* 端口 22（Linux 节点）或端口 3389（Windows 节点）上允许远程访问的入站 TCP 流量。 对于 Linux 上某些类型的多实例任务（如 MPI），还需要为包含 Batch 计算节点的子网中的 IP 允许 SSH 端口 22 流量。 这可能会根据子网级 NSG 规则进行阻止（请参阅下文）。
* 任何端口上通往虚拟网络的出站流量。 这可能会根据子网级 NSG 规则进行修改（请参阅下文）。
* 任何端口上通往 Internet 的出站流量。 这可能会根据子网级 NSG 规则进行修改（请参阅下文）。

> [!IMPORTANT]
> 在 Batch 配置的 NSG 中修改或添加入站或出站规则时，请务必小心。 如果 NSG 拒绝与指定子网中的计算节点通信，则 Batch 服务会将计算节点的状态设置为“不可用”。 此外，不得将资源锁应用于 Batch 创建的任何资源，因为这可能会由于用户启动的操作（如删除池）而导致资源清理被阻止。

#### <a name="network-security-groups-specifying-subnet-level-rules"></a>网络安全组：指定子网级规则

无需在子网级别指定 NSG，因为 Batch 会配置其自己的 NSG（请参阅上文）。 如果你的一个 NSG 与部署了 Batch 计算节点的子网关联，或者你要应用自定义 NSG 规则来替代应用的默认值，则必须为此 NSG 至少配置入站和出站安全规则，如下表所示。

在端口 3389 (Windows) 或 22 (Linux) 上配置入站流量的前提是，你需要允许对外部源中的计算节点进行远程访问。 如果需要支持使用某些 MPI 运行时的多实例任务，则可能需要在 Linux 上启用端口 22 规则。 使池计算节点可用不一定需要允许这些端口上的流量。

**入站安全规则**

| 源 IP 地址 | 源服务标记 | 源端口 | 目标 | 目标端口 | 协议 | 操作 |
| --- | --- | --- | --- | --- | --- | --- |
| 空值 | `BatchNodeManagement` [服务标记](../articles/virtual-network/security-overview.md#service-tags)（如果使用区域变体，则在与 Batch 帐户相同的区域中） | * | Any | 29876-29877 | TCP | 允许 |
| 用户源 IP，用于远程访问 Linux 多实例任务的计算节点和/或计算节点子网（如果需要）。 | 空值 | * | Any | 3389 (Windows)、22 (Linux) | TCP | 允许 |

> [!WARNING]
> Batch 服务 IP 地址随时可能会更改。 因此，强烈建议对 NSG 规则使用 `BatchNodeManagement` 服务标记（或区域变体）。 避免用特定 Batch 服务 IP 地址填充 NSG 规则。

**出站安全规则**

| 源 | 源端口 | 目标 | 目标服务标记 | 目标端口 | 协议 | 操作 |
| --- | --- | --- | --- | --- | --- | --- |
| Any | * | [服务标记](../articles/virtual-network/security-overview.md#service-tags) | `Storage`（如果使用区域变体，则在与 Batch 帐户相同的区域中） | 443 | TCP | 允许 |

### <a name="pools-in-the-cloud-services-configuration"></a>“云服务”配置中的池

**支持的 VNet** - 仅限经典 VNet

**子网 ID** - 通过 Batch API 指定子网时，请使用子网的资源标识符。 标识符的形式为：

`/subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.ClassicNetwork /virtualNetworks/{network}/subnets/{subnet}`

**权限** - `Microsoft Azure Batch` 服务主体必须为指定的 VNet 提供 `Classic Virtual Machine Contributor` Azure 角色。

#### <a name="network-security-groups"></a>网络安全组

子网必须允许来自 Batch 服务的入站通信，才能在计算节点上计划任务，必须允许出站通信，才能与 Azure 存储或其他资源通信。

不需指定 NSG，因为 Batch 将入站通信配置为只能从 Batch IP 地址到池节点。 但是，如果指定的子网具有关联的 NSG 和/或防火墙，则配置入站和出站安全规则，如以下各表中所示。 如果 NSG 拒绝与指定子网中的计算节点通信，则 Batch 服务会将计算节点的状态设置为“不可用”。

如果需要允许对池节点进行 RDP 访问，请在端口 3389 上为 Windows 配置入站流量。 无需此项即可使用池节点。

**入站安全规则**

| 源 IP 地址 | 源端口 | 目标 | 目标端口 | 协议 | 操作 |
| --- | --- | --- | --- | --- | --- |
Any <br /><br />虽然这需要有效地“全部允许”，但 Batch 服务会在每个节点级别应用 ACL 规则，以筛选掉所有非 Batch 服务 IP 地址。 | * | Any | 10100、20100、30100 | TCP | 允许 |
| 可选，用于允许对计算节点进行 RDP 访问。 | * | Any | 3389 | TCP | 允许 |

**出站安全规则**

| 源 | 源端口 | 目标 | 目标端口 | 协议 | 操作 |
| --- | --- | --- | --- | --- | --- |
| 任意 | * | 任意 | 443  | Any | Allow |
