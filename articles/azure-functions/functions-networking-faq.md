---
title: 有关 Azure Functions 中的网络常见问题
description: 解答的一些最常见的问题和使用 Azure Functions 的网络方案。
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: troubleshooting
ms.date: 4/11/2019
ms.author: alkarche, glenga
ms.openlocfilehash: 3cf6a0d080e2d8cafcab8e69a614b59a470c7aba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60637040"
---
# <a name="frequently-asked-questions-about-networking-in-azure-functions"></a>有关 Azure Functions 中的网络常见问题

本文列出了关于在 Azure Functions 中的网络的常见问题。 有关更完整概述，请参阅[函数的网络选项](functions-networking-options.md)。

## <a name="how-do-i-set-a-static-ip-in-functions"></a>如何在函数中设置静态 IP？

部署应用服务环境中的函数目前只能为具有静态入站和出站 IP 函数。 有关使用应用服务环境的详细信息，一文着手[创建和使用具有应用服务环境的内部负载均衡器](../app-service/environment/create-ilb-ase.md)。

## <a name="how-do-i-restrict-internet-access-to-my-function"></a>如何限制对我的函数的 internet 访问

你可以限制在两种方法中的 internet 访问权限：

* [IP 限制](../app-service/app-service-ip-restrictions.md):限制对 function app 的 IP 范围的入站的流量。
* 删除的所有 HTTP 触发器。 对于某些应用程序，它就足以只需避免 HTTP 触发器和任何其他事件源用于触发函数。

请记住，Azure 门户编辑器需要直接访问正在运行的函数。 通过 Azure 门户的任何代码更改需要你正在使用浏览门户具有其 IP 加入允许列表的设备。 但您仍可以使用现有的网络限制使用平台功能选项卡下的任何内容。

## <a name="how-do-i-restrict-my-function-app-to-a-virtual-network"></a>如何限制对虚拟网络的我函数的应用程序？

完全限制的函数，以便所有流量都流经虚拟网络的唯一方法是使用内部负载均衡应用服务环境。 此选项部署你的站点上的虚拟网络中的专用基础结构，并将发送的所有触发器和通过虚拟网络的流量。 

有关使用应用服务环境的详细信息，一文着手[创建和使用具有应用服务环境的内部负载均衡器](../app-service/environment/create-ilb-ase.md)。

## <a name="how-can-i-access-resources-in-a-virtual-network-from-a-function-app"></a>如何从函数应用访问虚拟网络中的资源？

通过使用虚拟网络集成，可以从正在运行的函数访问虚拟网络中的资源。 有关详细信息，请参阅[虚拟网络集成](functions-networking-options.md#virtual-network-integration)。

## <a name="how-do-i-access-resources-protected-by-service-endpoints"></a>如何访问受保护的服务终结点资源？

通过使用虚拟网络集成 （目前处于预览状态），可以从正在运行的函数来访问服务终结点保护的资源。 有关详细信息，请参阅[虚拟网络集成预览](functions-networking-options.md#preview-version-of-virtual-network-integration)。

## <a name="how-can-i-trigger-a-function-from-a-resource-in-a-virtual-network"></a>如何触发一个函数中的虚拟网络中的资源？

你可以仅通过将函数应用部署到应用服务环境触发一个函数中的虚拟网络中的资源。 有关使用应用服务环境的详细信息，请参阅[创建和使用具有应用服务环境的内部负载均衡器](../app-service/environment/create-ilb-ase.md)。


## <a name="how-can-i-deploy-my-function-app-in-a-virtual-network"></a>如何将函数应用虚拟网络中的部署？

部署到应用服务环境是唯一的方法来创建完全是虚拟网络中的 function app。 有关应用服务环境中使用内部负载均衡器的详细信息，一文着手[创建和使用具有应用服务环境的内部负载均衡器](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase)。

需要为虚拟网络资源，或更全面的网络隔离少只有单向访问权限的情况下，请参阅[Functions 网络概述](functions-networking-options.md)。

## <a name="next-steps"></a>后续步骤

若要了解更多有关网络和函数： 

* [遵循有关如何开始使用虚拟网络集成教程](./functions-create-vnet.md)
* [了解有关 Azure Functions 中的网络选项的详细信息](./functions-networking-options.md)
* [了解有关虚拟网络与应用服务和 Functions 集成的详细信息](../app-service/web-sites-integrate-with-vnet.md)
* [了解有关 Azure 中的虚拟网络的详细信息](../virtual-network/virtual-networks-overview.md)
* [启用网络功能和使用应用服务环境的控制](../app-service/environment/intro.md)
