---
title: Azure CDN 规则引擎的 Verizon 特定的 HTTP 标头 | Microsoft Docs
description: 本文介绍如何使用 Azure CDN 规则引擎的 Verizon 特定的 HTTP 标头。
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: v-deasim
ms.openlocfilehash: 1a4bb48efe2a91c85b773341bb38b0f3ce4c7d9b
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
ms.locfileid: "31516291"
---
# <a name="verizon-specific-http-headers-for-azure-cdn-rules-engine"></a>Azure CDN 规则引擎的 Verizon 特定的 HTTP 标头

对于 **Verizon 的 Azure CDN Premium** 产品，将 HTTP 请求发送到源服务器后，接入点 (POP) 服务器可在发往 POP 的客户端请求中以添加一个或多个保留的标头（或代理特殊标头）。 这些标头是收到的标准转发标头的补充。 有关标准请求标头的信息，请参阅[请求字段](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields#Request_fields)。

如果想要防止在发往源服务器的 Azure CDN（内容分发网络）POP 请求中添加其中的某个保留标头，必须使用规则引擎中的[代理特殊标头功能](cdn-rules-engine-reference-features.md#proxy-special-headers)创建一个规则。 在此规则中，排除想要从标头字段的默认标头列表中删除的标头。 如果已启用[调试缓存响应标头功能](cdn-rules-engine-reference-features.md#debug-cache-response-headers)，请务必添加所需的 `X-EC-Debug` 标头。 

例如，若要删除 `Via` 标头，规则的标头字段应包含以下标头列表：X-Forwarded-For、X-Forwarded-Proto、X-Host、X-Midgress、X-Gateway-List、X-EC-Name、Host。 

![代理特殊标头规则](./media/cdn-http-headers/cdn-proxy-special-header-rule.png)

下表描述了 Verizon CDN POP 可在请求中添加的标头：

请求标头 | 说明 | 示例
---------------|-------------|--------
[Via](#via-request-header) | 标识发往源服务器的请求的代理 POP 服务器。 | HTTP/1.1 ECS (dca/1A2B)
X-Forwarded-For | 指示请求方的 IP 地址。| 10.10.10.10
X-Forwarded-Proto | 指示请求的协议。 | http
X-Host | 指示请求的主机名。 | cdn.mydomain.com
X-Midgress | 指示是否通过附加的 CDN 服务器来代理请求。 例如，POP 服务器到源防护服务器，或 POP 服务器到 ADN 网关服务器。 <br />仅当发生 midgress 流量时，才将此标头添加到请求中。 在这种情况下，标头设置为 1，指示通过附加的 CDN 服务器代理了请求。| 1
[主机](#host-request-header) | 标识可在其中找到所请求内容的主机和端口。 | marketing.mydomain.com:80
[X-Gateway-List](#x-gateway-list-request-header) | ADN：标识分配给客户源服务器的 ADN 网关服务器故障转移列表。 <br />源防护服务器：指示分配给客户源服务器的源防护服务器集。 | `icn1,hhp1,hnd1`
X-EC-_&lt;name&gt;_ | 以 *X-EC* 开头的请求标头（例如 X-EC-Tag、[X-EC-Debug](cdn-http-debug-headers.md)）保留给 CDN 使用。| waf-production

## <a name="via-request-header"></a>Via 请求标头
`Via` 请求标头标识 POP 服务器所用的格式由以下语法指定：

`Via: Protocol from Platform (POP/ID)` 

语法中使用的元素定义如下：
- Protocol：指示用于代理请求的协议版本（例如 HTTP/1.1）。 

- Platform：指示在其上请求内容的平台。 以下代码在此字段中有效： 
    代码 | 平台
    -----|---------
    ECAcc | HTTP Large
    ECS   | HTTP Small
    ECD   | 应用程序传送网络 (ADN)

- POP：指示处理请求的 [POP](cdn-pop-abbreviations.md)。 

- ID：仅供内部使用。

### <a name="example-via-request-header"></a>示例 Via 请求标头

`Via: HTTP/1.1 ECD (dca/1A2B)`

## <a name="host-request-header"></a>Host 请求标头
如果以下条件都成立，POP 服务器将覆盖 `Host` 标头：
- 所请求内容的源是客户源服务器。
- 相应客户源服务器的“HTTP Host 标头”选项不为空。

将覆盖 `Host` 请求标头，以反映“HTTP Host 标头”选项中定义的值。
如果客户源服务器的“HTTP Host 标头”选项设置为空，则请求方提交的 `Host` 请求标头将转发到客户的源服务器。

## <a name="x-gateway-list-request-header"></a>X-Gateway-List 请求标头
如果满足以下条件之一，POP 服务器将添加/覆盖 `X-Gateway-List 请求标头：
- 请求指向 ADN 平台。
- 请求转发到源防护服务器功能保护的客户源服务器。

