---
title: Azure 前端-URL 重定向 |Microsoft Docs
description: 本文将帮助你了解 Azure 前门如何支持其路由规则的 URL 重定向。
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 61077c7900530fd4c5be64054bedd9c5d087fe77
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91442047"
---
# <a name="url-redirect"></a>URL 重定向
Azure 前门可以在以下各级别重定向流量：协议、主机名、路径、查询字符串。 可以为单个微服务配置这些功能，因为重定向是基于路径的。 这可以通过优化资源使用来简化应用程序配置，并支持新的重定向方案，包括全局重定向和基于路径的重定向。
</br>

:::image type="content" source="./media/front-door-url-redirect/front-door-url-redirect.png" alt-text="Azure 前门 URL 重定向":::

## <a name="redirection-types"></a>重定向类型
重定向类型为客户端设置响应状态代码，以了解重定向的目的。 支持以下类型的重定向：

- **301 (永久移动) **：指示目标资源已分配新的永久 URI。 以后对此资源的任何引用都将使用其中一个包含的 Uri。 将301状态代码用于 HTTP 到 HTTPS 的重定向。 
- **302 (找到) **：指示目标资源暂时在不同的 URI 下。 由于重定向可能会发生变化，因此客户端应继续使用有效的请求 URI 来处理将来的请求。
- **307 (临时重定向) **：指示目标资源暂时在不同的 URI 下。 如果请求方法自动重定向到该 URI，则不能更改该方法。 由于重定向可能会随时间而变化，因此客户端应继续使用原始的有效请求 URI 来处理将来的请求。
- **308 (永久重定向) **：指示已为目标资源分配新的永久 URI。 以后对此资源的任何引用都应使用其中一个包含的 Uri。

## <a name="redirection-protocol"></a>重定向协议
你可以设置将用于重定向的协议。 重定向功能最常见的用例是将 HTTP 设置为 HTTPS 重定向。

- **仅限 https**：如果你想要将流量从 HTTP 重定向到 https，请将协议设置为 "仅限 https"。 Azure 前门建议你始终将重定向设置为 "仅限 HTTPS"。
- **仅限 http**：将传入请求重定向到 http。 仅当你想要将流量 HTTP 保持为非加密时，才使用此值。
- **匹配请求**：此选项保留传入请求所使用的协议。 因此，HTTP 请求将保持 HTTP，HTTPS 请求将保持 HTTPS post 重定向。

## <a name="destination-host"></a>目标主机
在配置重定向路由的过程中，还可以更改重定向请求的主机名或域。 你可以设置此字段以更改重定向的 URL 中的主机名，或通过其他方式保留传入请求中的主机名。 因此，使用此字段可以将发送到的所有请求重定向 `https://www.contoso.com/*` 到 `https://www.fabrikam.com/*` 。

## <a name="destination-path"></a>目标路径
如果要将 URL 的路径段替换为重定向的一部分，则可以使用新的 "路径" 值来设置此字段。 否则，你可以选择保留路径值作为重定向的一部分。 因此，使用此字段可以将发送到的所有请求重定向 `https://www.contoso.com/\*` 到  `https://www.contoso.com/redirected-site` 。

## <a name="query-string-parameters"></a>查询字符串参数
还可以替换重定向 URL 中的查询字符串参数。 若要替换传入请求 URL 中的任何现有查询字符串，请将此字段设置为 "Replace"，然后设置相应的值。 否则，可以通过将字段设置为 "保留" 来保留原始查询字符串集。 例如，使用此字段可以将发送到的所有流量重定向 `https://www.contoso.com/foo/bar` 到 `https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F` 。 

## <a name="destination-fragment"></a>目标片段
目标片段是 "#" 之后的 URL 部分，浏览器使用该部分在网页的特定部分上进行陆地。 您可以设置此字段，以便将片段添加到重定向 URL。

## <a name="next-steps"></a>后续步骤

- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的工作原理](front-door-routing-architecture.md)。
