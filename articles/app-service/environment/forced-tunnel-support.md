---
title: "将 Azure 应用服务环境配置为采用强制隧道"
description: "在通过强制隧道传输出站流量时，启用应用服务环境来使其正常工作"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 384cf393-5c63-4ffb-9eb2-bfd990bc7af1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/10/2017
ms.author: ccompy
ms.custom: mvc
ms.openlocfilehash: 4caaf0df3f1dd4b2cb9b76283a6beed897531c1c
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2017
---
# <a name="configure-your-app-service-environment-with-forced-tunneling"></a>使用强制隧道配置应用服务环境

应用服务环境是指在 Azure 虚拟网络的实例中部署 Azure App Service。 许多客户使用 VPN 或 Azure ExpressRoute 连接将其虚拟网络配置为本地网络的扩展。 由于公司政策或其他安全约束，他们会配置路由用于将所有出站流量发送到本地，然后将流量外发到 Internet。 更改虚拟网络的路由，使来自虚拟网络的出站流量能够通过 VPN 或 ExpressRoute 连接流向本地，这一过程称为强制隧道。 

强制隧道会导致应用服务环境出现问题。 应用服务环境具有许多外部依赖项，请参见[应用服务环境网络体系结构][network]文档中的枚举。 默认情况下，应用服务环境要求所有出站通信都通过应用服务环境中预配的 VIP 进行。

强制隧道的定义及其用法都离不开路由。 在 Azure 虚拟网络中，路由是基于最长前缀匹配 (LPM) 实现的。 如果有多个路由的 LPM 匹配情况相同，则按以下顺序根据路由源来选择路由：

* 用户定义的路由 (UDR)
* BGP 路由（当使用 ExpressRoute 时）
* 系统路由

若要详细了解虚拟网络中的路由，请参阅[用户定义的路由和 IP 转发][routes]。 

如果希望在强制隧道虚拟网络中运行应用服务环境，有以下两种选择：

* 启用应用服务环境，获得直接访问 Internet 的权限。
* 更改应用服务环境的出口终结点。

## <a name="enable-your-app-service-environment-to-have-direct-internet-access"></a>启用应用服务环境，获得直接访问 Internet 的权限

当虚拟网络已配置 ExpressRoute 连接时，为使应用服务环境正常工作，可以：

* 将 ExpressRoute 配置为播发 0.0.0.0/0。 默认情况下，它强制通过隧道传递所有本地出站流量。
* 创建 UDR。 并使用地址前缀 0.0.0.0/0 和下一跃点类型 Internet，将其应用到包含应用服务环境的子网。

如果执行这两项更改，则不会强制由应用服务环境子网发往 Internet 的流量通过 ExpressRoute 连接，且应用服务环境可正常工作。

> [!IMPORTANT]
> UDR 中定义的路由必须足够明确，以便优先于 ExpressRoute 配置所播发的任何路由。 上述示例使用了广泛的 0.0.0.0/0 地址范围。 因此很困意外地被使用更具体地址范围的路由播发重写。
>
> 交叉播发从公共对等路径到专用对等路径的路由的 ExpressRoute 配置不支持应用服务环境。 已配置公共对等互连的 ExpressRoute 配置将收到来自 Microsoft 的路由播发。 这些播发包含大量的 Microsoft Azure IP 地址范围。 如果这些地址范围在专用对等路径上交叉播发，则来自应用服务环境子网的所有出站网络数据包均强制通过隧道发送至客户的本地网络基础结构。 默认情况下，应用服务环境目前不支持此网络流。 一个解决方法是停止公共对等路径到专用对等路径的交叉播发路由。 另一种解决方法是使应用服务环境能够在强制隧道配置中正常工作。

## <a name="change-the-egress-endpoint-for-your-app-service-environment"></a>更改应用服务环境的出口终结点 ##

本部分介绍如何通过更改应用服务环境所用的出口终结点，使其能够在强制隧道配置中正常工作。 如果来自应用服务环境的出站流量通过强制隧道传输到本地网络，则需要允许流量源自除应用服务环境 VIP 地址以外的 IP 地址。

应用服务环境不仅具有外部依赖项，还必须侦听入站流量并对其作出响应。 回复无法从其他地址发回，因为这样会中断 TCP。 更改应用服务环境的出口终结点需要执行三个步骤：

1. 设置一个路由表，确保能够从同一 IP 地址发出入站管理流量。

2. 添加用作应用服务环境防火墙出口的 IP 地址。

3. 设置应用服务环境的出站流量路由，使其通过隧道传输。

   ![强制隧道网络流][1]

在应用服务环境启动并运行后，可使用不同的出口地址对其进行配置，也可在应用服务环境部署期间对其进行设置。

### <a name="change-the-egress-address-after-the-app-service-environment-is-operational"></a>在应用服务环境运行之后，更改出口地址 ###
1. 获取要用作应用服务环境出口 IP 的 IP 地址。 如果正在执行强制隧道，则这些地址来自 NAT 或网关 IP。 若要通过 NVA 路由应用服务环境出站流量，则出口地址为 NVA 的公共 IP。

2. 在应用服务环境配置信息中设置出口地址。 转到 resource.azure.com，再转到 Subscription/<subscription id>/resourceGroups/<ase resource group>/providers/Microsoft.Web/hostingEnvironments/<ase name>。 然后即可看到描述应用服务环境的 JSON 代码。 确保代码的顶部显示“读/写”。 选择“编辑”。 向下滚动到底部，并将“userWhitelistedIpRanges”值从“null”更改为类似于以下内容的值。 使用要设置为出口地址范围的地址。 

        "userWhitelistedIpRanges": ["11.22.33.44/32", "55.66.77.0/24"] 

   选择顶部的“PUT”。 此选项会触发应用服务环境的缩放操作，并对防火墙进行调整。
 
3. 创建或编辑路由表并填充规则，以允许访问映射到应用服务环境位置的管理地址，或从中进行访问。 若要查找管理地址，请参阅[应用服务环境管理地址][management]。

4. 使用路由表或 BGP 路由调整应用到应用服务环境子网的路由。 

如果应用服务环境在门户中无响应，则表示所做的更改存在问题。 问题可能是出口地址列表不完整、流量丢失或流量被阻止。 

### <a name="create-a-new-app-service-environment-with-a-different-egress-address"></a>使用不同的出口地址创建新的应用服务环境 ###

如果虚拟网络已配置为通过强制隧道传输所有流量，则需要执行一些额外步骤来创建应用服务环境，使其能够成功启动。 需要在创建应用服务环境期间启用另一个出口终结点。 为此，需要使用已指定允许的出口地址的模板创建应用服务环境。

1. 获取要用作应用服务环境出口地址的 IP 地址。

2. 预先创建应用服务环境要使用的子网。 设置路由时需要此子网；另外，模板也需要此子网。

3. 使用映射到应用服务环境位置的管理 IP 创建路由表。 将其分配到应用服务环境。

4. 请按照[使用模板创建应用服务环境][template]中的说明进行操作。 拉取相应的模板。

5. 编辑 azuredeploy.json 文件中的“资源”部分。 包含 userWhitelistedIpRanges 行并在其中指定如下值：

       "userWhitelistedIpRanges":  ["11.22.33.44/32", "55.66.77.0/30"]

如果此部分已正确配置，应用服务环境应正常启动。 


<!--IMAGES-->
[1]: ./media/forced-tunnel-support/forced-tunnel-flow.png

<!--Links-->
[management]: ./management-addresses.md
[network]: ./network-info.md
[routes]: ../../virtual-network/virtual-networks-udr-overview.md
[template]: ./create-from-template.md
