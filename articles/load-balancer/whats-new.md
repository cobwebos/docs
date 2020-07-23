---
title: Azure 负载均衡器中的新增功能
description: 了解 Azure 负载均衡器的新增功能，例如最新的发行说明、已知问题、bug 修复、已弃用的功能和即将发布的更改。
services: load-balancer
author: anavinahar
ms.service: load-balancer
ms.topic: overview
ms.date: 07/07/2020
ms.author: anavin
ms.openlocfilehash: 4725aaaf7a2c1e0aa49ea8dbe046b720727a5d54
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147468"
---
# <a name="whats-new-in-azure-load-balancer"></a>Azure 负载均衡器中的新增功能有哪些？

Azure 负载均衡器会定期更新。 随时了解最新公告。 本文提供以下事项的信息：

- 最新版本
- 已知问题
- Bug 修复
- 已弃用的功能（如果适用）

你还可以[在此处](https://azure.microsoft.com/updates/?category=networking&query=load%20balancer)找到最新的 Azure 负载均衡器更新并订阅 RSS 源。

## <a name="new-features"></a>新增功能

|Feature  |说明  |添加日期  |
|---------|---------|---------|
| 支持基于 IP 的后端池管理（预览版） | Azure 负载均衡器支持通过 IPv4 或 IPv6 地址从后端池中添加和删除资源。 这样就可以轻松管理与负载均衡器关联的容器、虚拟机和虚拟机规模集。 它还允许在创建关联资源之前将 IP 地址作为后端池的一部分保留。 | 2020 年 7 月 |
| 使用 Azure Monitor 的 Azure 负载均衡器见解 | 作为 Azure 网络监视器一部分，客户现在拥有所有负载平衡器配置的拓扑图，以及在 Azure 门户中预配置了指标的标准负载平衡器的运行状况仪表板。 [开始使用并了解详细信息](https://azure.microsoft.com/blog/introducing-azure-load-balancer-insights-using-azure-monitor-for-networks/) | 2020 年 6 月 |
| 对 Azure 负载均衡器的 IPv6 支持（正式发布版） | 可以将 IPv6 地址作为 Azure 负载均衡器的前端。 了解如何[在此处创建双堆栈应用程序](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) |2020 年 4 月|
| 空闲超时时的 TCP 重置（正式发布版）| 使用 TCP 重置创建更具可预测性的应用程序行为。 [了解详细信息](load-balancer-tcp-reset.md)| 2020 年 2 月 |

## <a name="next-steps"></a>后续步骤

有关 Azure 负载均衡器的详细信息，请参阅[什么是 Azure 负载均衡器？](load-balancer-overview.md)和[常见问题解答](load-balancer-faqs.md)。