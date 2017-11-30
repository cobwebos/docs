---
title: "了解 Azure 中的出站连接 | Microsoft Docs"
description: "本文介绍了 Azure 如何使 VM 与公共 Internet 服务通信。"
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
editor: 
ms.assetid: 5f666f2a-3a63-405a-abcd-b2e34d40e001
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: d02960017b8793eccc2990a17e3d854991e877b6
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2017
---
# <a name="understanding-outbound-connections-in-azure"></a>了解 Azure 中的出站连接

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Azure 中的虚拟机 (VM) 可以与 Azure 外部的公用 IP 地址空间中的终结点进行通信。 当 VM 启动到公共 IP 地址空间中的目标的出站流时，Azure 将 VM 的专用 IP 地址映射到公共 IP 地址，并允许返回流量来访问 VM。

Azure 提供三种不同的方法来实现出站连接。 每种方法都有自己的功能和限制。 仔细查看每种方法以选择符合需求的方法。

| 方案 | 方法 | 注意 |
| --- | --- | --- |
| 独立 VM（无负载均衡器，无实例级公共 IP 地址） |默认 SNAT |Azure 关联用于 SNAT 的公共 IP 地址 |
| 负载均衡的 VM（VM 上没有实例级公共 IP 地址） |使用负载均衡器的 SNAT |Azure 对 SNAT 使用负载均衡器的公共 IP 地址 |
| 具有实例级公共 IP 地址的 VM（有或没有负载均衡器） |不使用 SNAT |Azure 使用分配给 VM 的公共 IP |

