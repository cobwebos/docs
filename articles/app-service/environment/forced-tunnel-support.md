---
title: "将 Azure 应用服务环境配置为采用强制隧道"
description: "在通过强制隧道传输出站流量时使 ASE 能够正常运行"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 384cf393-5c63-4ffb-9eb2-bfd990bc7af1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: ccompy
ms.openlocfilehash: cd89bd23074dec1de6fa0e8784d42a5c539938b1
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/11/2017
---
# <a name="configure-your-app-service-environment-with-forced-tunneling"></a>使用强制隧道配置应用服务环境

应用服务环境 (ASE) 是部署在客户 Azure 虚拟网络 (VNet) 中的 Azure 应用服务。 许多客户使用 VPN 或 ExpressRoute 连接将其 VNet 配置为本地网络的扩展。 由于公司政策或其他安全约束，他们会配置路由用于将所有出站流量发送到本地，然后将流量外发到 Internet。 更改 VNet 的路由，使来自 VNet 的出站流量能够通过 VPN 或 ExpressRoute 连接流向本地的过程称为强制隧道。  

强制隧道可能会给 ASE 造成问题。 ASE 具有许多的外部依赖项，此 [ASE 网络体系结构][network]文档已列举这些依赖项。 默认情况下，ASE 要求所有出站通信都通过 ASE 中预配的 VIP 进行。

路由对于强制隧道的定义及其用法至关重要。 在 Azure 虚拟网络中，路由是基于最长前缀匹配 (LPM) 实现的。  如果有多个路由的 LPM 匹配情况相同，则按以下顺序根据路由源来选择路由：

1. 用户定义路由
1. BGP 路由（当使用 ExpressRoute 时）
1. 系统路由

若要详细了解 VNet 中的路由，请参阅[用户定义的路由和 IP 转发][routes]。 

如果希望 ASE 在强制隧道 VNet 中运行，可采用两种选择：

1. 让 ASE 获得直接 Internet 访问权限
1. 更改 ASE 的出口终结点

## <a name="enable-your-ase-to-have-direct-internet-access"></a>让 ASE 获得直接 Internet 访问权限

在 VNet 中配置了 ExpressRoute 的情况下要使 ASE 正常工作，可以：

* 将 ExpressRoute 配置为播发 0.0.0.0/0。 默认情况下，它强制通过隧道传递所有本地出站流量。
* 创建 UDR。 将该 UDR 应用到包含 ASE 且地址前缀为 0.0.0.0/0、下一跃点类型为 Internet 的子网。

若执行了这两项更改，则不会强制由 ASE 子网发往 Internet 的流量通过 ExpressRoute，且 ASE 可正常工作。

> [!IMPORTANT]
> UDR 中定义的路由必须足够明确，以便优先于 ExpressRoute 配置所播发的任何路由。 上述示例使用了广泛的 0.0.0.0/0 地址范围。 因此很困意外地被使用更具体地址范围的路由播发重写。
>
> 若 ExpressRoute 配置交叉播发从公共对等路径到专用对等路径的路由，则这些配置不支持 ASE。 已配置公共对等互连的 ExpressRoute 配置将收到来自 Microsoft 的路由播发。 这些播发包含大量的 Microsoft Azure IP 地址范围。 如果这些地址范围在专用对等路径上交叉播发，则来自 ASE 子网的所有出站网络数据包均强制通过隧道发送至客户的本地网络基础结构。 目前，ASE 默认不支持此网络流。 一个解决方法是停止公共对等路径到专用对等路径的交叉播发路由。  另一种解决方法是使 ASE 能够在强制隧道配置中工作。

## <a name="change-the-egress-endpoint-for-your-ase"></a>更改 ASE 的出口终结点 ##

本部分介绍如何通过更改 ASE 所用的出口终结点，使 ASE 能够在强制隧道配置中运行。 如果来自 ASE 的出站流量通过强制隧道传输到本地网络，则需要允许流量源自除 ASE VIP 地址以外的 IP 地址。

ASE 不仅具有外部依赖项，而且还必须侦听用于管理 ASE 的入站流量。 ASE 必须能够响应此类流量，并且不能从另一个地址发回答复，否则会中断 TCP。  因此，更改 ASE 的出口终结点需要执行三个步骤。

1. 设置一个路由表，确保能够从同一 IP 地址发出入站管理流量
1. 添加用作 ASE 防火墙出口的 IP 地址
1. 设置来自 ASE 的、要通过隧道传输的出站流量的路由

![强制隧道网络流][1]

可以在 ASE 已启动并运行之后使用不同的出口地址配置 ASE，或者在部署 ASE 期间设置这些地址。  

### <a name="changing-the-egress-address-after-the-ase-is-operational"></a>在 ASE 运行后更改出口地址 ###
1. 获取要用作 ASE 出口 IP 的 IP 地址。 如果正在执行强制隧道，则这些地址是 NAT 或网关 IP。  如果想要通过 NVA 路由 ASE 出站流量，则出口地址是 NVA 的公共 IP。
2. 在 ASE 配置信息中设置出口地址。 转到 resource.azure.com 并导航到：Subscription/<subscription id>/resourceGroups/<ase resource group>/providers/Microsoft.Web/hostingEnvironments/<ase name>，然后可以看到描述 ASE 的 JSON 代码。  确保代码的顶部显示了 read/write。  单击“编辑”，滚动到底部，并将 userWhitelistedIpRanges 从  

       "userWhitelistedIpRanges": null 
      
  更改为如下所示的内容。 使用要设置为出口地址范围的地址。 

      "userWhitelistedIpRanges": ["11.22.33.44/32", "55.66.77.0/24"] 

  单击顶部的“PUT”。 这会在 ASE 中触发一个缩放操作并调整防火墙。
   
3. 创建或编辑一个路由表并填充规则，以允许访问映射到 ASE 位置的管理地址，或从中进行访问。  [应用服务环境管理地址][management]中列出了管理地址 

4. 使用路由表或 BGP 路由调整应用到 ASE 子网的路由。  

如果 ASE 在门户中无响应，则表示所做的更改有问题。  原因可能是出口地址列表不完整、流量丢失或流量被阻止。  

### <a name="create-a-new-ase-with-a-different-egress-address"></a>使用不同的出口地址创建新的 ASE  ###

如果 VNet 已配置为通过强制隧道传输所有流量，则需要执行一些额外的步骤来创建 ASE，使其能够成功启动。 这意味着，需要在创建 ASE 期间启用另一个出口终结点。  为此，需要使用一个指定了所允许出口地址的模板创建 ASE。

1. 获取要用作 ASE 出口地址的 IP 地址。
1. 预先创建 ASE 要使用的子网。 设置路由时需要此子网，另外，模板也需要此子网。  
1. 使用映射到 ASE 位置的管理 IP 创建一个路由表，并将其分配到 ASE
1. 遵循[使用模板创建 ASE][template] 中的指导，然后清理相应的模板
1. 编辑 azuredeploy.json 文件中的“resources”节。 包含 **userWhitelistedIpRanges** 行并在其中指定如下值：

       "userWhitelistedIpRanges":  ["11.22.33.44/32", "55.66.77.0/30"]

如果配置正确，则 ASE 应可正常启动。  


<!--IMAGES-->
[1]: ./media/forced-tunnel-support/forced-tunnel-flow.png

<!--Links-->
[management]: ./management-addresses.md
[network]: ./network-info.md
[routes]: ../../virtual-network/virtual-networks-udr-overview.md
[template]: ./create-from-template.md
