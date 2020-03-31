---
title: Azure VMware 云简单解决方案 - 为云简单私有云选择负载平衡解决方案
description: 描述在私有云中部署应用程序的负载平衡选项
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6c98b699b1d3aba15ce69c519d35d7ce3e90d123
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77014872"
---
# <a name="choose-a-load-balancing-solution-for-cloudsimple-private-clouds"></a>为云简单私有云选择负载平衡解决方案

在 CloudSimple 私有云中部署应用程序时，您可以选择多个选项中的任何一个进行负载平衡。

您可以在 CloudSimple 私有云中选择虚拟或基于软件的负载均衡器，甚至使用 Azure 订阅中运行的 Azure L7 负载均衡器，以在云简单私有云中运行的前端 Web 层 VM。 在这里，我们列出了几个选项：

## <a name="virtual-load-balancers"></a>虚拟负载均衡器

您可以通过 vCenter 接口在 VMware 环境中部署虚拟负载均衡器设备，并将它们配置为应用程序流量的前端。

一些流行的供应商是： NginX： http://nginx.org/en/docs/http/load_balancing.html F5- BigIP https://www.f5.com/products/big-ip-services/virtual-editions - 流量管理器： Citrix ADC：https://www.citrix.com/products/citrix-adc/

## <a name="azure-l7-load-balancer"></a>Azure L7 负载均衡器

当您使用 Azure 应用程序网关作为在私有云中运行的应用程序的 L7 负载均衡器时，不需要管理负载均衡器软件。 负载均衡器软件由 Azure 管理。 私有云中的所有 Web 层 VM 都使用专用 IP 地址，无需其他 NAT 规则或公共 IP 地址来解析名称。 Web 层 VM 通过专用、低延迟、高带宽连接与 Azure 应用程序网关通信。

要了解有关如何配置此解决方案的详细信息，请参阅有关使用 Azure 应用程序网关作为 L7 负载均衡器的解决方案指南。

## <a name="azure-internal-load-balancer"></a>Azure 内部负载均衡器

如果选择在混合部署中运行应用程序，其中 Web 前端层在 Azure 订阅中的 Azure vNet 中运行，并且应用程序的数据库层在 CloudSimple 私有云中的 VMware VM 中运行，则可以使用 Azure 内部负载用于流量管理的数据库层 VM 前面的平衡器（L4 负载均衡器）。

要了解详细信息，请参阅 Azure[内部负载均衡器](../load-balancer/concepts-limitations.md#internalloadbalancer)文档。

## <a name="global-server-load-balancer"></a>全局服务器负载均衡器

如果要查找基于 DNS 的负载均衡器，则可以使用 Azure 应用商店中提供的第三方解决方案，也可以使用本机 Azure 解决方案。

Azure 流量管理器是基于 DNS 的流量负载均衡器，使您能够以最佳方式将流量分发到跨全局 Azure 区域和本地的服务，同时提供高可用性和响应能力。 要了解详细信息，请参阅 Azure[流量管理器](../traffic-manager/traffic-manager-configure-geographic-routing-method.md)文档。
