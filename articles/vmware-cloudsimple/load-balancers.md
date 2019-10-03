---
title: Azure VMware 解决方案 (按 CloudSimple)-为 CloudSimple 私有云选择负载平衡解决方案
description: 描述在私有云中部署应用程序的负载平衡选项
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f6fc5112f7106c6cc8f8736237ce803da43cd882
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2019
ms.locfileid: "69881042"
---
# <a name="choose-a-load-balancing-solution-for-cloudsimple-private-clouds"></a>为 CloudSimple 私有云选择负载平衡解决方案

在 CloudSimple 私有云中部署应用程序时, 可以选择多个选项进行负载平衡。

你可以选择 CloudSimple 私有云中的虚拟或基于软件的负载均衡器, 甚至可以使用 Azure 订阅中运行的 Azure L7 负载均衡器在 CloudSimple 私有云中运行的 web 层 Vm。 此处列出了几个选项:

## <a name="virtual-load-balancers"></a>虚拟负载均衡器

可以通过 vCenter 接口在 VMware 环境中部署虚拟负载均衡器设备, 并将其配置为前端应用程序流量。

一些热门供应商:NginX http://nginx.org/en/docs/http/load_balancing.html BigIP-流量管理器: https://www.f5.com/products/big-ip-services/virtual-editions Citrix ADC: https://www.citrix.com/products/citrix-adc/

## <a name="azure-l7-load-balancer"></a>Azure L7 负载均衡器

将 Azure 应用程序网关用作在私有云中运行的应用程序的 L7 负载均衡器时, 无需管理负载均衡器软件。 负载平衡器软件由 Azure 管理。 私有云中的所有 web 层 Vm 均使用专用 IP 地址, 并且不需要其他 NAT 规则或公共 Ip 地址来解析名称。 Web 层 Vm 通过专用、低延迟、高带宽连接与 Azure 应用程序网关进行通信。

若要了解有关如何配置此解决方案的详细信息, 请参阅使用 Azure 应用程序关作为 L7 负载均衡器的解决方案指南。

## <a name="azure-internal-load-balancer"></a>Azure 内部负载均衡器

如果你选择在混合部署中运行你的应用程序, 其中 web 前端层在 Azure 订阅中的 Azure vNet 中运行, 并且该应用程序的数据库层在 CloudSimple 私有云的 VMware Vm 中运行, 则可以使用 Azure 内部负载用于流量管理的 DB 层 Vm 前面的均衡器 (L4 负载均衡器)。

若要了解详细信息, 请参阅 Azure[内部负载均衡器](../load-balancer/load-balancer-overview.md#internalloadbalancer)文档。

## <a name="global-server-load-balancer"></a>全局服务器负载均衡器

如果你正在寻找基于 DNS 的负载均衡器, 则可以使用 Azure Marketplace 中提供的第三方解决方案, 也可以使用本机 Azure 解决方案。

Azure 流量管理器是一种基于 DNS 的流量负载均衡器, 可让你以最佳方式将流量分配到全球 Azure 区域和本地的服务, 同时提供高可用性和响应能力。 若要了解详细信息, 请参阅 Azure[流量管理器](../traffic-manager/traffic-manager-configure-geographic-routing-method.md)文档。
