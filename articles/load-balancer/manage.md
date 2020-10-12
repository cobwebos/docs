---
title: Azure 负载均衡器门户设置
description: 开始了解 Azure 负载均衡器门户设置
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/8/2020
ms.author: allensu
ms.openlocfilehash: e1080aea12e70f4312fbee07b063d5a5cfbd1201
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89596254"
---
# <a name="azure-load-balancer-portal-settings"></a>Azure 负载均衡器门户设置

创建 Azure 负载均衡器时，可以通过本文中的信息详细了解各个设置以及适合自己的配置。

## <a name="create-load-balancer"></a>创建负载均衡器

Azure 负载均衡器是一个网络负载均衡器，用于在后端池中的 VM 实例之间分配流量。

### <a name="basics"></a>基础

在“创建负载均衡器”门户页的“基本信息”选项卡中，可以看到以下信息：



| 设置 |  详细信息 |
| ---------- | ---------- |
| 订阅  | 选择订阅。 此选择是你要在其中部署负载平衡器的订阅。 |
| 资源组 | 选择“新建”并在文本框中键入资源组的名称。 如果有现有的已创建资源组，请选择它。 |
| 名称 | 此设置是 Azure 负载均衡器的名称。 |
| 区域 | 选择要在其中部署负载均衡器的 Azure 区域。 |
| 类型 | 负载均衡器有两种类型： </br> 内部（专用） </br> 公共（外部）。</br> 内部负载均衡器 (ILB) 通过专用 IP 地址将流量路由到后端池成员。</br> 公共负载均衡器通过 Internet 将来自客户端的请求定向到后端池。</br> 详细了解[负载均衡器类型](components.md#frontend-ip-configuration-)。|
| SKU  | 选择“标准”。 </br> 负载均衡器有两种 SKU：“基本”和“标准” 。 </br> 基本 SKU 的功能有限。 </br> 对于生产型工作负载，建议使用标准 SKU。 </br> 详细了解 [SKU](skus.md)。 |

如果选择“公共”作为类型，则会看到以下信息：

| 设置 |  详细信息 |
| ---------- | ---------- |
| 公共 IP 地址 | 选择“新建”可创建公共负载均衡器的公共 IP 地址。 </br> 如果有现有的公共 IP，请选择“使用现有项”。  |
| 公共 IP 地址名称 | 公共 IP 地址的名称。|
| 公用 IP 地址 SKU | 公共 IP 地址有两个 SKU：“基本”和“标准” 。 </br> 基本不支持区域复原和区域性特性。 </br> 对于生产型工作负载，建议使用标准 SKU。 </br> 负载均衡器和公共 IP 地址 SKU 必须匹配。 |
| 分配 | 对于标准 SKU，会自动选择“静态”。 </br> 基本公共 IP 有两种类型：“动态”和“静态”。 </br> 动态公共 IP 地址在创建之前不会进行分配。 </br> 如果删除了资源，则可能会丢失 IP。 </br> 建议使用静态 IP 地址。 |
| 可用性区域 | 选择“区域冗余”以创建弹性负载均衡器。 </br> 若要创建区域负载均衡器，请从 **1**、 **2**或 **3**选择特定的区域。 </br> 标准负载均衡器和公共 Ip 支持区域。 </br> 了解有关 [负载均衡器和可用性区域](load-balancer-standard-availability-zones.md)的详细信息。 </br> 你将看不到 "基本" 区域选择。 基本负载均衡器不支持区域。 |
| 添加一个公共 IPv6 地址 | 负载均衡器支持将 **IPv6** 地址用于前端。 </br> 详细了解[负载平衡器和 IPv6](load-balancer-ipv6-overview.md)
| 路由首选项 | 选择 " **Microsoft 网络**"。 </br> Microsoft 网络表示通过 Microsoft 全球网络路由流量。 </br> Internet 表示通过 internet 服务提供商网络路由流量。 </br> 了解[路由首选项](../virtual-network/routing-preference-overview.md)的详细信息|

:::image type="content" source="./media/manage/create-public-load-balancer-basics.png" alt-text="创建公共负载均衡器。" border="true":::

如果在“类型”中选择“内部”，则会看到以下信息：

| 设置 |  详细信息 |
| ---------- | ---------- |
| 虚拟网络 | 你希望自己的内部负载均衡器能够成为其一部分的虚拟网络。 </br> 为内部负载均衡器选择的专用前端 IP 地址将来自此虚拟网络。 |
| IP 地址分配 | 选项为“静态”或“动态”。 </br> “静态”可确保 IP 不会更改。 动态 IP 地址可能会更改。 |
| 可用性区域 | 你的选项为： </br> **区域冗余** </br> **区域 1** </br> **区域 2** </br> **区域 3** </br> 若要创建高度可用且可复原可用性区域故障的负载均衡器，请选择 **区域冗余** IP。 |

:::image type="content" source="./media/manage/create-internal-load-balancer-basics.png" alt-text="创建公共负载均衡器。" border="true":::

## <a name="frontend-ip-configuration"></a>前端 IP 配置

Azure 负载均衡器的 IP 地址。 这是客户端的联系点。 

可以有一个或多个前端 IP 配置。 如果已完成上述的基本信息部分，则已为负载均衡器创建了前端。 

如果要将前端 IP 配置添加到负载均衡器，请转到 Azure 门户中的负载均衡器，选择“前端 IP 配置”，然后选择“+添加”。 

| 设置 |  详细信息 |
| ---------- | ---------- |
| 名称 | 前端 IP 配置的名称。 |
| IP 版本 | 希望前端使用的 IP 地址版本。 </br> 负载均衡器支持 IPv4 和 IPv6 前端 IP 配置。 |
| IP 类型 | IP 类型决定了单个 IP 地址是与前端关联，还是与使用某个 IP 前缀的 IP 地址范围关联。 </br> 在需要重复连接到同一终结点时，可以使用[公共 IP 前缀](../virtual-network/public-ip-address-prefix.md)。 前缀可确保提供足够的端口来帮助解决 SNAT 端口问题。 |
| 公共 IP 地址（或前缀，前提是选择了上述前缀） | 为负载均衡器前端选择或创建新的公共 IP（或前缀）。 |

:::image type="content" source="./media/manage/frontend.png" alt-text="创建公共负载均衡器。" border="true":::

## <a name="backend-pools"></a>后端池

后端地址池包含后端池中虚拟网络接口的 IP 地址。 

如果要将后端池添加到负载均衡器，请转到 Azure 门户中的负载均衡器，选择“后端池”，然后选择“+添加”。 

| 设置 | 详细信息 |
| ---------- |  ---------- |
| 名称 | 后端池的名称。 |
| 虚拟网络 | 后端实例所在的虚拟网络。 |
| IP 版本 | 选项为“IPv4”或“IPv6”。 |

可以将虚拟机或虚拟机规模集添加到 Azure 负载均衡器的后端池。 首先创建虚拟机或虚拟机规模集。 接下来，在门户中将其添加到负载均衡器。

:::image type="content" source="./media/manage/backend.png" alt-text="创建公共负载均衡器。" border="true":::

## <a name="health-probes"></a>运行状况探测

运行状况探测用于监视后端 VM 或实例的状态。 运行状况探测状态决定了根据运行状况检查将新连接发送到实例的时间。 

如果要将运行状况探测添加到负载均衡器，请在 Azure 门户中转到你的负载均衡器，选择“运行状况探测”，然后选择“+添加”。 

| 设置 | 详细信息 |
| ---------- | ---------- |
| 名称 | 运行状况探测的名称。 |
| 协议 | 你选择的协议所确定的检查类型用于确定后端实例是否正常。 </br> 你的选项为： </br> **TCP** </br> **HTTPS** </br> **HTTP** </br> 确保使用正确的协议。 此选择将取决于应用程序的性质。 </br> 运行状况探测的配置和探测响应确定哪些后端池实例将接收新流。 </br> 可以使用运行状况探测来检测后端终结点上应用程序的故障。 </br> 详细了解[运行状况探测](load-balancer-custom-probe-overview.md)。 |
| 端口 | 运行状况探测的目标端口。 </br> 此设置是后端实例上的端口，可供运行状况探测用于确定实例的运行状况。 |
| 时间间隔 | 两次探测尝试之间的秒数。 </br> 该间隔将确定运行状况探测尝试访问后端实例的频率。 </br> 如果选择 5，则会在 5 秒后进行第二次探测尝试，依此类推。 |
| 不正常阈值 | 在将 VM 视为不正常之前必须发生的连续探测失败次数。</br> 如果选择 2，则在连续两次失败后，不会将新流设置为此后端实例。 |

:::image type="content" source="./media/manage/health-probe.png" alt-text="创建公共负载均衡器。" border="true":::

## <a name="load-balancing-rules"></a>负载均衡规则

定义将传入的流量分配至后端池中所有实例的方式。 负载均衡规则将给定的前端 IP 配置和端口映射到多个后端 IP 地址和端口。

如果要将负载均衡器规则添加到负载均衡器，请在 Azure 门户中转到你的负载均衡器，选择“负载均衡规则”，然后选择“+添加”。 
    
| 设置 | 详细信息 |
| ---------- | ---------- |
| 名称 | 负载均衡器规则的名称。 |
| IP 版本 | 选项为“IPv4”或“IPv6”。  |
| 前端 IP 地址 | 选择前端 IP 地址。 </br> 要将负载均衡器规则与之关联的负载均衡器的前端 IP 地址。|
| 协议 | Azure 负载均衡器是第 4 层网络负载均衡器。 </br> 选项包括：TCP 或 UDP。 |
| 端口 | 此设置是与你希望根据此负载均衡规则分配流量的前端 IP 关联的端口。 |
| 后端端口 | 此设置是你想要负载均衡器将流量发送到的后端池中的实例上的端口。 此设置可以与前端端口相同，也可以与前端端口不同（如果你需要为你的应用程序提供的灵活性）。 |
| 后端池 | 你希望此负载均衡器规则应用到的后端池。 |
| 运行状况探测 | 你已创建的运行状况探测，用于检查后端池中实例的状态。 </br> 只有正常的实例才会接收新流量。 |
| 会话暂留 |  你的选项为： </br> **无** </br> 客户端 IP </br> 客户端 IP 和协议</br> </br> 保持从客户端到后端池中同一虚拟机的流量。 将在会话的持续时间内保持此流量。 </br> “无”指定任何虚拟机可以处理来自同一客户端的后续请求。 </br> “客户端 IP”指定来自同一客户端 IP 地址的后续请求将由同一虚拟机处理。 </br> “客户端 IP 和协议”确保来自同一客户端 IP 地址和协议的连续请求将由同一虚拟机处理。 </br> 详细了解[分配模式](load-balancer-distribution-mode.md)。 |
| 空闲超时（分钟） | 保持 TCP 或 HTTP 连接打开，而不依赖于客户端发送“保持活动状态”消息 |  
| TCP 重置 | 负载均衡器可以发送 TCP 重置，这有助于创建可预测性更高的应用程序行为（在连接空闲时发生）。 </br> 详细了解 [TCP 重置](load-balancer-tcp-reset.md)|
| 浮动 IP | “浮动 IP”是 Azure 的术语，表示所谓的“直接服务器返回 (DSR)”的一部分。 </br> DSR 由两个部分组成： <br> 1.流拓扑 </br> 2.平台级别的 IP 地址映射方案。 </br></br> Azure 负载均衡器始终在 DSR 流拓扑中运行，不管是否启用了浮动 IP。 </br> 此操作意味着，流的出站部分始终会正确重写为直接流回到源。 </br> 如果不使用浮动 IP，则 Azure 会公开传统的负载均衡 IP 地址映射方案，即 VM 实例的 IP。 </br> 启用浮动 IP 会更改向负载均衡器前端 IP 进行的 IP 地址映射，以实现更大的灵活性。 </br> 有关详细信息，请参阅 [Azure 负载均衡器的多个前端](load-balancer-multivip-overview.md)。|
| 创建隐式出站规则 | 请选择“否”。 </br> 默认设置：disableOutboundSnat = false  </br> 在这种情况下，出站通过同一前端 IP 发生。 </br></br> disableOutboundSnat = true </br>在这种情况下，出站规则是出站所需的。 |

:::image type="content" source="./media/manage/load-balancing-rule.png" alt-text="创建公共负载均衡器。" border="true":::

## <a name="inbound-nat-rules"></a>入站 NAT 规则

入站 NAT 规则将转发发送到前端 IP 地址和端口组合的传入流量。 

该流量将被转发到后端池中的特定虚拟机或实例。 可以通过与负载均衡相同的基于哈希的分配来实现此端口转发。

如果你的方案需要远程桌面协议 (RDP) 或安全外壳 (SSH) 会话对后端池中的 VM 实例进行分隔。 可将多个内部终结点映射到同一前端 IP 地址上的端口。 

可以使用前端 IP 地址来远程管理 VM，无需额外的跳转盒。

如果要将入站 NAT 规则添加到负载均衡器，请在 Azure 门户中转到你的负载均衡器，选择“入站 NAT 规则”，然后选择“+添加”。 

| 设置 | 详细信息 |
| ---------- | ---------- |
| 名称 | 入站 NAT 规则的名称 |
| 前端 IP 地址 | 选择前端 IP 地址。 </br> 要将入站 NAT 规则与之关联的负载均衡器的前端 IP 地址。 |
| IP 版本 | 选项为“IPv4”和“IPv6”。 |
| 服务 | 你将在 Azure 负载均衡器上运行的服务的类型。 </br> 此处的选择将相应地更新端口信息。 |
| 协议 | Azure 负载均衡器是第 4 层网络负载均衡器。 </br> 选项包括：TCP 或 UDP。 |
| 空闲超时（分钟） | 保持 TCP 或 HTTP 连接打开，而不依赖于客户端发送“保持活动状态”消息。 |
| TCP 重置 | 负载均衡器可以发送 TCP 重置，这有助于创建可预测性更高的应用程序行为（在连接空闲时发生）。 </br> 详细了解 [TCP 重置](load-balancer-tcp-reset.md) |
| 端口 | 此设置是与你希望根据此入站 NAT 规则分配流量的前端 IP 关联的端口。 |
| 目标虚拟机 | 你希望将此规则与之关联的后端池的虚拟机部分。 |
| 端口映射 | 此设置可以是默认设置，也可以是自定义设置，具体取决于应用程序首选项。 |

:::image type="content" source="./media/manage/inbound-nat-rule.png" alt-text="创建公共负载均衡器。" border="true":::

## <a name="outbound-rules"></a>出站规则

负载均衡器出站规则为后端池中的 VM 配置出站 SNAT。

如果要将出站规则添加到负载均衡器，请在 Azure 门户中转到你的负载均衡器，选择“出站规则”，然后选择“+添加”。 

| 设置 | 详细信息 |
| ------- | ------ |
| 名称 | 出站规则的名称。 |
| 前端 IP 地址 | 选择前端 IP 地址。 </br> 要将出站规则与之关联的负载均衡器的前端 IP 地址。 |
| 协议 | Azure 负载均衡器是第 4 层网络负载均衡器。 </br> 选项包括：“所有”、“TCP”或“UDP”。 |
| 空闲超时（分钟） | 保持 TCP 或 HTTP 连接打开，而不依赖于客户端发送“保持活动状态”消息。 |
| TCP 重置 | 负载均衡器可以发送 TCP 重置，这有助于创建可预测性更高的应用程序行为（在连接空闲时发生）。 </br> 详细了解 [TCP 重置](load-balancer-tcp-reset.md) |
| 后端池 | 你希望将此出站规则应用到的后端池。 |

### <a name="port-allocation"></a>端口分配

| 设置 | 详细信息 |
| ------- | ------ |
| 端口分配 | 建议选择“手动选择出站端口数”。|

### <a name="outbound-ports"></a>出站端口

| 设置 | 详细信息 |
| ------- | ------ |
| 选择依据 | 选择“每个实例的端口数” |
| 每个实例的端口数 | 输入 10,000。 |

:::image type="content" source="./media/manage/outbound-rule.png" alt-text="创建公共负载均衡器。" border="true":::

## <a name="next-steps"></a>后续步骤

本文介绍了 Azure 门户中 Azure 负载均衡器的不同术语和设置。

* [了解](./load-balancer-overview.md)有关 Azure 负载均衡器的详细信息。
* Azure 负载均衡器[常见问题解答](./load-balancer-faqs.md)。