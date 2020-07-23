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
ms.openlocfilehash: 1b21141a4b3f9ae92cdcf1d5a93a457012cb136a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85506578"
---
### <a name="general-requirements"></a>一般要求

* VNet 必须与用于创建池的 Batch 帐户位于同一订阅和区域中。

* 使用 VNet 的池最多可以有 4096 个节点。

* 为池指定的子网必须提供足够的未分配 IP 地址来容纳面向该池的 VM 的数量；即，池的 `targetDedicatedNodes` 和 `targetLowPriorityNodes` 属性的总和。 如果子网没有足够的未分配 IP 地址，池将分配部分计算节点，并发生调整大小错误。

* 需要通过为 VNet 提供服务的自定义 DNS 服务器解析 Azure 存储终结点。 具体而言，`<account>.table.core.windows.net`、`<account>.queue.core.windows.net` 和 `<account>.blob.core.windows.net` 形式的 URL 应当是可以解析的。

其他 VNet 要求会有所不同，具体取决于 Batch 池是使用“虚拟机”配置还是使用“云服务”配置。 若要进行新的池部署（部署到 VNet 中），建议使用“虚拟机”配置。

### <a name="pools-in-the-virtual-machine-configuration"></a>“虚拟机”配置中的池

**支持的 VNet** - 仅限基于 Azure 资源管理器的 VNet

**子网 ID** - 通过 Batch API 指定子网时，请使用子网的资源标识符。** 标识符的形式为：

`/subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.Network/virtualNetworks/{network}/subnets/{subnet}`

**权限** - 检查在 VNet 的订阅或资源组上实施的安全策略或锁定是否限制用户管理 VNet 所需的权限。

**其他网络资源** - Batch 自动在包含 VNet 的资源组中分配其他网络资源。

> [!IMPORTANT]
> 对于每个100专用节点或低优先级节点，批处理分配：一个网络安全组（NSG）、一个公共 IP 地址和一个负载均衡器。 这些资源受订阅的[资源配额](../articles/azure-resource-manager/management/azure-subscription-service-limits.md)限制。 对于较大的池，你可能需要为这些资源中的一个或多个请求增加配额。

#### <a name="network-security-groups-batch-default"></a>网络安全组： Batch 默认值

子网必须允许来自批处理服务的入站通信，才能在计算节点上计划任务，以及根据工作负荷的需要，与 Azure 存储或其他资源通信的出站通信。 对于虚拟机配置中的池，Batch 会在附加到计算节点的网络接口（Nic）级别添加 Nsg。 这些 Nsg 配置了以下附加规则：

* 端口29876和29877上的入站 TCP 流量与服务标记对应的批处理服务 IP 地址 `BatchNodeManagement` 。
* 端口 22（Linux 节点）或端口 3389（Windows 节点）上允许远程访问的入站 TCP 流量。 对于 Linux 上某些类型的多实例任务（如 MPI），还需要为包含 Batch 计算节点的子网中的 Ip 允许 SSH 端口22流量。 每个子网级 NSG 规则可能会阻止此情况（请参阅下文）。
* 任何端口上通往虚拟网络的出站流量。 每个子网级 NSG 规则可能会修改此项（请参阅下文）。
* 任何端口上到 Internet 的出站流量。 每个子网级 NSG 规则可能会修改此项（请参阅下文）。

> [!IMPORTANT]
> 如果修改或添加批处理配置的 Nsg 中的入站或出站规则，则请小心。 如果 NSG 拒绝了与指定子网中计算节点的通信，批处理服务会将计算节点的状态设置为 "**不可用**"。 此外，不应将资源锁应用于 Batch 创建的任何资源，因为这样做可以防止由于用户启动的操作（如删除池）而清理资源。

#### <a name="network-security-groups-specifying-subnet-level-rules"></a>网络安全组：指定子网级规则

