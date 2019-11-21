---
title: Azure Resource Manager support for Load Balancer
description: In this article, use Azure PowerShell and templates with Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: 57a60a65dee995695224aa1b451e848ea8768ab1
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74215393"
---
# <a name="azure-resource-manager-support-with-azure-load-balancer"></a>Azure Resource Manager support with Azure Load Balancer



Azure 资源管理器是 Azure 中的首选服务管理框架。 Azure 负载均衡器可使用基于 Azure 资源管理器的 API 和工具进行管理。

## <a name="concepts"></a>概念

借助 Resource Manager，Azure 负载均衡器包含以下子资源：

* Front-end IP configuration – a load balancer can include one or more frontend IP addresses, otherwise known as a virtual IPs (VIPs). 这些 IP 地址充当流量的入口。
* Back-end address pool – This pool is a collection of IP addresses associated with the virtual machine Network Interface Card (NIC) to which load is distributed.
* Load-balancing rules – a rule property maps a given frontend IP and port combination to a set of back-end IP addresses and port combination. A single load balancer can have multiple load-balancing rules. Each rule is a combination of a frontend IP and port and back-end IP and port associated with VMs.
* 探测 – 使用探测可以跟踪 VM 实例的运行状况。 如果运行状况探测失败，VM 实例会自动从轮转列表中删除。
* Inbound NAT rules – NAT rules defining the inbound traffic flowing through the frontend IP and distributed to the back-end IP.

![](./media/load-balancer-arm/load-balancer-arm.png)

## <a name="quickstart-templates"></a>快速启动模板

Azure 资源管理器使你可以使用声明性模板设置应用程序。 在单个模板中，你可以部署多个服务及其依赖项。 可在应用程序生命周期的每个阶段中使用相同模板重复部署应用程序。

Templates may include definitions for:
* **虚拟机**
* **虚拟网络**
* **可用性集**
* **Network interfaces (NICs)**
* **存储帐户**
* **负载均衡器**
* **网络安全组**
* **Public IPs.** 

使用模板，可创建复杂应用程序所需的一切内容。 模板文件可签入到内容管理系统进行版本控制和协作。

[了解有关模板的详细信息](../azure-resource-manager/resource-manager-template-walkthrough.md)

[了解有关网络资源的详细信息](../networking/networking-overview.md)

For Quickstart templates using Azure Load Balancer, see the [GitHub repository](https://github.com/Azure/azure-quickstart-templates) that hosts a set of community-generated templates.

模板示例：

* [负载均衡器中的 2 个 VM 和负载均衡规则](https://go.microsoft.com/fwlink/?LinkId=544799)
* [2 VMs in a VNET with an Internal Load Balancer and load balancer rules](https://go.microsoft.com/fwlink/?LinkId=544800)
* [2 VMs in a load balancer and configure NAT rules on the LB](https://go.microsoft.com/fwlink/?LinkId=544801)

## <a name="setting-up-azure-load-balancer-with-a-powershell-or-cli"></a>使用 PowerShell 或 CLI 设置 Azure 负载均衡器

Get started with Azure Resource Manager cmdlets, command-line tools, and REST APIs

* [Azure 网络 Cmdlet](https://docs.microsoft.com/powershell/module/az.network#networking) 可用于创建负载均衡器。
* [如何使用 Azure 资源管理器创建负载均衡器](load-balancer-get-started-ilb-arm-ps.md)
* [将 Azure CLI 与 Azure 资源管理结合使用](../xplat-cli-azure-resource-manager.md)
* [负载均衡器 REST API](https://msdn.microsoft.com/library/azure/mt163651.aspx)

## <a name="next-steps"></a>后续步骤

[Get started creating an Internet facing load balancer](load-balancer-get-started-internet-arm-ps.md) and configure the type of [distribution mode](load-balancer-distribution-mode.md) for specific network traffic behavior.

了解如何管理[负载均衡器的空闲 TCP 超时设置](load-balancer-tcp-idle-timeout.md)。 These settings are important when your application needs to keep connections alive for servers behind a load balancer.