如果不希望 VM 与 Azure 外部的公共 IP 地址空间中的终结点通信，则可以使用网络安全组 (NSG) 来阻止访问。 [阻止公共连接](#preventing-public-connectivity)中详细介绍了 NSG 的使用。

## <a name="standalone-vm-with-no-instance-level-public-ip-address"></a>独立 VM（无实例级公共 IP 地址）

在此场景中，VM 不是 Azure 负载均衡器池的一部分，并且没有分配给它的实例级公共 IP (ILPIP) 地址。 当 VM 创建出站流时，Azure 将此出站流的专用源 IP 地址转换为公共源 IP 地址。 用于此出站流的公共 IP 地址是不可配置的，并且不会影响订阅的公共 IP 资源限制。 Azure 使用源网络地址转换 (SNAT) 来执行此功能。 使用公共 IP 地址的临时端口区分由 VM 产生的各个流。 创建流后 SNAT 动态分配临时端口。 在此情况下，用于 SNAT 的临时端口被称为 SNAT 端口。

SNAT 端口是可能会被耗尽的有限资源。 因此了解它们的使用方式很重要。 每个到单个目标 IP 的流使用一个 SNAT 端口。 对于到相同的目标 IP 地址和端口的多个流，每个流使用一个 SNAT 端口。 这可以确保源自相同的公共 IP 地址，并到相同的目标 IP 地址和端口的流的唯一性。 每个流均流到不同目标 IP 地址和端口的多个流共用一个 SNAT 端口。 目标 IP 地址和端口使流具有唯一性。

可使用[用于负载均衡器的 Log Analytics](load-balancer-monitor-log.md) 和[要监视 SNAT 端口耗尽消息的警报事件日志](load-balancer-monitor-log.md#alert-event-log)来监视出站连接的健康状况。 如果 SNAT 端口资源已经耗尽，那么在现有流释放 SNAT 端口之前出站流会失败。 负载均衡器对于回收 SNAT 端口使用 4 分钟的空闲超时时间。  查看后文的[具有实例级公共 IP 地址的 VM（有或没有负载均衡器）](#vm-with-an-instance-level-public-ip-address-with-or-without-load-balancer)和[管理 SNAT 耗尽的情况](#snatexhaust)部分。

## <a name="load-balanced-vm-with-no-instance-level-public-ip-address"></a>负载均衡的 VM（无实例级公共 IP 地址）

在此场景中，VM 是 Azure 负载均衡器池的一部分。  没有分配给 VM 的公共 IP 地址。 必须为负载均衡器资源配置一个规则来将公共 IP 前端链接到后端池。  如果没有完成此配置，则行为将如适用于[没有实例级公共 IP 的独立 VM](load-balancer-outbound-connections.md#standalone-vm-with-no-instance-level-public-ip-address)上一节内容中所述。

当负载均衡的 VM 创建出站流时，Azure 将此出站流的专用源 IP 地址转换为公共负载均衡器前端的公共 IP 地址。 Azure 使用源网络地址转换 (SNAT) 来执行此功能。 使用负载均衡器的公共 IP 地址的临时端口区分由 VM 产生的各个流。 创建出站流后 SNAT 动态分配临时端口。 在此情况下，用于 SNAT 的临时端口被称为 SNAT 端口。

SNAT 端口是可能会被耗尽的有限资源。 因此了解它们的使用方式很重要。 每个到单个目标 IP 地址的流和端口使用一个 SNAT 端口。 对于到相同的目标 IP 地址和端口的多个流，每个流使用一个 SNAT 端口。 这可以确保源自相同的公共 IP 地址，并到相同的目标 IP 地址和端口的流的唯一性。 每个流均流到不同目标 IP 地址和端口的多个流共用一个 SNAT 端口。 目标 IP 地址和端口使流具有唯一性。

可使用[用于负载均衡器的 Log Analytics](load-balancer-monitor-log.md) 和[要监视 SNAT 端口耗尽消息的警报事件日志](load-balancer-monitor-log.md#alert-event-log)来监视出站连接的健康状况。 如果 SNAT 端口资源已经耗尽，那么在现有流释放 SNAT 端口之前出站流会失败。 负载均衡器对于回收 SNAT 端口使用 4 分钟的空闲超时时间。  查看下一个部分以及[管理 SNAT 耗尽的情况](#snatexhaust)。

## <a name="vm-with-an-instance-level-public-ip-address-with-or-without-load-balancer"></a>具有实例级公共 IP 地址的 VM（有或没有负载均衡器）

在此场景中，向 VM 分配了实例级公共 IP (ILPIP)。 VM 是否为负载均衡并不重要。 如果使用 ILPIP，则不使用源网络地址转换 (SNAT)。 VM 将 ILPIP 用于所有出站流。 如果应用程序启动很多出站流，并且遇到 SNAT 耗尽的情况，应考虑分配 ILPIP 以缓解 SNAT 约束。

## <a name="discovering-the-public-ip-used-by-a-given-vm"></a>发现指定 VM 所使用的公共 IP

有多种方法来确定出站连接的公共源 IP 地址。 OpenDNS 提供了一种服务可以向你显示 VM 的公共 IP 地址。 使用 nslookup 命令，可以将名称 myip.opendns.com 的 DNS 查询发送到 OpenDNS 解析程序。 该服务返回用于发送此查询的源 IP 地址。 在 VM 中执行以下查询时，返回的是用于该 VM 的公共 IP。

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventing-public-connectivity"></a>阻止公共连接

有时允许 VM 创建出站流是不可取的，或者可能需要管理哪些目标可以通过出站流访问或哪些目标可以启动入站流。 在此情况下，使用[网络安全组 (NSG)](../virtual-network/virtual-networks-nsg.md) 管理 VM 可以访问的目标以及可以启动入站流的公共目标。 将 NSG 应用于负载均衡的 VM 时，需要注意[默认标记](../virtual-network/virtual-networks-nsg.md#default-tags)和[默认规则](../virtual-network/virtual-networks-nsg.md#default-rules)。

必须确保 VM 可以接收来自 Azure 负载均衡器的运行状况探测请求。 如果 NSG 阻止来自 AZURE_LOADBALANCER 默认标记的运行状况探测请求，那么 VM 的运行状况探测程序会失败，并且 VM 被标记为停机。 负载均衡器停止向此 VM 发送新流。

## <a name="snatexhaust"></a>管理 SNAT 耗尽的情况

用于 SNAT 的临时端口是可用尽的资源，如[独立 VM（无实例级公共 IP 地址）](#standalone-vm-with-no-instance-level-public-ip-address)和[负载均衡的 VM（无实例级公共 IP 地址）](#standalone-vm-with-no-instance-level-public-ip-address)中所述。

如果知道正在启动与同一目标 IP 地址和端口的多个出站 TCP 或 UDP 连接，观察失败的出站连接，或者支持人员通知已耗尽 SNAT 端口，则有几个常见缓解选项可供选择。  查看这些选项，确定最适合自己的场景的选项。  一个或多个选项可能有助于管理这种场景。

### <a name="modify-application-to-reuse-connections"></a>修改应用程序以重复使用连接 
在应用程序中重复使用连接，可以降低对用于 SNAT 的临时端口的需求。  这尤其适用于 HTTP/1.1 这样的协议，在这些协议中，默认重复使用连接。  其他使用 HTTP 作为其传输（如 REST）的协议也可以因此受益。  对每个请求来说，重复使用总是优于单独的原子 TCP 连接，并且会导致更高性能和非常高效的 TCP 事务。

### <a name="modify-application-to-use-connection-pooling"></a>修改应用程序以使用连接池
可以在应用程序中使用连接池方案，其中在一组固定的连接（在可能的情况下，重复使用每一个）上，内部分布请求。  这会限制正在使用的临时端口的数量，并创建更加可预测的环境。  还可以通过在操作答复上单个连接阻塞时允许多个同时操作，增加请求的吞吐量。  连接池可能已经存在于正在使用的框架中，以开发应用程序或应用程序的配置设置。  可将它与连接重复使用相结合，并且多个请求使用指向相同目标 IP 地址和端口的可预测固定数量的端口，同时还可以从 TCP 事务的高效使用中受益，从而减少延迟和资源利用。

### <a name="modify-application-to-use-less-aggressive-retry-logic"></a>修改应用程序以使用主动性较低的重试逻辑
当用于 SNAT 的临时端口耗尽或应用程序故障发生时，无衰减或回退逻辑的积极重试或暴力重试会使耗尽状况再次发生或一直持续。 使用主动性较低的重试逻辑，可以降低对临时端口的需求。   临时端口有 4 分钟的空闲超时（不可调整），如果重试太过积极，则消耗没有机会进行自行清除。  因此，应用程序停用事务的方式和频率对于设计至关重要。

### <a name="assign-an-instance-level-public-ip-to-each-vm"></a>将实例级公共 IP 分配给每个 VM
这会场景更改为 [VM 的实例级公共 IP](#vm-with-an-instance-level-public-ip-address-with-or-without-load-balancer)。  用于各 VM 的公共 IP 的所有临时端口都可供 VM 使用（与以下场景相反：公共 IP 的临时端口与同相应后端池关联的 VM 的所有临时端口共享）。  需要作出一些权衡，比如 IP 地址的额外成本和将大量个人 IP 地址列入白名单所产生的潜在影响。

## <a name="limitations"></a>限制

如果[多个（公共）IP 地址与一个负载均衡器关联](load-balancer-multivip-overview.md)，则所有这些公共 IP 地址皆是出站流的候选项。

Azure 使用算法根据池的大小来确定可用的 SNAT 端口数。  目前此项不可配置。

出站连接有 4 分钟的空闲超时。  这是不可调整的。

请务必记住，可用的 SNAT 端口数不会直接转换为连接数。 有关何时和如何分配 SNAT 端口以及[如何管理此可耗尽资源](#snatexhaust)的详细信息，请参考前一节。
