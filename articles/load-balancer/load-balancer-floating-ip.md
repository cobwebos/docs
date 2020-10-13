---
title: Azure 负载均衡器的浮动 IP 配置
description: Azure 负载均衡器浮动 IP 概述
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2020
ms.author: allensu
ms.openlocfilehash: e15aab3ebfe82fa97f5716769b5ff9675b4b3dc4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91637148"
---
# <a name="azure-load-balancer-floating-ip-configuration"></a>Azure 负载均衡器的浮动 IP 配置

负载均衡器针对 UDP 和 TCP 应用程序提供了多种功能。

## <a name="floating-ip"></a>浮动 IP

在某些应用程序场景中，后端池中单个 VM 上的多个应用程序实例更需要或要求使用相同端口。 重复使用端口的常见示例包括提供高可用性群集、网络虚拟设备，以及公开多个不重新加密的 TLS 终结点。 如果想要在多个规则中重复使用后端端口，必须在规则定义中启用浮动 IP。

“浮动 IP”是 Azure 的术语，表示是所谓的直接服务器返回 (DSR) 的一部分。 DSR 由两个部分组成：

- 流拓扑
- IP 地址映射方案

在平台级别，Azure 负载均衡器始终在 DSR 流拓扑中运行，无论是否已启用浮动 IP。 这意味着，流的出站部分始终正确重写为直接流回到来源。
如果不使用浮动 IP，则 Azure 将公开传统的负载均衡 IP 地址映射方案以便于使用（VM 实例的 IP）。 启用浮动 IP 会更改负载均衡器前端 IP 的 IP 地址映射，以实现更大的灵活性。 在[此处](load-balancer-multivip-overview.md)了解更多信息。

## <a name="limitations"></a><a name = "limitations"></a>限制

- 对于负载平衡方案，辅助 IP 配置当前不支持浮动 IP

## <a name="next-steps"></a>后续步骤

- 请参阅[创建公共标准负载均衡器](quickstart-load-balancer-standard-public-portal.md)，开始使用负载均衡器。
- 了解 [Azure 负载均衡器出站连接](load-balancer-outbound-connections.md)。
- 详细了解 [Azure 负载均衡器](load-balancer-overview.md)。
- 了解[运行状况探测](load-balancer-custom-probe-overview.md)。
- 了解有关[标准负载均衡器诊断](load-balancer-standard-diagnostics.md)的信息。
- 详细了解[网络安全组](../virtual-network/security-overview.md)。
