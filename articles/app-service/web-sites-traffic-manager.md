---
title: "使用 Azure 流量管理器控制 Azure Web 应用流量"
description: "本文提供了有关 Azure 流量管理器的摘要信息，因为它与 Azure Web 应用相关。"
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
ms.openlocfilehash: fb7d391e3118a9dccde5501c3f30c6f580932a30
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2017
---
# <a name="controlling-azure-web-app-traffic-with-azure-traffic-manager"></a>使用 Azure 流量管理器控制 Azure Web 应用流量
> [!NOTE]
> 本文提供了有关 Microsoft Azure 流量管理器的摘要信息，因为它与 Azure 应用服务 Web 应用相关。 有关 Azure 流量管理器本身的更多信息，请访问本文末尾的链接。
> 
> 

## <a name="introduction"></a>介绍
可以使用 Azure 流量管理器来控制如何将来自 Web 客户端的请求分发到 Azure 应用服务中的 Web 应用。 将 Web 应用终结点添加到 Azure 流量管理器配置文件时，Azure 流量管理器会跟踪 Web 应用的状态（正在运行、已停止或已删除），以便确定那些终结点中有哪些应该接收流量。

## <a name="load-balancing-methods"></a>负载均衡方法
Azure 流量管理器使用三种不同的负载均衡方法。 下面的列表中说明了这些方法，因为它们与 Azure Web 应用相关。

* **故障转移**：如果在不同区域有克隆的 Web 应用，可使用此方法配置一个 Web 应用来为所有 Web 客户端流量提供服务，并在另一区域中配置另一个 Web 应用以在第一个 Web 应用不可用时为该流量提供服务。
* **轮循机制**：如果在不同区域有克隆的 Web 应用，可使用此方法在不同区域中的 Web 应用间均等地分布流量。
* **性能**：“性能”方法根据到客户端的最短往返行程时间来分布流量。 “性能”方法可用于同一区域或不同区域中的 Web 应用。

## <a name="web-apps-and-traffic-manager-profiles"></a>Web 应用和流量管理器配置文件
要通过配置来控制 Web 应用流量，则需要在 Azure 流量管理器中创建一个使用前述三种负载均衡方法之一的配置文件，然后将要控制其流量的终结点（在此例中为 Web 应用）添加到该配置文件。 Web 应用状态（正在运行、已停止或已删除）会定期传送到该配置文件，以便 Azure 流量管理器可以相应地对流量进行定向。

将 Azure 流量管理器与 Azure 一起使用时，请记住以下几点：

* 对于同一区域内的仅限 Web 应用部署，Web 应用已经提供了与 Web 应用模式无关的故障转移和轮循机制功能。
* 对于同一区域中将 Web 应用与另一 Azure 云服务一起使用的部署，可以组合使用两种类型的终结点以启用混合方案。
* 在一个配置文件中，只能为每个区域指定一个 Web 应用终结点。 当选择某个 Web 应用作为一个区域的终结点后，该区域中的其余 Web 应用对于该配置文件会变为不可选择状态。
* 在 Azure 流量管理器配置文件中指定的 Web 应用终结点将出现在配置文件中 Web 应用“配置”页面的“域名”部分下，但无法在该位置进行配置。
* 在将 Web 应用添加到配置文件后，该 Web 应用门户页面的仪表板上的“网站 URL”会显示该 Web 应用的自定义域 URL（如果已经设置了一个）。 否则，它会显示流量管理器配置文件 URL（例如，`contoso.trafficmgr.com`）。 在 Web 应用的“配置”页面的“域名”部分下将可以看到 Web 应用的直接域名和流量管理器 URL。
* 自定义域名将正常工作，但除了将它们添加到 Web 应用之外，还必须配置 DNS 映射，使之指向流量管理器 URL。 有关如何为 Azure Web 应用设置自定义域的信息，请参阅[为 Azure 网站配置自定义域名](app-service-web-tutorial-custom-domain.md)。
* 只能将标准或高级模式下的 Web 应用添加到 Azure 流量管理器配置文件。

## <a name="next-steps"></a>后续步骤
有关 Azure 流量管理器概念及技术方面的概述，请参阅[流量管理器概述](../traffic-manager/traffic-manager-overview.md)。

有关将流量管理器与 Web 应用一起使用的详细信息，请参阅博客文章 [Using Azure Traffic Manager with Azure Web Sites](http://blogs.msdn.com/b/waws/archive/2014/03/18/using-windows-azure-traffic-manager-with-waws.aspx)（将 Azure 流量管理器用于 Azure 网站）和 [Azure Traffic Manager can now integrate with Azure Web Sites](https://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/)（Azure 流量管理器现在可以与 Azure 网站集成）。

