---
title: IP 地址 168.63.129.16 是什么？ | Microsoft Docs
description: 了解 IP 地址 168.63.129.16 以及它如何与资源一起工作。
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: v-jesits
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/21/2019
ms.author: genli
ms.openlocfilehash: 7f0539e7c2f7e5ae8847b35b47d3708c6c5d6a09
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "62107978"
---
# <a name="what-is-ip-address-1686312916"></a>IP 地址 168.63.129.16 是什么？

IP 地址 168.63.129.16 是虚拟公共 IP 地址，用于简化 Azure 平台资源的通信通道。 客户可以在 Azure 中为其专有虚拟网络定义任何地址空间。 因此，Azure 平台资源必须显示为一个唯一的公共 IP 地址。 此虚拟公共 IP 地址有助于实现以下几个方面：

- 使 VM 代理能够与 Azure 平台通信，以表明它处于“就绪”状态。
- 启用与 DNS 虚拟服务器的通信，以便为没有自定义 DNS 服务器的资源（如 VM）提供筛选的名称解析。 此筛选确保客户只能解析其自己资源的主机名。
- 从负载均衡器启用运行状况探测，以确定负载均衡集中 VM 的运行状况状态。
- 为 PaaS 角色启用来宾代理检测信号消息。

## <a name="scope-of-ip-address-1686312916"></a>IP 地址 168.63.129.16 的作用域

虚拟公共 IP 地址 168.63.129.16 用于所有区域和所有国家云。 此特殊公共 IP 地址将不会更改。 默认网络安全组规则允许此 IP 地址。 建议在所有本地防火墙策略中允许此 IP 地址。 此特殊 IP 地址和资源之间的通信是安全的，因为只有内部 Azure 平台才能从此 IP 地址获得消息。 如果阻止此地址，可能会在各种场景中出现意外行为。

此外，可以要求流量从虚拟公共 IP 地址 168.63.129.16 流向为[负载均衡器运行状况探测](../load-balancer/load-balancer-custom-probe-overview.md)配置的终结点。 在非虚拟网络场景中，运行状况探测源自某个专用 IP。 

## <a name="next-steps"></a>后续步骤

- [安全组](security-overview.md)
- [创建、更改或删除网络安全组](manage-network-security-group.md)
