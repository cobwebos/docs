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
ms.openlocfilehash: 126b9ccefedee1f5cefdac8a8666a58e7a4a1fef
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548622"
---
# <a name="frequently-asked-questions-about-networking-in-azure-functions"></a>有关 Azure Functions 中的网络常见问题

下面是网络的常见问题的列表。 有关更完整概述，请阅读[函数网络选项文档](functions-networking-options.md)

## <a name="how-do-i-set-a-static-ip-in-functions"></a>如何在函数中设置静态 IP？

部署应用服务环境 (ASE) 中的函数目前只能为具有静态入站和出站 IP 函数。 有关使用 ASE 的详细信息，先参阅此文章：[创建和使用 ILB ASE](../app-service/environment/create-ilb-ase.md)。

## <a name="how-do-i-restrict-internet-access-to-my-function"></a>如何限制对我的函数的 internet 访问

你可以限制中通过多种方式，下面列出的 internet 访问。

* [IP 限制](../app-service/app-service-ip-restrictions.md)： 限制对 function app 的 IP 范围的入站的流量。
* 删除所有的 HTTP 触发器。 对于某些应用程序，就足以只需避免 HTTP 触发器，并使用任何其他事件源为触发函数。

执行此操作时最重要的考虑因素是请记住，Azure 门户编辑器要求您正在运行的函数，以使用直接访问。 通过 Azure 门户的任何代码更改需要你正在使用浏览门户具有其 IP 加入允许列表的设备。 您可以仍，但是，使用平台功能选项卡下的任何内容与现有的网络限制。

## <a name="how-do-i-restrict-my-function-app-to-a-vnet"></a>如何限制对 VNET 我函数的应用程序？

这样，所有流量通过 VNET 完全限制函数的唯一方法是使用内部负载均衡 (ILB) 应用服务环境 (ASE)。 此选项部署你的站点在 VNET 内的专用基础结构，并将发送的所有触发器和通过 VNET 的流量。 有关使用 ASE 的详细信息，先参阅此文章：[创建和使用 ILB ASE](../app-service/environment/create-ilb-ase.md)。

## <a name="how-can-i-access-resources-in-a-vnet-from-a-function-app"></a>如何从函数应用访问 VNET 中的资源？

您可以从正在运行的函数使用 VNET 集成访问 VNET 中的资源。 有关详细信息，请参阅[VNET 集成](functions-networking-options.md#vnet-integration)

## <a name="how-do-i-access-resources-protected-by-service-endpoints"></a>如何访问受保护的服务终结点资源？

（目前处于预览状态） 中使用新的 VNET 集成，可以访问服务终结点保护的资源从正在运行的函数。 有关详细信息，请参阅[预览 VNET 集成](functions-networking-options.md#preview-vnet-integration)。

## <a name="how-can-i-trigger-a-function-from-a-resource-in-a-vnet"></a>如何触发一个函数中的 VNET 中的资源？

仅可以通过将函数应用部署到应用服务环境触发一个函数中的 VNET 中的资源。 有关使用 ASE 的详细信息，请参阅[创建和使用 ILB ASE](../app-service/environment/create-ilb-ase.md)。


## <a name="how-can-i-deploy-my-function-app-in-a-vnet"></a>如何将函数应用在 VNET 中的部署？

部署到应用服务环境是唯一的方法来创建一个函数应用，全部位于使用 ILB ASE VNET 的详细信息，请开始的文章：[创建和使用 ILB ASE](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase)。

只需单向访问 VNET 的资源，或小于全面网络隔离的情况下，请参阅[Functions 网络概述](functions-networking-options.md)。

## <a name="next-steps"></a>后续步骤

若要了解有关网络的详细信息和函数： 

* [请按照我们的入门 VNET 集成教程](./functions-create-vnet.md)
* [了解有关此处的函数中的网络选项的详细信息](./functions-networking-options.md)
* [单击此处深入了解 VNET 与应用服务/Functions 的集成](../app-service/web-sites-integrate-with-vnet.md)
* [深入了解 Azure 中的 VNET](../virtual-network/virtual-networks-overview.md)
* [启用网络功能和使用应用服务环境的控制](../app-service/environment/intro.md)
