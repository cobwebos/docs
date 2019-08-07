---
title: VMware 解决方案 (按 CloudSimple)-Azure 公共 IP 地址
description: 通过 CloudSimple 了解公共 IP 地址及其对 VMware 解决方案的好处
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 29d2bdb75a7f4409e6b3ffee2608cfe7adde6b1f
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812521"
---
# <a name="cloudsimple-public-ip-address-overview"></a>CloudSimple 公共 IP 地址概述

使用公共 IP 地址, internet 资源可以与专用 IP 地址的私有云资源进行入站通信。 专用 IP 地址是虚拟机或软件负载平衡器。 专用 IP 地址在私有云 vCenter 上。 公共 IP 地址可让你将在私有云上运行的服务公开到 internet。

公共 IP 地址专用于专用 IP 地址, 直到你取消分配该地址。 公共 IP 地址只能分配给一个专用 IP 地址。

与公共 IP 地址关联的资源始终使用公共 IP 地址进行 internet 访问。 默认情况下, 仅允许对公共 IP 地址进行出站 internet 访问。  拒绝公共 IP 地址上的传入流量。  若要允许入站流量, 请为特定端口创建公共 IP 地址的防火墙规则。

## <a name="benefits"></a>优点

使用公共 IP 地址进行入站通信提供:

* 分布式拒绝服务 (DDoS) 攻击防护。 自动为公共 IP 地址启用此保护。
* 始终开启流量监视和常见网络级别攻击的实时缓解措施。 这些防御措施与 Microsoft 联机服务使用的防御方法相同。
* Azure 全球网络的整个缩放。 网络可用于跨区域分发和缓解攻击流量。  

## <a name="next-steps"></a>后续步骤

* 了解如何[分配公共 IP 地址](https://docs.azure.cloudsimple.com/public-ips/)
