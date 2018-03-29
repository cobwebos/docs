---
title: 负载均衡器的 Azure 资源管理器支持 | Microsoft Docs
description: 使用 Azure 资源管理器将 powershell 用于负载均衡器。 对负载均衡器使用模板
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: tysonn
ms.assetid: d0394f11-ee5a-4407-9d86-79c936297265
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: d1d098f45c391363523b6a56ec0c6fdb40e64d68
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2018
---
# <a name="using-azure-resource-manager-support-with-azure-load-balancer"></a>将 Azure 资源管理器支持与 Azure 负载均衡器配合使用



Azure 资源管理器是 Azure 中的首选服务管理框架。 Azure 负载均衡器可使用基于 Azure 资源管理器的 API 和工具进行管理。

## <a name="concepts"></a>概念

借助 Resource Manager，Azure 负载均衡器包含以下子资源：

* 前端 IP 配置 - 单个负载均衡器可包含一个或多个前端 IP 地址（也称为虚拟 IP，即 VIP）。 这些 IP 地址充当流量的入口。
* 后端地址池 - 即指与虚拟机网络接口卡 (NIC) 关联且分配有负载的 IP 地址。
* 负载均衡规则 - 规则属性将给定的前端 IP 和端口组合映射到一组后端 IP 地址和端口组合。 单个负载均衡器可拥有多个负载均衡规则。 每个规则都包含前端 IP 和端口，以及与 VM 关联的后端 IP 和端口。
* 探测 – 使用探测可以跟踪 VM 实例的运行状况。 如果运行状况探测失败，VM 实例会自动从轮转列表中删除。
* 入站 NAT 规则 - 定义流过前端 IP 并分配到后端 IP 的入站流量的 NAT 规则。

![](./media/load-balancer-arm/load-balancer-arm.png)

## <a name="quickstart-templates"></a>快速入门模板

Azure 资源管理器可让你使用声明性模板预配应用程序。 在单个模板中，可以部署多个服务及其依赖项。 在应用程序生命周期的每个阶段，可使用相同模板重复部署应用程序。

模板可包含以下项的定义：虚拟机、虚拟网络、可用性集、网络接口 (NIC)、存储帐户、负载均衡器、网络安全组和公开 IP。 使用模板，可创建复杂应用程序所需的一切内容。 模板文件可签入到内容管理系统进行版本控制和协作。

[了解有关模板的详细信息](../azure-resource-manager/resource-manager-template-walkthrough.md)

[了解有关网络资源的详细信息](../virtual-network/resource-groups-networking.md)

有关使用 Azure 负载均衡器的快速入门模板，请参阅 [GitHub 存储库](https://github.com/Azure/azure-quickstart-templates)（托管社区生成的模板集）。

模板示例：

* [负载均衡器中的 2 个 VM 和负载均衡规则](http://go.microsoft.com/fwlink/?LinkId=544799)
* [VNET 中包含内部负载均衡器和负载均衡器规则的 2 个 VM](http://go.microsoft.com/fwlink/?LinkId=544800)
* [负载均衡器中的 2 个 VM，在 LB 上配置 NAT 规则](http://go.microsoft.com/fwlink/?LinkId=544801)

## <a name="setting-up-azure-load-balancer-with-a-powershell-or-cli"></a>使用 PowerShell 或 CLI 设置 Azure 负载均衡器

Azure 资源管理器 cmdlet、命令行工具和 REST API 入门

* [Azure 网络 Cmdlet](https://msdn.microsoft.com/library/azure/mt163510.aspx) 可用于创建负载负载均衡器。
* [如何使用 Azure 资源管理器创建负载均衡器](load-balancer-get-started-ilb-arm-ps.md)
* [将 Azure CLI 与 Azure 资源管理结合使用](../xplat-cli-azure-resource-manager.md)
* [负载均衡器 REST API](https://msdn.microsoft.com/library/azure/mt163651.aspx)

## <a name="next-steps"></a>后续步骤

还可以[开始创建面向 Internet 的负载均衡器](load-balancer-get-started-internet-arm-ps.md)，并配置适合特定负载均衡器网络流量行为的[分发模式](load-balancer-distribution-mode.md)类型。

了解如何管理[负载均衡器的空闲 TCP 超时设置](load-balancer-tcp-idle-timeout.md)。 应用程序对于负载均衡器后的服务器需要使连接保持活动状态时，这非常重要。
