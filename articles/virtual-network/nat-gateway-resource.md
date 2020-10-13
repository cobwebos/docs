---
title: 使用 NAT 网关资源设计虚拟网络
titleSuffix: Azure Virtual Network NAT
description: 了解如何使用 NAT 网关资源设计虚拟网络。
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: nat
Customer intent: As an IT administrator, I want to learn more about how to design virtual networks with NAT gateway resources.
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: allensu
ms.openlocfilehash: 62c1b323899f03a043904f4b10d5fe3bb551e0f4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91441757"
---
# <a name="designing-virtual-networks-with-nat-gateway-resources"></a>使用 NAT 网关资源设计虚拟网络

NAT 网关资源是[虚拟网络 NAT](nat-overview.md) 的一部分，为虚拟网络的一个或多个子网提供出站 Internet 连接。 虚拟网络的子网指明要使用的 NAT 网关。 NAT 为子网提供源网络地址转换 (SNAT)。  NAT 网关资源指定虚拟机在创建出站流时要使用的静态 IP 地址。 静态 IP 地址来自公共 IP 地址资源 (PIP) 、公共 IP 前缀资源或两者都有。 如果使用公共 IP 前缀资源，则由 NAT 网关资源使用整个公共 IP 前缀资源的所有 IP 地址。 NAT 网关资源最多可以使用公共 IP 地址资源或公共 IP 前缀资源中的 16 个（总计）静态 IP 地址。

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" alt="Figure depicts a NAT gateway resource that consumes all IP addresses for a public IP prefix and directs that traffic to and from two subnets of virtual machines and a virtual machine scale set." width="256" title="用于出站 Internet 连接的虚拟网络 NAT">
</p>

*图：用于出站 Internet 连接的虚拟网络 NAT*

## <a name="how-to-deploy-nat"></a>如何部署 NAT

我们有意简化了 NAT 网关的配置和使用：  

