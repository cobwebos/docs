---
title: Azure 前门 - URL 重定向 |微软文档
description: 本文可帮助您了解 Azure 前门如何支持其路由的 URL 重定向（如果配置）。
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
ms.openlocfilehash: 5e3e44c4aee84fe9e2e21174a1d65fdf26b765a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295474"
---
# <a name="url-redirect"></a>URL 重定向
您可以使用 Azure 前门重定向流量。 您可以在多个级别（协议、主机名、路径、查询字符串）重定向流量，并且所有功能都可以配置为单个微服务，因为重定向是基于路径的。 这样可以简化应用程序配置、优化资源使用情况，并支持全局重定向和基于路径的重定向等新的重定向方案。
</br>

![Azure 前门 URL 重定向][1]

## <a name="redirection-types"></a>重定向类型
重定向类型设置响应状态代码，以便客户端了解重定向的用途。 支持以下类型的重定向：

- **301（永久移动）：** 表示目标资源已分配新的永久 URI，将来对此资源的任何引用应使用随附的 URI 之一。 使用 301 状态代码进行 HTTP 到 HTTPS 重定向。 
- **302 （已找到）：** 指示目标资源暂时驻留在不同的 URI 下。 由于重定向有时可能会更改，因此客户端应继续使用有效的请求 URI 来执行将来的请求。
- **307（临时重定向）：** 指示目标资源暂时驻留在不同的 URI 下，并且用户代理如果自动重定向到该 URI，则用户代理不得更改请求方法。 由于重定向可能会随时间而变化，因此客户端应继续使用原始有效请求 URI 来执行将来的请求。
- **308（永久重定向）：** 指示目标资源已分配新的永久 URI，将来对此资源的任何引用应使用随附的 URI 之一。 具有链接编辑功能的客户端应尽可能自动将指向有效请求 URI 的引用重新链接到服务器发送的一个或多个新引用。

## <a name="redirection-protocol"></a>重定向协议
您可以设置将用于重定向的协议。 这允许重定向功能的最常见用例之一，即将 HTTP 设置为 HTTPS 重定向。

- **仅 HTTPS**：如果希望将流量从 HTTP 重定向到 HTTPS，则仅将协议设置为 HTTPS。 Azure 前门建议应始终仅将重定向设置为 HTTPS。
- **仅 HTTP**：这将传入请求重定向到 HTTP。 仅当要保留流量 HTTP（即非加密）时，才使用此值。
- **匹配请求**：此选项保留传入请求使用的协议。 因此，HTTP 请求将保持 HTTP，HTTPS 请求将保持 HTTPS 后重定向。

## <a name="destination-host"></a>目标主机
作为配置重定向路由的一部分，您还可以更改重定向请求的主机名或域。 您可以将此字段设置为更改重定向的 URL 中的主机名，或者以其他方式保留来自传入请求的主机名。 因此，使用此字段，您可以将发送到 的所有请求重定向`https://www.contoso.com/*`到`https://www.fabrikam.com/*`。

## <a name="destination-path"></a>目标路径
对于要将 URL 的路径段作为重定向的一部分替换的情况，可以使用新的路径值设置此字段。 否则，您可以选择保留路径值作为重定向的一部分。 因此，使用此字段，您可以将发送到 的所有请求重定向到`https://www.contoso.com/\*``https://www.contoso.com/redirected-site`。

## <a name="query-string-parameters"></a>查询字符串参数
您还可以替换重定向 URL 中的查询字符串参数。 为了替换来自传入请求 URL 的任何现有查询字符串，请将此字段设置为"替换"，然后设置相应的值。 否则，您可以通过将字段设置为"保留"来保留原始查询字符串集。 例如，使用此字段，可以将发送到 的所有流量重定向到`https://www.contoso.com/foo/bar``https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F`。 

## <a name="destination-fragment"></a>目标片段
目标片段是"+"之后 URL 的部分，浏览器通常用于在页面上的特定部分着陆。 您可以将此字段设置为向重定向 URL 添加片段。

## <a name="next-steps"></a>后续步骤

- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的工作原理](front-door-routing-architecture.md)。

<!--Image references-->
[1]: ./media/front-door-url-redirect/front-door-url-redirect.png