---
title: Azure CDN 规则引擎的 X-EC-Debug HTTP 标头 | Microsoft Docs
description: X-EC-Debug 调试缓存请求标头提供有关应用到所请求资产的缓存策略的附加信息。 这些标头特定于 Verizon。
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
ms.date: 04/12/2018
ms.author: v-deasim
ms.openlocfilehash: 3a99e322d81748c54585e7dd0eb06959bfeb9569
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
---
# <a name="x-ec-debug-http-headers-for-azure-cdn-rules-engine"></a>Azure CDN 规则引擎的 X-EC-Debug HTTP 标头
调试缓存请求标头 `X-EC-Debug` 提供有关应用到所请求资产的缓存策略的附加信息。 这些标头特定于 **Verizon 的 Azure CDN Premium** 产品。

## <a name="usage"></a>使用情况
仅当满足以下条件时，从 POP 服务器发送给用户的响应才包含 `X-EC-Debug` 标头：

- 已针对指定的请求的规则引擎启用[调试缓存响应标头功能](cdn-rules-engine-reference-features.md#debug-cache-response-headers)。
- 指定的请求定义要包含在响应中的调试缓存响应标头集。

## <a name="requesting-debug-cache-information"></a>请求调试缓存信息
在指定的请求中使用以下指令，定义要包含在响应中的调试缓存信息：

请求标头 | 说明 |
---------------|-------------|
X-EC-Debug: x-ec-cache | [缓存状态代码](#cache-status-code-information)
X-EC-Debug: x-ec-cache-remote | [缓存状态代码](#cache-status-code-information)
X-EC-Debug: x-ec-check-cacheable | [可缓存](#cacheable-response-header)
X-EC-Debug: x-ec-cache-key | [缓存键](#cache-key-response-header)
X-EC-Debug: x-ec-cache-state | [缓存状态](#cache-state-response-header)

### <a name="syntax"></a>语法

请求调试缓存响应标头时，可将以下标头和指定指令包括在请求中：

`X-EC-Debug: Directive1,Directive2,DirectiveN`

### <a name="sample-x-ec-debug-header"></a>示例 X-EC-Debug 标头

`X-EC-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state`

## <a name="cache-status-code-information"></a>缓存状态代码信息
X-EC-Debug 响应标头可以标识服务器及其如何通过以下指令处理了响应：

标头 | 说明
-------|------------
X-EC-Debug: x-ec-cache | 每当通过 CDN 路由内容时，都会报告此标头。 此标头标识完成请求的 POP 服务器。
X-EC-Debug: x-ec-cache-remote | 仅当请求的内容已缓存在来源防护服务器或 ADN 网关服务器上时，才报告此标头。

### <a name="response-header-format"></a>响应标头格式

X-EC-Debug 标头采用以下格式报告缓存状态代码信息：

- `X-EC-Debug: x-ec-cache: <StatusCode from Platform (POP/ID)>`

- `X-EC-Debug: x-ec-cache-remote: <StatusCode from Platform (POP/ID)>`

上述响应标头语法中使用的元素定义如下：
- StatusCode：指示 CDN 如何处理请求的内容，通过缓存状态代码表示。
    
    如果由于基于令牌的身份验证而拒绝了未授权的请求，则可以报告 TCP_DENIED 状态代码而不是 NONE。 但是，在查看缓存状态报告或原始日志数据时，将继续使用 NONE 状态代码。

- Platform：指示在其上请求内容的平台。 以下代码在此字段中有效：

    代码  | 平台
    ------| --------
    ECAcc | HTTP Large
    ECS   | HTTP Small
    ECD   | 应用程序传送网络 (ADN)

- POP：指示处理请求的 [POP](cdn-pop-abbreviations.md)。 

### <a name="sample-response-headers"></a>示例响应标头

以下示例标头提供请求的缓存状态代码信息：

- `X-EC-Debug: x-ec-cache: TCP_HIT from ECD (lga/0FE8)`

- `X-EC-Debug: x-ec-cache-remote: TCP_HIT from ECD (dca/EF00)`

## <a name="cacheable-response-header"></a>可缓存响应标头
`X-EC-Debug: x-ec-check-cacheable` 响应标头指示是否可能缓存了请求的内容。

此响应标头并不指示是否发生了缓存， 而是指示请求是否符合缓存的条件。

### <a name="response-header-format"></a>响应标头格式

报告是否可能缓存了请求的 `X-EC-Debug` 响应标头采用以下格式：

`X-EC-Debug: x-ec-check-cacheable: <cacheable status>`

上述响应标头语法中使用的元素定义如下：

值  | 说明
-------| --------
是    | 指示请求的内容是否符合缓存的条件。
否     | 指示请求的内容是否不符合缓存的条件。 此状态可能是以下原因之一造成的： <br /> - 客户特定的配置：特定于你帐户的配置可能阻止 POP 服务器缓存资产。 例如，规则引擎可能会通过对符合条件的请求启用“绕过缓存”功能，来阻止缓存资产。<br /> - 缓存响应标头：所请求资产的 Cache-Control 和 Expires 标头可能阻止 POP 服务器缓存该资产。
UNKNOWN | 指示服务器无法评估请求的资产是否可缓存。 如果由于基于令牌的身份验证而拒绝了请求，则通常会出现此状态。

### <a name="sample-response-header"></a>示例响应标头

以下示例响应标头指示是否可能缓存了请求的内容：

`X-EC-Debug: x-ec-check-cacheable: YES`

## <a name="cache-key-response-header"></a>缓存键响应标头
`X-EC-Debug: x-ec-cache-key` 响应标头指示与请求内容关联的物理缓存键。 物理缓存键包括一个路径，用于标识要缓存的资产。 换言之，服务器会根据 cache-key 所定义的路径查看缓存版的资产。

此物理缓存键以双正斜杠 (//) 开头，后接用于请求内容的协议（HTTP 或 HTTPS）。 此协议后接所请求资产的相对路径，该路径以内容接入点开头（例如 _/000001/_）。

默认情况下，HTTP 平台配置为使用 *standard-cache*，这意味着，查询字符串将被缓存机制忽略。 此类配置会阻止缓存键包含查询字符串数据。

如果将查询字符串记录在缓存键中，则该字符串会转换为其哈希等效值，然后插在所请求资产的名称与其文件扩展名之间（例如，asset&lt;hash value&gt;.html）。

### <a name="response-header-format"></a>响应标头格式

`X-EC-Debug` 响应标头采用以下格式报告物理缓存键信息：

`X-EC-Debug: x-ec-cache-key: CacheKey`

### <a name="sample-response-header"></a>示例响应标头

以下示例响应标头指示所请求内容的物理缓存键：

`X-EC-Debug: x-ec-cache-key: //http/800001/origin/images/foo.jpg`

## <a name="cache-state-response-header"></a>缓存状态响应标头
`X-EC-Debug: x-ec-cache-state` 响应标头指示所请求内容在请求时的缓存状态。

### <a name="response-header-format"></a>响应标头格式

`X-EC-Debug` 响应标头采用以下格式报告缓存状态信息：

`X-EC-Debug: x-ec-cache-state: max-age=MASeconds (MATimePeriod); cache-ts=UnixTime (ddd, dd MMM yyyy HH:mm:ss GMT); cache-age=CASeconds (CATimePeriod); remaining-ttl=RTSeconds (RTTimePeriod); expires-delta=ExpiresSeconds`

上述响应标头语法中使用的元素定义如下：

- MASeconds：指示所请求内容的 Cache-Control 标头定义的最大期限（以秒为单位）。

- MATimePeriod：将最大期限值（即 MASeconds）转换为单位更大（例如，天）的近似等效值。 

- UnixTime：指示所请求内容的缓存时间戳，以 Unix 时间（也称为 POSIX 时间或 Unix 时期）为单位。 缓存时间戳指示计算资产 TTL 的起始日期/时间。 

    如果源服务器不使用第三方 HTTP 缓存服务器或该服务器不返回 Age 响应标头，则缓存时间戳始终为检索或重新验证资产时的日期/时间。 否则，POP 服务器将使用 Age 字段计算资产的 TTL，如下所示：Retrieval/RevalidateDateTime - Age。

- ddd, dd MMM yyyy HH:mm:ss GMT：指示所请求内容的缓存时间戳。 有关详细信息，请参阅上面的 UnixTime 元素。

- CASeconds：指示自缓存时间戳以来所经历的秒数。

- RTSeconds：指示将缓存内容视为新鲜的剩余秒数。 此值的计算方式如下：RTSeconds = 最大期限 - 缓存期限。

- RTTimePeriod：将剩余 TTL 值（即 RTSeconds）转换为单位更大（例如，天）的近似等效值。

- ExpiresSeconds：指示在达到 `Expires` 响应标头中指定的日期/时间之前剩余的秒数。 如果 `Expires` 响应标头未包含在响应中，则此元素的值为 *none*。

### <a name="sample-response-header"></a>示例响应标头

以下示例响应标头指示所请求内容在请求时的缓存状态：

```X-EC-Debug: x-ec-cache-state: max-age=604800 (7d); cache-ts=1341802519 (Mon, 09 Jul 2012 02:55:19 GMT); cache-age=0 (0s); remaining-ttl=604800 (7d); expires-delta=none```