NAT 网关资源：
- 创建区域性或局部性（区域隔离）NAT 网关资源；
- 分配 IP 地址；
- 如有必要，请修改 TCP 空闲超时（可选）。  在更改默认值<ins>之前</ins>，请查看[计时器](#timers)。

虚拟网络：
- 将虚拟网络子网配置为使用 NAT 网关。

不需要指定用户定义的路由。

## <a name="resource"></a>资源

从以下采用类似于模板格式的 Azure 资源管理器示例中就能看出，资源的设计非常简单。  此处显示了类似于模板的格式用于演示概念和结构。  请根据需要修改示例。  本文档并非旨在用作教程。

下图显示了不同 Azure 资源管理器资源之间的可写引用。  箭头指示引用的方向，从可写位置开始。 审阅 

<p align="center">
  <img src="media/nat-overview/flow-map.svg" alt="Figure depicts a NAT receiving traffic from internal subnets and directing it to a public IP and an IP prefix." width="256" title="虚拟网络 NAT 对象模型">
</p>

*图：虚拟网络 NAT 对象模型*

建议为大多数工作负荷使用 NAT，除非对[基于池的负载均衡器出站连接](../load-balancer/load-balancer-outbound-connections.md)有具体的依赖。  

可以从标准负载均衡器方案（包括[出站规则](../load-balancer/load-balancer-outbound-rules-overview.md)）迁移到 NAT 网关。 若要迁移，请将负载均衡器前端中的公共 IP 和公共 IP 前缀资源移到 NAT 网关。 不需要为 NAT 网关指定新的 IP 地址。 可以重复使用标准公共 IP 地址资源和公共 IP 前缀资源，只要总共不超过 16 个 IP 地址即可。 在转换期间，请规划好迁移并考虑到服务中断。  将此过程自动化可以最大程度地缩减中断时间。 首先在过渡环境中测试迁移。  在转换期间，入站来源流不受影响。


以下示例是 Azure 资源管理器模板中的代码片段。  此模板部署多个资源，其中包括 NAT 网关。  在此示例中，模板有以下参数：

- **natgatewayname** - NAT 网关的名称。
- **location** - 资源所在的 Azure 区域。
- **publicipname** - 与 NAT 网关关联的出站公共 IP 的名称。
- **vnetname** - 虚拟网络的名称。
- **subnetname** - 与 NAT 网关关联的子网的名称。

所有 IP 地址和前缀资源提供的 IP 地址总数不能超过 16 个。 允许提供 1 到 16 范围内的任意数量的 IP 地址。

:::code language="json" source="~/quickstart-templates/101-nat-gateway-vnet/azuredeploy.json" range="81-96":::

创建 NAT 网关资源后，可以在虚拟网络的一个或多个子网上使用它。 指定哪些子网使用此 NAT 网关资源。 一个 NAT 网关不能跨多个虚拟网络。 不需要将同一个 NAT 网关分配到虚拟网络的所有子网。 可以使用不同的 NAT 网关资源配置各个子网。

不使用可用性区域的方案是区域性的（不指定局部区域）。 如果使用可用性区域，则可以指定一个局部区域，将 NAT 隔离到特定的局部区域。 不支持区域冗余。 请查看 NAT [可用性区域](#availability-zones)。

:::code language="json" source="~/quickstart-templates/101-nat-gateway-vnet/azuredeploy.json" range="1-146" highlight="81-96":::

NAT 网关是使用虚拟网络中某个子网上的属性定义的。 虚拟网络 **vnetname** 的子网 **subnetname** 上的虚拟机创建的流将使用 NAT 网关。 所有出站连接将使用与 **natgatewayname** 关联的 IP 地址作为源 IP 地址。

有关此示例中使用的 Azure 资源管理器模板的详细信息，请参阅：

- [快速入门：创建 NAT 网关 - 资源管理器模板](quickstart-create-nat-gateway-template.md)
- [虚拟网络 NAT](https://azure.microsoft.com/resources/templates/101-nat-gateway-1-vm/)

## <a name="design-guidance"></a>设计指南

请阅读本部分来了解有关使用 NAT 设计虚拟网络的注意事项。  

1. [成本优化](#cost-optimization)
1. [入站和出站连接的共存](#coexistence-of-inbound-and-outbound)
2. [管理基本资源](#managing-basic-resources)
3. [可用性区域](#availability-zones)

### <a name="cost-optimization"></a>成本优化

[服务终结点](virtual-network-service-endpoints-overview.md)和[专用链接](../private-link/private-link-overview.md)是可考虑用于优化成本的选项。 这些服务不需要 NAT。 定向到服务终结点或专用链接的流量不会得到虚拟网络 NAT 的处理。  

服务终结点将 Azure 服务资源关联到虚拟网络，并控制对 Azure 服务资源的访问。 例如，在访问 Azure 存储时，可将服务终结点用于存储，以免产生 NAT 数据处理费用。 服务终结点是免费的。

专用链接将 Azure PaaS 服务（或通过专用链接托管的其他服务）作为虚拟网络中的专用终结点公开。  专用链接根据持续时间和处理的数据计费。

请评估上述一种或两种方法是否适合你的方案，并根据需要选用。

### <a name="coexistence-of-inbound-and-outbound"></a>入站和出站连接的共存

NAT 网关与以下资源兼容：

 - 标准负载均衡器
 - 标准公共 IP
 - 标准公共 IP 前缀

开发新的部署时，请从标准 SKU 着手。

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network." width="256" title="用于出站 Internet 连接的虚拟网络 NAT">
</p>

*图：用于出站 Internet 连接的虚拟网络 NAT*

可以使用从 Internet 建立入站连接功能，来扩展 NAT 网关提供的仅限 Internet 出站连接方案。 每个资源都知道流的来源方向。 在使用 NAT 网关的子网上，所有 Internet 出站连接方案都将由 NAT 网关取代。 从 Internet 建立入站连接方案由相应的资源提供。

#### <a name="nat-and-vm-with-instance-level-public-ip"></a>使用实例级公共 IP 的 NAT 和 VM

<p align="center">
  <img src="media/nat-overview/flow-direction2.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with an instance-level public IP." width="300" title="使用实例级公共 IP 的虚拟网络 NAT 和 VM">
</p>

*图：使用实例级公共 IP 的虚拟网络 NAT 和 VM*

| 方向 | 资源 |
|:---:|:---:|
| 入站 | 使用实例级公共 IP 的 VM |
| 出站 | NAT 网关 |

VM 将使用 NAT 网关建立出站连接。  来源入站连接不受影响。

#### <a name="nat-and-vm-with-public-load-balancer"></a>使用公共负载均衡器的 NAT 和 VM

<p align="center">
  <img src="media/nat-overview/flow-direction3.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with a public load balancer." width="350" title="使用公共负载均衡器的虚拟网络 NAT 和 VM">
</p>

*图：使用公共负载均衡器的虚拟网络 NAT 和 VM*

| 方向 | 资源 |
|:---:|:---:|
| 入站 | 公共负载均衡器 |
| 出站 | NAT 网关 |

负载均衡规则或出站规则中的任何出站配置将由 NAT 网关取代。  来源入站连接不受影响。

#### <a name="nat-and-vm-with-instance-level-public-ip-and-public-load-balancer"></a>使用实例级公共 IP 和公共负载均衡器的 NAT 与 VM

<p align="center">
  <img src="media/nat-overview/flow-direction4.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with an instance-level public IP and a public load balancer." width="425" title="使用实例级公共 IP 和公共负载均衡器的虚拟网络 NAT 与 VM">
</p>

*图：使用实例级公共 IP 和公共负载均衡器的虚拟网络 NAT 与 VM*

| 方向 | 资源 |
|:---:|:---:|
| 入站 | 使用实例级公共 IP 和公共负载均衡器的 VM |
| 出站 | NAT 网关 |

负载均衡规则或出站规则中的任何出站配置将由 NAT 网关取代。  VM 也使用 NAT 网关建立出站连接。  来源入站连接不受影响。

### <a name="managing-basic-resources"></a>管理基本资源

标准负载均衡器、公共 IP 和公共 IP 前缀与 NAT 网关兼容。 NAT 网关在子网范围内运行。 必须在不带 NAT 网关的子网上部署这些服务的基本 SKU。 借助这种隔离，两个 SKU 变体可以在同一虚拟网络中共存。

NAT 网关优先于子网的出站方案。 无法通过适当的转换来调整基本负载均衡器或公共 IP（以及使用这些资源构建的任何托管服务）。 NAT 网关控制与子网上 Internet 流量的出站连接。 发往基本负载均衡器和公共 IP 的入站流量将不可用。 发往基本负载均衡器和/或 VM 上配置的公共 IP 的入站流量将不可用。

### <a name="availability-zones"></a>可用性区域

#### <a name="zone-isolation-with-zonal-stacks"></a>使用局部区域堆栈实现区域隔离

<p align="center">
  <img src="media/nat-overview/az-directions.svg" alt="Figure depicts three zonal stacks, each of which contains a NAT gateway and a subnet." width="425" title="提供区域隔离的虚拟网络 NAT，创建多个“区域堆栈” "zonal stacks"">
</p>

图：  实现了区域隔离的虚拟网络 NAT，创建了多个“局部区域性堆栈”

即使没有可用性区域，NAT 也具有复原能力，可以承受多次基础结构组件故障。  可用性区域建立在这种复原能力的基础之上，可为 NAT 提供区域隔离方案。

虚拟网络及其子网是区域性构造。  子网不局限于某个区域。

当使用 NAT 网关资源的虚拟机实例与 NAT 网关资源及其公共 IP 地址位于同一区域时，可以实现区域隔离的承诺。 用于实现区域隔离的模式是为每个可用性区域创建一个“局部区域性堆栈”。  此“局部区域性堆栈”由某个子网（假设该子网仅为同一区域提供服务）上的虚拟机实例、NAT 网关资源、公共 IP 地址和/或前缀资源构成。   控制平面操作和数据平面与指定的区域相符，且受限于该区域。 

不包含你的方案的区域发生故障预期不会影响 NAT。 由于区域隔离，来自同一区域中的虚拟机的出站流量将会失败。  

#### <a name="integrating-inbound-endpoints"></a>集成入站终结点

如果你的方案需要入站终结点，则你有两个选项可供选择：

| 选项 | 模式 | 示例 | 优点 | 缺点 |
|---|---|---|---|---|
| (1) | 使入站终结点与为出站终结点创建的相应局部区域性堆栈相一致。 | 创建包含局部区域性前端的标准负载均衡器。 | 入站与出站终结点采用相同的运行状况模型和故障模式。 操作更简单。 | 可能需要使用公用 DNS 名称来屏蔽每个区域的各个 IP 地址。 |
| (2) | 使用跨区域入站终结点覆盖局部区域性堆栈。 | 创建包含区域冗余前端的标准负载均衡器。 | 入站终结点使用单个 IP 地址。 | 入站与出站终结点采用不同的运行状况模型和故障模式。  操作更复杂。 |

>[!NOTE]
> 区域隔离的 NAT 网关要求 IP 地址与 NAT 网关的区域相匹配。 不允许使用其 IP 地址来自其他区域的 NAT 网关资源，也不允许使用没有区域的 NAT 网关资源。

#### <a name="cross-zone-outbound-scenarios-not-supported"></a>不支持跨区域出站方案

<p align="center">
  <img src="media/nat-overview/az-directions2.svg" alt="Figure depicts three zonal stacks, each of which contains a NAT gateway and a subnet, with the connections between to of the gateways and their subnets broken." width="425" title="与跨区域子网不兼容的虚拟网络 NAT">
</p>

图：  与跨区域子网不兼容的虚拟网络 NAT

如果虚拟机实例部署在同一子网中的多个区域内，则无法使用 NAT 网关资源实现区域承诺。   即使已将多个局部区域性 NAT 网关附加到子网，虚拟机实例也不知道要选择哪个 NAT 网关资源。

如果) 虚拟机实例的区域和区域 NAT 网关的区域未对齐，或 b) 区域 NAT 网关资源与区域虚拟机实例一起使用，则存在区域性承诺不。

尽管方案看起来正常运行，但从可用性区域的角度看，其运行状况模型和故障模式是未定义的。 请考虑改用局部区域性堆栈或全区域性堆栈。

>[!NOTE]
>NAT 网关资源的区域属性不可变。  使用所需的区域或局部区域首选项重新部署 NAT 网关资源。

>[!NOTE] 
>如果未指定局部区域，则 IP 地址本身不是局部区域冗余的。  如果 IP 地址不是在特定局部区域中创建的，则[标准负载均衡器的前端是局部区域冗余的](../load-balancer/load-balancer-standard-availability-zones.md#frontend)。  此规则不适用于 NAT。  仅支持区域隔离或局部区域隔离。

## <a name="performance"></a>性能

每个 NAT 网关资源最多可提供 50 Gbps 的吞吐量。 可以将部署拆分成多个子网，为每个子网或子网组分配一个 NAT 网关，以便进行横向扩展。

每个 NAT 网关可为 TCP 和 UDP 分别支持每个分配的出站 IP 地址的64000流。  请查看下面的有关源网络地址转换 (SNAT) 的部分来获取详细信息，并查看[故障排除文章](https://docs.microsoft.com/azure/virtual-network/troubleshoot-nat)来了解具体的问题解决指南。

## <a name="source-network-address-translation"></a>源网络地址转换

源网络地址转换 (SNAT) 将流的源重写为源自不同的 IP 地址。  NAT 网关资源使用一个通常称作端口地址转换 (PAT) 的 SNAT 变体。 PAT 可重写源地址和源端口。 使用 SNAT 时，专用地址的数量与其转换的公共地址之间没有固定的关系。  

### <a name="fundamentals"></a>基本

让我们看一个示例，其中通过四个流来解释基本概念。  NAT 网关正在使用公共 IP 地址资源65.52.1.1，VM 正在连接到65.52.0.1。

| 流向 | 源元组 | 目标元组 |
|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | 65.52.0.1:80 |

发生 PAT 后，这些流可能类似于：

| 流向 | 源元组 | 经过 SNAT 处理的源元组 | 目标元组 | 
|:---:|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | **65.52.1.1：1234** | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | **65.52.1.1:1235** | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | **65.52.1.1:1236** | 65.52.0.1:80 |

目标会将流的源视为 65.52.0.1 (SNAT 源元组) ，并显示分配的端口。  上表中所示的 PAT 也称为端口伪装 SNAT。  多个专用源在 IP 和端口后面伪装。  

#### <a name="source-snat-port-reuse"></a>源 (SNAT) 端口重用

NAT 网关找机会将源 (SNAT) 端口重复使用。  下面的示例演示了此概念，作为前面的流集的附加流。  示例中的 VM 是65.52.0.2 的流。

| 流向 | 源元组 | 目标元组 |
|:---:|:---:|:---:|
| 4 | 192.168.0.16:4285 | 65.52.0.2:80 |

NAT 网关可能会将流4转换为可用于其他目标的端口。  有关正确调整 IP 地址设置大小的详细讨论，请参阅 [缩放](https://docs.microsoft.com/azure/virtual-network/nat-gateway-resource#scaling) 。

| 流向 | 源元组 | 经过 SNAT 处理的源元组 | 目标元组 | 
|:---:|:---:|:---:|:---:|
| 4 | 192.168.0.16:4285 | 65.52.1.1：**1234** | 65.52.0.2:80 |

不要依赖于上述示例中分配的特定方式的源端口。  上面只是基本概念的演示图。

NAT 提供的 SNAT 在多个方面不同于[负载均衡器](../load-balancer/load-balancer-outbound-connections.md)。

### <a name="on-demand"></a>按需

NAT 为新的出站流量流提供按需 SNAT 端口。 配置了 NAT 的子网上的任何虚拟机将使用库存中所有可用的 SNAT 端口。 

<p align="center">
  <img src="media/nat-overview/lb-vnnat-chart.svg" alt="Figure depicts inventory of all available SNAT ports used by any virtual machine on subnets configured with N A T." width="550" title="虚拟网络 NAT 按需出站 SNAT">
</p>

*图：虚拟网络 NAT 按需出站 SNAT*

虚拟机的任何 IP 配置都可以按需创建出站流。  不需要进行预先分配和按实例的规划，包括根据每个实例的最差情况进行过度预配。  

<p align="center">
  <img src="media/nat-overview/exhaustion-threshold.svg" alt="Figure depicts inventory of all available SNAT ports used by any virtual machine on subnets configured with N A T with exhaustion threshold." width="550" title="耗尽方案的差异">
</p>

*图：耗尽方案的差异*

释放某个 SNAT 端口后，该端口可供配置了 NAT 的子网上的任何虚拟机使用。  按需分配允许子网上的动态和分散工作负荷按需使用 SNAT 端口。  只要有可用的 SNAT 端口库存，SNAT 流就会成功。 SNAT 端口热点则可受益于较大的端口库存。 不需要 SNAT 端口的虚拟机并非不使用这些端口。

### <a name="scaling"></a>扩展

缩放 NAT 功能主要用于管理共享的可用 SNAT 端口库存。 NAT 需有足够的 SNAT 端口库存，才能解决已附加到 NAT 网关资源的所有子网的预期高峰出站流。  可以使用公共 IP 地址资源和/或公共 IP 前缀资源来创建 SNAT 端口库存。  

>[!NOTE]
>如果你分配一个公共 IP 前缀资源，则会使用整个公共 IP 前缀。  不能先分配一个公共 IP 前缀资源，然后再将个别 IP 地址取出来分配给其他资源。  如果要将来自公共 IP 地址前缀的个别 IP 地址分配给多个资源，则你需要基于公共 IP 前缀资源创建个别公共 IP 地址，再根据需要分配这些地址，而不能分配公共 IP 前缀资源本身。

SNAT 将专用地址映射到一个或多个公共 IP 地址，并重写进程中的源地址和源端口。 NAT 网关资源将为所配置的每个公共 IP 地址使用 64,000 个端口（SNAT 端口）进行此转换。 NAT 网关资源可以扩展到 16 个 IP 地址和 100 万个 SNAT 端口。 如果提供了公共 IP 前缀资源，则前缀中的每个 IP 地址都会提供 SNAT 端口库存。 添加更多公共 IP 地址可以增加可用库存 SNAT 端口。 TCP 和 UDP 是独立的 SNAT 端口库存，与此无关。

NAT 网关资源找机会将源 (SNAT) 端口。 作为缩放用途的设计指南，你应假设每个流都需要新的 SNAT 端口，并缩放出站流量的可用 IP 地址总数。  应该仔细考虑要为其设计的规模，并相应地设置 IP 地址数量。

对于不同目标的 SNAT 端口，最有可能会被重复使用。 由于 SNAT 端口耗尽方法，流可能不会成功。  

有关示例，请参阅 [SNAT 基础知识](https://docs.microsoft.com/azure/virtual-network/nat-gateway-resource#source-network-address-translation) 。


### <a name="protocols"></a>协议

NAT 网关资源与 UDP 和 TCP 流的 IP 和 IP 传输标头交互，对应用层有效负载不可知。  不支持其他 IP 协议。

### <a name="timers"></a>计时器

>[!IMPORTANT]
>空闲计时器没有必要太长，太长可能会增加 SNAT 耗尽的可能性。 你指定的计时器时间越长，NAT 保持使用 SNAT 端口的时间也越长，直至最终达到空闲超时。 如果流已达到空闲超时，则它们最终会失败，并且会不必要地消耗 SNAT 端口库存。  原本在 2 小时后失败的流也会在达到 4 分钟默认超时后失败。 增大空闲超时是迫不得已才应使用的方法，应该慎用。 如果流永远不会进入空闲状态，则它不受空闲计时器的影响。

对于所有流，可将 TCP 空闲超时从 4 分钟（默认值）调整为 120 分钟（2小时）。  此外，对于流中的流量，还可以重置空闲计时器。  TCP Keepalive 是刷新长时间空闲连接和执行终结点活动状态检测的推荐模式。  TCP Keepalive 以重复 ACK 的形式显示给终结点，其开销较低，对应用层不可见。

以下计时器用于 SNAT 端口释放：

| Timer | 值 |
|---|---|
| TCP FIN | 60 秒 |
| TCP RST | 10 秒 |
| TCP 半开 | 30 秒 |

5 秒钟后，SNAT 端口即可供同一目标 IP 地址和目标端口重复使用。

>[!NOTE] 
>这些计时器设置随时可能更改。 提供这些值是为了帮助进行故障排除，暂时请不要依赖于特定的计时器。

## <a name="limitations"></a>限制

- NAT 与标准 SKU 公共 IP、公共 IP 前缀和负载均衡器资源兼容。   基本资源（例如基本负载均衡器）以及派生自这些资源的任何产品都与 NAT 不兼容。  必须将基本资源放在未配置 NAT 的子网中。
- 支持 IPv4 地址系列。  NAT 不会与 IPv6 地址系列交互。  NAT 不能部署在具有 IPv6 前缀的子网中。
- NAT 不能跨多个虚拟网络。

## <a name="suggestions"></a>建议

我们很想知道如何能够改进该服务。 缺少某个功能？ 请在 [UserVoice for NAT](https://aka.ms/natuservoice) 上针对我们接下来应打造什么功能提出建议。

## <a name="next-steps"></a>后续步骤

* 了解[虚拟网络 NAT](nat-overview.md)。
* 了解 [NAT 网关资源的指标和警报](nat-metrics.md)。
* 了解如何[排查 NAT 网关资源问题](troubleshoot-nat.md)。
* 有关验证 NAT 网关的教程
  - [Azure CLI](tutorial-create-validate-nat-gateway-cli.md)
  - [PowerShell](tutorial-create-validate-nat-gateway-powershell.md)
  - [门户](tutorial-create-validate-nat-gateway-portal.md)
* 有关部署 NAT 网关资源的快速入门
  - [Azure CLI](./quickstart-create-nat-gateway-cli.md)
  - [PowerShell](./quickstart-create-nat-gateway-powershell.md)
  - [门户](./quickstart-create-nat-gateway-portal.md)
  - [模板](./quickstart-create-nat-gateway-template.md)
* 了解 NAT 网关资源 API
  - [REST API](https://docs.microsoft.com/rest/api/virtualnetwork/natgateways)
  - [Azure CLI](https://docs.microsoft.com/cli/azure/network/nat/gateway)
  - [PowerShell](https://docs.microsoft.com/powershell/module/az.network/new-aznatgateway)
* 了解[可用性区域](../availability-zones/az-overview.md)。
* 了解[标准负载均衡器](../load-balancer/load-balancer-standard-overview.md)。
* 了解[可用性区域和标准负载均衡器](../load-balancer/load-balancer-standard-availability-zones.md)。
* [在 UserVoice 中告诉我们接下来想要为虚拟网络 NAT 开发什么功能](https://aka.ms/natuservoice)。