无需在虚拟网络子网级别指定 Nsg，因为 Batch 会配置自己的 Nsg （请参阅上文）。 如果有一个与部署批处理计算节点的子网关联的 NSG，或者如果想要应用自定义 NSG 规则来替代应用的默认值，则必须至少使用下表中所示的入站和出站安全规则配置此 NSG。

仅当你需要允许从外部源对计算节点进行远程访问时，才在端口3389（Windows）或22（Linux）上配置入站流量。 如果需要在某些 MPI 运行时中支持多实例任务，你可能需要在 Linux 上启用端口22规则。 为了使池计算节点可以使用，不需要允许这些端口上的流量。

**入站安全规则**

| 源 IP 地址 | 源服务标记 | 源端口 | 目标 | 目标端口 | 协议 | 操作 |
| --- | --- | --- | --- | --- | --- | --- |
| 不适用 | `BatchNodeManagement`[服务标记](../articles/virtual-network/security-overview.md#service-tags)（如果使用区域变体，则在与 Batch 帐户相同的区域中） | * | Any | 29876-29877 | TCP | 允许 |
| 用于远程访问计算节点和/或适用于 Linux 多实例任务的计算节点子网（如果需要）的用户源 Ip。 | 不适用 | * | Any | 3389 (Windows)、22 (Linux) | TCP | 允许 |

> [!WARNING]
> Batch 服务 IP 地址随时可能会更改。 因此，强烈建议使用 `BatchNodeManagement` NSG 规则的服务标记（或区域变体）。 避免用特定的批处理服务 IP 地址填充 NSG 规则。

**入站安全规则**

| 源 | 源端口 | 目标 | 目标服务标记 | 目标端口 | 协议 | 操作 |
| --- | --- | --- | --- | --- | --- | --- |
| Any | * | [服务标记](../articles/virtual-network/security-overview.md#service-tags) | `Storage`（如果使用区域变量，请在与 Batch 帐户相同的区域中） | 443 | TCP | 允许 |

### <a name="pools-in-the-cloud-services-configuration"></a>“云服务”配置中的池

**支持的 VNet** - 仅限经典 VNet

**子网 ID** - 通过 Batch API 指定子网时，请使用子网的资源标识符。** 标识符的形式为：

`/subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.ClassicNetwork /virtualNetworks/{network}/subnets/{subnet}`

**权限** - `Microsoft Azure Batch` 服务主体必须为指定的 VNet 提供 `Classic Virtual Machine Contributor` 基于角色的访问控制 (RBAC) 角色。

#### <a name="network-security-groups"></a>网络安全组

子网必须允许来自 Batch 服务的入站通信，才能在计算节点上计划任务，必须允许出站通信，才能与 Azure 存储或其他资源通信。

不需指定 NSG，因为 Batch 将入站通信配置为只能从 Batch IP 地址到池节点。 但是，如果指定的子网具有关联的 NSG 和/或防火墙，则配置入站和出站安全规则，如以下各表中所示。 如果 NSG 拒绝了与指定子网中计算节点的通信，则批处理服务会将计算节点的状态设置为 "**不可用**"。

如果需要允许 RDP 访问池节点，请在端口 3389 for Windows 上配置入站流量。 这不是池节点可用的要求。

**入站安全规则**

| 源 IP 地址 | 源端口 | 目标 | 目标端口 | 协议 | 操作 |
| --- | --- | --- | --- | --- | --- |
Any <br /><br />虽然这需要有效地“全部允许”，但 Batch 服务会在每个节点级别应用 ACL 规则，以筛选掉所有非 Batch 服务 IP 地址。 | * | Any | 10100、20100、30100 | TCP | 允许 |
| 可选，允许 RDP 访问计算节点。 | * | Any | 3389 | TCP | 允许 |

**入站安全规则**

| 源 | 源端口 | 目标 | 目标端口 | 协议 | 操作 |
| --- | --- | --- | --- | --- | --- |
| 任意 | * | 任意 | 443  | Any | Allow |
