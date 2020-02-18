---
title: Azure Functions 中的网络常见问题
description: 解答有关与 Azure Functions 联网的一些最常见的问题和方案。
author: alexkarcher-msft
ms.topic: troubleshooting
ms.date: 4/11/2019
ms.author: alkarche
ms.reviewer: glenga
ms.openlocfilehash: acb1e942c1f342ce6fee7d8aeacafcc1d7b6fd91
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75409529"
---
# <a name="frequently-asked-questions-about-networking-in-azure-functions"></a>Azure Functions 中的网络常见问题

本文列出了有关 Azure Functions 中的网络的常见问题。 有关更全面的概述，请参阅[函数网络选项](functions-networking-options.md)。

## <a name="how-do-i-set-a-static-ip-in-functions"></a>如何实现在函数中设置静态 IP？

目前，在应用服务环境中部署函数是为函数提供静态入站和出站 IP 的唯一方法。 有关使用应用服务环境的详细信息，请从[创建和使用带有应用服务环境的内部负载均衡器](../app-service/environment/create-ilb-ase.md)开始。

## <a name="how-do-i-restrict-internet-access-to-my-function"></a>如何实现限制 internet 访问我的函数？

可以通过以下几种方式限制 internet 访问：

* [Ip 限制](../app-service/app-service-ip-restrictions.md)：按 ip 范围限制到 function app 的入站流量。
    * 在 "IP 限制" 下，你还可以配置[服务终结点，这些终结点](../virtual-network/virtual-network-service-endpoints-overview.md)将你的函数限制为仅接受来自特定虚拟网络的入站流量。
* 删除所有 HTTP 触发器。 对于某些应用程序，只需避免使用 HTTP 触发器并使用任何其他事件源来触发函数。

请记住，Azure 门户编辑器要求直接访问正在运行的函数。 通过 Azure 门户进行的任何代码更改都需要使用的设备浏览门户以获得其 IP 允许列表。 但你仍可以使用 "平台功能" 选项卡下的任何内容，并提供网络限制。

## <a name="how-do-i-restrict-my-function-app-to-a-virtual-network"></a>如何实现将函数应用限制为虚拟网络？

可以使用[服务终结点](./functions-networking-options.md#private-site-access)将 function app 的**入站**流量限制为虚拟网络。 此配置仍允许 function app 对 internet 进行出站调用。

完全限制某个函数以便所有流量流过虚拟网络的唯一方式是使用内部负载平衡应用服务环境。 此选项将你的站点部署在虚拟网络中的专用基础结构上，并通过虚拟网络发送所有触发器和流量。 

有关使用应用服务环境的详细信息，请从[创建和使用带有应用服务环境的内部负载均衡器](../app-service/environment/create-ilb-ase.md)开始。

## <a name="how-can-i-access-resources-in-a-virtual-network-from-a-function-app"></a>如何从函数应用访问虚拟网络中的资源？

你可以使用虚拟网络集成从正在运行的功能访问虚拟网络中的资源。 有关详细信息，请参阅[虚拟网络集成](functions-networking-options.md#virtual-network-integration)。

## <a name="how-do-i-access-resources-protected-by-service-endpoints"></a>如何实现访问由服务终结点保护的资源？

通过使用虚拟网络集成，你可以从正在运行的功能访问服务终结点保护的资源。 有关详细信息，请参阅[虚拟网络集成](functions-networking-options.md#virtual-network-integration)。

## <a name="how-can-i-trigger-a-function-from-a-resource-in-a-virtual-network"></a>如何从虚拟网络中的资源触发函数？

可以允许使用[服务终结点](./functions-networking-options.md#private-site-access)从虚拟网络调用 HTTP 触发器。 

还可以通过将 function app 部署到高级计划、应用服务计划或应用服务环境，从虚拟网络中的所有其他资源触发函数。 有关详细信息，请参阅[非 HTTP 虚拟网络触发器](./functions-networking-options.md#virtual-network-triggers-non-http)

## <a name="how-can-i-deploy-my-function-app-in-a-virtual-network"></a>如何在虚拟网络中部署函数应用？

部署到应用服务环境是创建完全位于虚拟网络内部的函数应用的唯一方法。 有关将内部负载均衡器与应用服务环境结合使用的详细信息，请从[创建和使用带有应用服务环境的内部负载均衡器](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase)开始。

对于只需单向访问虚拟网络资源或不太全面的网络隔离的情况，请参阅[功能网络概述](functions-networking-options.md)。

## <a name="next-steps"></a>后续步骤

若要详细了解网络和功能： 

* [遵循有关虚拟网络集成入门的教程](./functions-create-vnet.md)
* [详细了解 Azure Functions 中的网络选项](./functions-networking-options.md)
* [详细了解与应用服务和功能的虚拟网络集成](../app-service/web-sites-integrate-with-vnet.md)
* [详细了解 Azure 中的虚拟网络](../virtual-network/virtual-networks-overview.md)
* [启用应用服务环境的更多网络功能和控制](../app-service/environment/intro.md)
