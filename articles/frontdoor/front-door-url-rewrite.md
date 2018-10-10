---
title: Azure Front Door 服务 — URL 重写 | Microsoft Docs
description: 本文可帮助了解 Azure Front Door 服务如何为路由执行 URL 重写（如果已配置）。
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 00fe3aa7a641b9d07aad90a9d008a99efc6e9d97
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46993455"
---
# <a name="url-rewrite-custom-forwarding-path"></a>URL 重写（自定义转发路径）
Azure Front Door 服务支持 URL 重写，方法是允许配置可选的**自定义转发路径**，以便在构造要转发到后端的请求时使用。 默认情况下，如果未提供自定义转发路径，则 Front Door 会将传入的 URL 路径复制到转发请求中使用的 URL。 转发请求中使用的主机标头与为选定后端配置的主机标头一样。 阅读[后端主机标头](front-door-backend-pool.md#hostheader)，了解它的作用以及配置方式。

使用自定义转发路径进行 URL 重写的强大之处在于，它会将与通配符路径匹配的传入路径的任何部分复制到转发路径（这些路径段是下面示例中的**绿色**段）：
</br>
![Azure Front Door URL 重写][1]

## <a name="url-rewrite-example"></a>URL 重写示例
考虑配置了以下前端主机和路径的路由规则：

| 主机      | 路径       |
|------------|-------------|
| www.contoso.com | /\*         |
|            | /foo        |
|            | /foo/\*     |
|            | /foo/bar/\* |

下表的第一列显示了传入请求的示例，第二列显示了“最确切”的匹配路由“路径”。  表中第一行的第三列和后续列是已配置的**自定义转发路径**的示例，这些列中的其余行表示转发的请求路径与该行中的请求匹配时的示例。

例如，如果查看第二行，可发现其表示对于传入请求 `www.contoso.com/sub`，如果自定义转发路径为 `/`，则转发的路径将为 `/sub`。 如果自定义转发路径为 `/fwd/`，则转发的路径为 `/fwd/sub`。 其余列以此类推。 下方路径中**强调**的部分表示通配符匹配的部分。


| 传入的请求       | 最确切的匹配路径 | /          | /fwd/          | /foo/          | /foo/bar/          |
|------------------------|--------------------------|------------|----------------|----------------|--------------------|
| www.contoso.com/            | /\*                      | /          | /fwd/          | /foo/          | /foo/bar/          |
| www.contoso.com/**sub**     | /\*                      | /**sub**   | /fwd/**sub**   | /foo/**sub**   | /foo/bar/**sub**   |
| www.contoso.com/**a/b/c**   | /\*                      | /**a/b/c** | /fwd/**a/b/c** | /foo/**a/b/c** | /foo/bar/**a/b/c** |
| www.contoso.com/foo         | /foo                     | /          | /fwd/          | /foo/          | /foo/bar/          |
| www.contoso.com/foo/        | /foo/\*                  | /          | /fwd/          | /foo/          | /foo/bar/          |
| www.contoso.com/foo/**bar** | /foo/\*                  | /**bar**   | /fwd/**bar**   | /foo/**bar**   | /foo/bar/**bar**   |


## <a name="optional-settings"></a>可选设置
还可以为任何给定的路由规则设置指定其他可选设置：

* **缓存配置**：如果禁用或未指定，则与此路由规则匹配的请求将不会尝试使用缓存内容，而是会始终从后端提取内容。 阅读关于[使用 Front Door 缓存](front-door-caching.md)的详细信息。



## <a name="next-steps"></a>后续步骤

- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的工作原理](front-door-routing-architecture.md)。

<!--Image references-->
[1]: ./media/front-door-url-rewrite/front-door-url-rewrite-example.jpg