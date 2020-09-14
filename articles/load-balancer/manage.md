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
ms.sourcegitcommit: 1b320bc7863707a07e98644fbaed9faa0108da97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89596254"
---
# <a name="azure-load-balancer-portal-settings"></a>Azure 负载均衡器门户设置

当你创建 Azure 负载均衡器时，本文中的信息将帮助你了解有关各个设置以及适合你的配置的详细信息。

## <a name="create-load-balancer"></a>创建负载均衡器

Azure 负载均衡器是一个网络负载均衡器，用于在后端池中的 VM 实例之间分配流量。

### <a name="basics"></a>基础知识

在 "创建负载均衡器门户" 页的 " **基本** 信息" 选项卡中，你将看到以下信息：



| 设置 |  详细信息 |
| ---------- | ---------- |
| 订阅  | 选择订阅。 此选择是要在其中部署负载平衡器的订阅。 |
| 资源组 | 选择 " **新建** "，然后在文本框中键入资源组的名称。 如果已创建现有资源组，请选择它。 |
| 名称 | 此设置是 Azure 负载均衡器的名称。 |
| 区域 | 选择要在其中部署负载均衡器的 Azure 区域。 |
| 类型 | 负载均衡器有两种类型： </br> **内部 (专用) ** </br> **公用 (外部) **。</br> 内部负载均衡器 (ILB) 通过专用 IP 地址将流量路由到后端池成员。</br> 公共负载均衡器将来自客户端的请求定向到后端池。</br> 了解有关 [负载均衡器类型](components.md#frontend-ip-configuration-)的详细信息。|
| SKU  | 选择“标准”。 </br> 负载均衡器有两个 Sku： **基本** 和 **标准**。 </br> 基本功能有限。 </br> 建议为生产工作负荷使用**Standard** 。 </br> 了解有关 [sku](skus.md)的详细信息。 |

如果选择 " **公共** " 作为类型，将看到以下信息：

| 设置 |  详细信息 |
| ---------- | ---------- |
| 公共 IP 地址 | 选择 " **新建** " 以创建公共负载均衡器的公共 IP 地址。 </br> 如果有现有的公共 IP，请选择 " **使用现有**的"。  |
| 公共 IP 地址名称 | 公共 IP 地址的名称。|
| 公用 IP 地址 SKU | 公共 IP 地址有两个 Sku： **基本** 和 **标准**。 </br> 基本不支持区域复原和区域性特性。 </br> 建议为生产工作负荷使用**Standard** 。 </br> 负载均衡器和公共 IP 地址 Sku **必须匹配**。 |
| 分配 | 自动为标准选择 "**静态**"。 </br> 基本公共 Ip 有两种类型： **动态** 和 **静态**。 </br> 在创建之前，不会分配动态公共 IP 地址。 </br> 如果删除了资源，则可能会丢失 Ip。 </br> 建议使用静态 IP 地址。 |
| 可用性区域 | 选择“区域冗余”以创建弹性负载均衡器。 </br> 若要创建区域负载均衡器，请从 **1**、 **2**或 **3**选择特定的区域。 </br> 标准负载均衡器和公共 Ip 支持区域。 </br> 了解有关 [负载均衡器和可用性区域](load-balancer-standard-availability-zones.md)的详细信息。 </br> 你将看不到 "基本" 区域选择。 基本负载均衡器不支持区域。 |
| 添加一个公共 IPv6 地址 | 负载均衡器支持用于前端的 **IPv6** 地址。 </br> 了解有关[负载均衡器和 IPv6 的](load-balancer-ipv6-overview.md)详细信息
| 路由首选项 | 选择 " **Microsoft 网络**"。 </br> Microsoft 网络表示通过 Microsoft 全球网络路由流量。 </br> Internet 表示通过 internet 服务提供商网络路由流量。 </br> 了解[路由首选项](../virtual-network/routing-preference-overview.md)的详细信息|

:::image type="content" source="./media/manage/create-public-load-balancer-basics.png" alt-text="创建负载均衡器公共。" border="true":::

如果在 "类型" 中选择 " **内部** "，则会看到以下信息：

| 设置 |  详细信息 |
| ---------- | ---------- |
| 虚拟网络 | 要作为其一部分的内部负载均衡器的虚拟网络。 </br> 为内部负载均衡器选择的专用前端 IP 地址将来自此虚拟网络。 |
| IP 地址分配 | 选项为 **静态** 或 **动态**。 </br> 静态确保 IP 不会更改。 动态 IP 可能会更改。 |
| 可用性区域 | 你的选项为： </br> **区域冗余** </br> **区域 1** </br> **区域 2** </br> **区域 3** </br> 若要创建高度可用且可复原可用性区域故障的负载均衡器，请选择 **区域冗余** IP。 |

:::image type="content" source="./media/manage/create-internal-load-balancer-basics.png" alt-text="内部创建负载均衡器。" border="true":::

## <a name="frontend-ip-configuration"></a>前端 IP 配置

Azure 负载均衡器的 IP 地址。 这是客户端的联系点。 

可以有一个或多个前端 IP 配置。 如果已完成上述 "基本信息" 部分，则已为负载均衡器创建前端。 

如果要将前端 IP 配置添加到负载均衡器，请在 Azure 门户中，选择 " **前端 ip 配置**"，然后选择 " **+ 添加**"。

| 设置 |  详细信息 |
| ---------- | ---------- |
| 名称 | 前端 IP 配置的名称。 |
| IP 版本 | 希望前端使用的 IP 地址版本。 </br> 负载均衡器支持 IPv4 和 IPv6 前端 IP 配置。 |
| IP 类型 | IP 类型确定单个 IP 地址是与前端关联还是使用 IP 前缀来确定 IP 地址范围。 </br> 当你需要重复连接到同一终结点时， [公共 IP 前缀](../virtual-network/public-ip-address-prefix.md) 可帮助。 前缀可确保提供足够的端口来协助 SNAT 端口问题。 |
| 如果选择上述前缀，则公共 IP 地址 (或前缀)  | 为负载均衡器前端选择或创建新的公共 IP (或前缀) 。 |

:::image type="content" source="./media/manage/frontend.png" alt-text="创建前端 ip 配置 页。" border="true":::

## <a name="backend-pools"></a>后端池

后端地址池包含后端池中虚拟网络接口的 IP 地址。 

如果要将后端池添加到负载均衡器，请在 Azure 门户中，选择 " **后端池**"，然后选择 " **+ 添加**"。

| 设置 | 详细信息 |
| ---------- |  ---------- |
| 名称 | 后端池的名称。 |
| 虚拟网络 | 后端实例为的虚拟网络。 |
| IP 版本 | 选项为 **IPv4** 或 **IPv6**。 |

可以将虚拟机或虚拟机规模集添加到 Azure 负载均衡器的后端池。 首先创建虚拟机或虚拟机规模集。 接下来，将其添加到门户中的负载均衡器。

:::image type="content" source="./media/manage/backend.png" alt-text="创建后端池页面。" border="true":::

## <a name="health-probes"></a>运行状况探测

运行状况探测用于监视后端 Vm 或实例的状态。 运行状况探测状态决定了基于运行状况检查将新连接发送到实例的时间。 

若要将运行状况探测添加到负载均衡器，请在 Azure 门户中，选择 " **运行状况探测**"，然后选择 " **+ 添加**"。

| 设置 | 详细信息 |
| ---------- | ---------- |
| 名称 | 运行状况探测的名称。 |
| 协议 | 选择的协议确定用于确定后端实例)  (的检查的类型是否正常。 </br> 你的选项为： </br> **TCP** </br> **HTTPS** </br> **HTTP** </br> 确保使用正确的协议。 此选择将取决于你的应用程序的特性。 </br> 运行状况探测和探测响应的配置确定将接收新流的后端池实例。 </br> 可以使用运行状况探测来检测后端终结点上应用程序的故障。 </br> 了解有关 [运行状况探测](load-balancer-custom-probe-overview.md)的详细信息。 |
| Port | 运行状况探测的目标端口。 </br> 此设置是运行状况探测用于确定实例运行状况的后端实例上的端口。 |
| 时间间隔 | 探测尝试之间的秒数。 </br> 间隔将确定运行状况探测尝试到达后端实例的频率。 </br> 如果选择5，则第二次探测尝试将在5秒后执行。 |
| 不正常阈值 | 虚拟机被视为不正常之前必须发生的连续探测失败次数。</br> 如果选择 "2"，则在连续两次失败后，不会将新流设置为此后端实例。 |

:::image type="content" source="./media/manage/health-probe.png" alt-text="添加运行状况探测。" border="true":::

## <a name="load-balancing-rules"></a>负载均衡规则

定义如何将传入流量分配到后端池中的所有实例。 负载均衡规则将给定的前端 IP 配置和端口映射到多个后端 IP 地址和端口。

如果要将负载均衡器规则添加到负载均衡器，请在 Azure 门户中找到负载均衡器，选择 " **负载均衡规则**"，然后选择 " **+ 添加**"。
    
| 设置 | 详细信息 |
| ---------- | ---------- |
| 名称 | 负载均衡器规则的名称。 |
| IP 版本 | 选项为 **IPv4** 或 **IPv6**。  |
| 前端 IP 地址 | 选择前端 IP 地址。 </br> 要关联负载均衡器规则的负载均衡器的前端 IP 地址。|
| 协议 | Azure 负载均衡器是第4层网络负载均衡器。 </br> 选项有： **TCP** 或 **UDP**。 |
| Port | 此设置是与要根据此负载均衡规则分发流量的前端 IP 关联的端口。 |
| 后端端口 | 此设置是要将流量发送到的后端池中的实例上的端口。 如果需要应用程序的灵活性，此设置可以与前端端口相同或不同。 |
| 后端池 | 希望应用此负载均衡器规则的后端池。 |
| 运行状况探测 | 创建的运行状况探测，用于检查后端池中的实例的状态。 </br> 只有正常运行的实例才会接收新的流量。 |
| 会话暂留 |  你的选项为： </br> **无** </br> **客户端 IP** </br> **客户端 IP 和协议**</br> </br> 在后端池中维护从客户端到相同虚拟机的流量。 此流量将在会话期间保持。 </br> **无** 指定任何虚拟机可能处理来自同一客户端的后续请求。 </br> **客户端 IP** 指定同一虚拟机将处理来自同一客户端 ip 地址的后续请求。 </br> **客户端 ip 和协议** 确保来自同一客户端 ip 地址和协议的连续请求将由同一虚拟机处理。 </br> 详细了解 [分布模式](load-balancer-distribution-mode.md)。 |
| 空闲超时（分钟） | 保持 **TCP** 或 **HTTP** 连接打开而不依赖于客户端发送 keep-alive 消息 |  
| TCP 重置 | 负载均衡器可以发送 **TCP 重置** ，以帮助在连接空闲时创建更可预测的应用程序行为。 </br> 了解有关[TCP 重置](load-balancer-tcp-reset.md)的详细信息|
| 浮动 IP | 浮动 IP 是 Azure 的术语，适用于称为 **直接服务器返回 (DSR) **的部分。 </br> DSR 由两个部分组成： <br> 1. 流拓扑 </br> 2. 平台级别的 IP 地址映射方案。 </br></br> Azure 负载均衡器始终在 DSR 流拓扑中运行，无论是否已启用浮动 IP。 </br> 此操作表示流的出站部分始终正确重写，以直接流动回源。 </br> 如果没有浮动 IP，Azure 将公开传统的负载均衡 IP 地址映射方案、VM 实例的 IP。 </br> 启用浮动 IP 会将 IP 地址映射更改为负载均衡器的前端 IP，以实现更大的灵活性。 </br> 有关详细信息，请参阅 [Azure 负载均衡器的多个前端](load-balancer-multivip-overview.md)。|
| 创建隐式出站规则 | 请选择“否”。 </br> 默认值： **disableOutboundSnat = false**  </br> 在这种情况下，通过相同的前端 IP 发生出站。 </br></br> **disableOutboundSnat = true** </br>在这种情况下，出站规则是必需的。 |

:::image type="content" source="./media/manage/load-balancing-rule.png" alt-text="添加负载均衡规则。" border="true":::

## <a name="inbound-nat-rules"></a>入站 NAT 规则

入站 NAT 规则将发送的传入流量转发到前端 IP 地址和端口组合。 

该流量将被转发到后端池中的特定虚拟机或实例。 可以通过与负载均衡相同的基于哈希的分配来实现此端口转发。

如果方案需要远程桌面协议 (RDP) 或安全外壳 (SSH) 会话以将后端池中的 VM 实例隔离开来。 可以将多个内部终结点映射到相同前端 IP 地址上的端口。 

前端 IP 地址可用于远程管理 Vm，而无需额外的跳转框。

如果要将入站 nat 规则添加到负载均衡器，请在 Azure 门户中，选择 " **入站 nat 规则**"，然后选择 " **+ 添加**"。

| 设置 | 详细信息 |
| ---------- | ---------- |
| 名称 | 入站 NAT 规则的名称 |
| 前端 IP 地址 | 选择前端 IP 地址。 </br> 要关联入站 NAT 规则的负载均衡器的前端 IP 地址。 |
| IP 版本 | 选项为 IPv4 和 IPv6。 |
| 服务 | 你将在 Azure 负载均衡器上运行的服务的类型。 </br> 此处的选择将相应地更新端口信息。 |
| 协议 | Azure 负载均衡器是第4层网络负载均衡器。 </br> 选项有： TCP 或 UDP。 |
| 空闲超时（分钟） | 使 TCP 或 HTTP 连接保持打开状态，而不依赖于客户端发送 keep-alive 消息。 |
| TCP 重置 | 负载均衡器可以发送 TCP 重置，以帮助在连接空闲时创建更可预测的应用程序行为。 </br> 了解有关[TCP 重置](load-balancer-tcp-reset.md)的详细信息 |
| Port | 此设置是与要根据此入站 NAT 规则分发流量的前端 IP 关联的端口。 |
| 目标虚拟机 | 要将此规则关联到的后端池的虚拟机部分。 |
| 端口映射 | 此设置可以是默认值，也可以是基于应用程序首选项自定义的。 |

:::image type="content" source="./media/manage/inbound-nat-rule.png" alt-text="添加入站 NAT 规则。" border="true":::

## <a name="outbound-rules"></a>出站规则

负载均衡器出站规则为后端池中的 VM 配置出站 SNAT。

如果要将出站规则添加到负载均衡器，请在 Azure 门户中，选择 " **出站规则**"，然后选择 " **+ 添加**"。

| 设置 | 详细信息 |
| ------- | ------ |
| 名称 | 出站规则的名称。 |
| 前端 IP 地址 | 选择前端 IP 地址。 </br> 要将出站规则关联到的负载均衡器的前端 IP 地址。 |
| 协议 | Azure 负载均衡器是第4层网络负载均衡器。 </br> 选项包括： **All**、 **TCP**或 **UDP**。 |
| 空闲超时（分钟） | 使 **TCP** 或 **HTTP** 连接保持打开状态，而不依赖于客户端发送 keep-alive 消息。 |
| TCP 重置 | 负载均衡器可以发送 **TCP 重置** ，以帮助在连接空闲时创建更可预测的应用程序行为。 </br> 了解有关[TCP 重置](load-balancer-tcp-reset.md)的详细信息 |
| 后端池 | 希望应用此出站规则的后端池。 |

### <a name="port-allocation"></a>端口分配

| 设置 | 详细信息 |
| ------- | ------ |
| 端口分配 | 建议选择 " **手动选择出站端口数**"。|

### <a name="outbound-ports"></a>出站端口

| 设置 | 详细信息 |
| ------- | ------ |
| 选择方式 | 选择“每个实例的端口数” |
| 每个实例的端口 | 输入 10,000。 |

:::image type="content" source="./media/manage/outbound-rule.png" alt-text="添加入站出站规则。" border="true":::

## <a name="next-steps"></a>后续步骤

本文介绍了 Azure 负载均衡器的 Azure 门户中的不同术语和设置。

* [了解](./load-balancer-overview.md) 有关 Azure 负载均衡器的详细信息。
* Azure 负载均衡器的[常见问题解答](./load-balancer-faqs.md)。
