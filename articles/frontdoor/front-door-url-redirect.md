---
title: Azure 前端门的服务的 URL 重定向 |Microsoft Docs
description: 本文可帮助你了解 Azure 第一道防线服务如何支持 URL 重定向为其路由中，如果配置。
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: sharadag
ms.openlocfilehash: 3d77a16d24a1a843b39d97904a675518c43a525a
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332485"
---
# <a name="url-redirect"></a>URL 重定向
Azure 第一道防线服务可用于将流量重定向。 可以在多个级别 （协议、 主机名、 路径、 查询字符串） 的流量重定向和基于路径的重定向原样可以为各个微服务配置的所有功能。 这样可以简化应用程序配置、优化资源使用情况，并支持全局重定向和基于路径的重定向等新的重定向方案。
</br>

![Azure 的第一道防线 URL 重定向][1]

## <a name="redirection-types"></a>重定向类型
重定向类型设置为客户端了解的重定向的用途的响应状态代码。 支持以下类型的重定向：

- **301 （永久移动）** :指示目标资源已分配新的永久性 URI，并且任何将来对此资源的引用应该使用包含 Uri 之一。 使用 301 状态代码为 HTTP 到 HTTPS 重定向。 
- **（找到） 的 302**:指示目标资源暂时在一个不同的 URI。 因为有时可能会更改重定向，客户端应该继续使用有效的请求 URI 用于将来的请求。
- **307 （临时重定向）** :指示目标资源已临时驻留在其他 URI 下以及在用户代理不能更改它会执行自动重定向到该 URI 的请求方法。 重定向可随时间变化的因为客户端应该继续使用原始的有效请求 URI 用于将来的请求。
- **308 （永久重定向）** :指示目标资源已分配新的永久性 URI，并且任何将来对此资源的引用应该使用包含 Uri 之一。 具有链接编辑功能，客户端应该会自动重新链接引用为有效请求 URI 到一个或多个服务器，在可能的情况所发送的新引用。

## <a name="redirection-protocol"></a>重定向协议
您可以设置将用于重定向的协议。 这允许的最常见的重定向功能，设置 HTTP 到 HTTPS 重定向的用例之一。

- **仅 HTTPS**:设置协议为 HTTPS，如果想要将从 HTTP 到 HTTPS 的流量重定向。 Azure 的第一道防线服务建议，您应始终将设置重定向到 HTTPS 仅。
- **仅 HTTP**:这将传入请求重定向到 HTTP。 使用此值，如果你想要保留你的流量 HTTP 是唯一、 非加密型。
- **匹配请求**:此选项将保留传入的请求所用的协议。 因此，HTTP 请求将始终 HTTP 和 HTTPS 请求始终 HTTPS post 的重定向。

## <a name="destination-host"></a>目标主机
作为配置重定向路由的一部分，还可以更改主机名或重定向请求的域。 您可以设置此字段以更改重定向 URL 中的主机名或否则保留传入请求中的主机名。 因此，使用此字段可以重定向所有请求都发送 https://www.contoso.com/ * 到 https://www.fabrikam.com/ *。

## <a name="destination-path"></a>目标路径
对于你想要重定向中替换 URL 的路径段的情况下，可以设置此字段的新路径值。 否则，你可以选择保留重定向的路径值。 因此，使用此字段，您可以重定向所有请求都发送到 https://www.contoso.com/ * 到 https://www.contoso.com/redirected-site 。

## <a name="query-string-parameters"></a>查询字符串参数
您可以替换中的重定向 URL 的查询字符串参数。 若要替换任何现有的查询字符串从传入请求 URL，此将字段设置为 Replace，然后设置适当的值。 否则，可以通过设置为 Preserve 字段保留的查询字符串的原始集。 例如，使用此字段中，您可以重定向所有流量发送到 https://www.contoso.com/foo/bar 到 https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F 。 

## <a name="destination-fragment"></a>目标片段
目标片段后 '#'，通常由浏览器用来登录页面上的特定部分的部分 URL。 您可以将此字段将片段添加到重定向 URL 设置。

## <a name="next-steps"></a>后续步骤

- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的工作原理](front-door-routing-architecture.md)。

<!--Image references-->
[1]: ./media/front-door-url-redirect/front-door-url-redirect.png