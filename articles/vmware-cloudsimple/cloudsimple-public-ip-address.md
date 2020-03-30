---
title: Azure VMware 解决方案（按云简单 - 公共 IP 地址）
description: 了解公共 IP 地址及其在 Azure VMware 解决方案上的优势（通过云简单
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 147ab6b5ace4493e1a0b303c320cb2e81d829b29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024969"
---
# <a name="cloudsimple-public-ip-address-overview"></a>云简单公共 IP 地址概述

公共 IP 地址允许互联网资源通过专用 IP 地址向私有云资源通信。 专用 IP 地址是私有云 vCenter 上的虚拟机或软件负载均衡器。 公共 IP 地址允许您将在私有云上运行的服务公开给互联网。

公共 IP 地址专用于专用 IP 地址，直到您取消分配它。 公共 IP 地址只能分配给一个专用 IP 地址。

与公共 IP 地址关联的资源始终使用公共 IP 地址进行 Internet 访问。 默认情况下，公共 IP 地址只允许访问出站互联网。  公共 IP 地址上的传入流量被拒绝。  要允许入站流量，请为特定端口的公共 IP 地址创建防火墙规则。

## <a name="benefits"></a>优点

使用公共 IP 地址来通信入站提供：

* 分布式拒绝服务 （DDoS） 攻击防护。 将自动为公共 IP 地址启用此保护。
* 始终打开流量监控并实时缓解常见网络级攻击。 这些防御与微软在线服务使用的防御相同。
* Azure 全局网络的整个规模。 该网络可用于跨区域分发和缓解攻击流量。  

## <a name="next-steps"></a>后续步骤

* 了解如何[分配公共 IP 地址](public-ips.md)