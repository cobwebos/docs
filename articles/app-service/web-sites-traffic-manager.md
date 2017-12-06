---
title: "使用 Azure 流量管理器控制 Azure 应用服务流量"
description: "本文提供了有关 Azure 流量管理器的摘要信息，因为它与 Azure 应用服务相关。"
services: app-service\web
documentationcenter: 
author: cephalin
writer: cephalin
manager: erikre
editor: mollybos
ms.assetid: dabda633-e72f-4dd4-bf1c-6e945da456fd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2016
ms.author: cephalin
ms.openlocfilehash: efb732e9be62313eb199cb2cfbb1fa4d2cde0282
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2017
---
# <a name="controlling-azure-app-service-traffic-with-azure-traffic-manager"></a>使用 Azure 流量管理器控制 Azure 应用服务流量
> [!NOTE]
> 本文提供了有关 Microsoft Azure 流量管理器的摘要信息，因为它与 Azure 应用服务相关。 有关 Azure 流量管理器本身的更多信息，请访问本文末尾的链接。
> 
> 

## <a name="introduction"></a>介绍
可以使用 Azure 流量管理器来控制如何将来自 Web 客户端的请求分发到 Azure 应用服务中的应用。 将应用服务终结点添加到 Azure 流量管理器配置文件时，Azure 流量管理器会跟踪应用服务的状态（正在运行、已停止或已删除），以便确定那些终结点中有哪些应该接收流量。

## <a name="routing-methods"></a>路由方法
Azure 流量管理器使用四种不同的路由方法。 下面的列表中介绍了与 Azure 应用服务相关时这些方法的情况。

* **[优先级](#priority)：**所有流量都使用主应用，并提供备份以防主或备份应用不可用。
* **[加权](#weighted)：**将流量分配到一组应用，不管是平均分配还是根据定义的权重进行分配。
* **[性能](#performance)：**当应用位于不同的地理位置时，请使用从最低网络延迟角度而言“最靠近的”应用。
* **[地理](#geographic)：**根据 DNS 查询来自的地理位置将用户定向到特定的应用。 

有关详细信息，请参阅[流量管理器路由方法](../traffic-manager/traffic-manager-routing-methods.md)。

## <a name="app-service-and-traffic-manager-profiles"></a>应用服务和流量管理器配置文件
要通过配置来控制应用服务流量，则需要在 Azure 流量管理器中创建一个使用前述三种负载均衡方法之一的配置文件，然后将要控制其流量的终结点（在此例中为应用服务）添加到该配置文件。 应用状态（正在运行、已停止或已删除）会定期传送到该配置文件，以便 Azure 流量管理器可以相应地对流量进行定向。

将 Azure 流量管理器与 Azure 一起使用时，请记住以下几点：

* 对于同一区域内的仅限应用部署，应用服务已经提供了与应用服务模式无关的故障转移和轮循机制功能。
* 对于同一区域中将应用服务与另一 Azure 云服务一起使用的部署，可以组合使用两种类型的终结点以启用混合方案。
* 在一个配置文件中，只能为每个区域指定一个应用服务终结点。 当选择某个应用作为一个区域的终结点后，该区域中的其余应用对于该配置文件会变为不可选择状态。
* 在 Azure 流量管理器配置文件中指定的应用服务终结点将出现在配置文件中应用“配置”页面的“域名”部分下，但无法在该位置进行配置。
* 在将应用添加到配置文件后，该应用门户页面的仪表板上的“网站 URL”会显示该应用的自定义域 URL（如果已经设置了一个）。 否则，它将显示流量管理器配置文件 URL（例如，`contoso.trafficmanager.net`）。 在应用的“配置”页面的“域名”部分下将可以看到应用的直接域名和流量管理器 URL。
* 自定义域名将正常工作，但除了将它们添加到应用之外，还必须配置 DNS 映射，使之指向流量管理器 URL。 有关如何为应用服务设置自定义域的信息，请参阅[将现有的自定义 DNS 名称映射到 Azure Web 应用](app-service-web-tutorial-custom-domain.md)。
* 只能将标准或高级模式下的应用添加到 Azure 流量管理器配置文件。

## <a name="next-steps"></a>后续步骤
有关 Azure 流量管理器概念及技术方面的概述，请参阅[流量管理器概述](../traffic-manager/traffic-manager-overview.md)。

有关将流量管理器与应用服务配合使用的详细信息，请参阅博客文章 [Using Azure Traffic Manager with Azure Web Sites](http://blogs.msdn.com/b/waws/archive/2014/03/18/using-windows-azure-traffic-manager-with-waws.aspx)（将 Azure 流量管理器用于 Azure 网站）和 [Azure Traffic Manager can now integrate with Azure Web Sites](https://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/)（Azure 流量管理器现在可以与 Azure 网站集成）。

