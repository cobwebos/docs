---
title: Azure 前端-URL 重写 |Microsoft Docs
description: 本文将帮助你了解 Azure 前门如何为你的路由重写 URL （如果已配置）。
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
ms.openlocfilehash: eb5b4ab8a23a374aec54d65dd5390ab3fec3e905
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91445478"
---
# <a name="url-rewrite-custom-forwarding-path"></a>URL 重写（自定义转发路径）
Azure 前门支持 URL 重写，方法是配置一个可选的 **自定义转发路径** ，以便在构造转发到后端的请求时使用。 默认情况下，如果未提供自定义转发路径，则前门会将传入 URL 路径复制到在转发的请求中使用的 URL。 转发请求中使用的主机标头与为选定后端配置的主机标头一样。 阅读[后端主机标头](front-door-backend-pool.md#hostheader)，了解它的作用以及配置方式。

URL 重写的强大部分是，自定义转发路径会将与通配符路径匹配的传入路径的任何部分复制到转发路径 (这些路径段是以下示例中的 **绿色** 段) ：
</br>

:::image type="content" source="./media/front-door-url-rewrite/front-door-url-rewrite-example.jpg" alt-text="Azure Front Door URL 重写":::

## <a name="url-rewrite-example"></a>URL 重写示例
请考虑使用配置了以下前端主机和路径组合的路由规则：

| 主机      | 路径       |
|------------|-------------|
| www\.contoso.com | /\*   |
|            | /foo        |
|            | /foo/\*     |
|            | /foo/bar/\* |

下表的第一列显示了传入请求的示例，第二列显示了“最确切”的匹配路由“路径”。  表中的第三个和第三列是配置的 **自定义转发路径**的示例。

例如，如果查看第二行，可发现其表示对于传入请求 `www.contoso.com/sub`，如果自定义转发路径为 `/`，则转发的路径将为 `/sub`。 如果自定义转发路径为 `/fwd/`，则转发的路径为 `/fwd/sub`。 其余列以此类推。 下方路径中**强调**的部分表示通配符匹配的部分。

| 传入的请求       | 最确切的匹配路径 | /          | /fwd/          | /foo/          | /foo/bar/          |
|------------------------|--------------------------|------------|----------------|----------------|--------------------|
| www \. contoso.com/            | /\*                      | /          | /fwd/          | /foo/          | /foo/bar/          |
| www \. contoso.com/**sub**     | /\*                      | /**该子**   | /fwd/**sub**   | /foo/**sub**   | /foo/bar/**sub**   |
| www \. contoso.com/**a/b/c**   | /\*                      | /**a/b/c** | /fwd/**a/b/c** | /foo/**a/b/c** | /foo/bar/**a/b/c** |
| www \. contoso.com/foo         | /foo                     | /          | /fwd/          | /foo/          | /foo/bar/          |
| www \. contoso.com/foo/        | /foo/\*                  | /          | /fwd/          | /foo/          | /foo/bar/          |
| www \. contoso.com/foo/**bar** | /foo/\*                  | /**栏**   | /fwd/**bar**   | /foo/**bar**   | /foo/bar/**bar**   |

## <a name="optional-settings"></a>可选设置
还可以为任何给定的路由规则设置指定其他可选设置：

* **缓存配置** -如果已禁用或未指定，则与此路由规则匹配的请求将不会尝试使用缓存的内容，而是始终从后端提取。 阅读关于[使用 Front Door 缓存](front-door-caching.md)的详细信息。

## <a name="next-steps"></a>后续步骤

- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的工作原理](front-door-routing-architecture.md)。
