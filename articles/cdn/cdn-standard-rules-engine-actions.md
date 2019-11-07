---
title: 从 Microsoft 标准规则引擎操作 Azure CDN |Microsoft Docs
description: Microsoft 标准规则引擎操作 Azure CDN 的参考文档。
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: dbde93cc7ffd21e341653407e6e4f910e4620974
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615983"
---
# <a name="azure-cdn-from-microsoft-standard-rules-engine-actions"></a>从 Microsoft 标准规则引擎操作中 Azure CDN

本文列出了来自 Microsoft[标准规则引擎](cdn-standard-rules-engine.md)的 Azure 内容分发网络（CDN）的可用操作的详细说明。

规则的第二部分是操作。 操作定义应用于由一组匹配条件确定的请求类型的行为。

## <a name="actions"></a>操作

以下操作可供使用。 

## <a name="cache-expiration"></a>缓存过期

此操作可让你覆盖规则匹配条件指定的请求的终结点的 TTL。

**必填字段**

缓存行为 |                
---------------|----------------
绕过缓存 | 如果选择此选项并且规则匹配，则不会缓存内容。
忽略 | 如果选择此选项并且规则匹配，则将用操作中指定的值覆盖从原点返回的 TTL 值。
缺少时设置 | 如果选择此选项并且规则匹配，则规则将 TTL 设置为在 "操作" 中指定的值。

**其他字段**

天 | 小时 | 分钟数 | 秒
-----|-------|---------|--------
Int | Int | Int | Int 

## <a name="cache-key-query-string"></a>缓存键查询字符串

此操作允许您根据查询字符串修改缓存键。

**必填字段**

行为 | 说明
---------|------------
包括 | 如果选择此选项并且规则匹配，则在生成缓存键时将包含参数中指定的查询字符串。 
缓存每个唯一的 URL | 选择此选项并且规则匹配时，每个唯一的 URL 都将有自己的缓存密钥。 
排除 | 如果选择此选项并且规则匹配，则在生成缓存键时将排除参数中指定的查询字符串。
忽略查询字符串 | 如果选择此选项并且规则匹配，则在生成缓存键时不会考虑查询字符串。 

## <a name="modify-request-header"></a>修改请求标头

此操作允许您修改发送到源的请求中存在的标头。

**必填字段**

操作 | HTTP 标头名称 | 值
-------|------------------|------
附加 | 如果选择此选项并且规则匹配，则 "标头名称" 中指定的标头将添加到具有指定值的请求。 如果标头已存在，则将值追加到现有值。 | String
覆盖 | 如果选择此选项并且规则匹配，则 "标头名称" 中指定的标头将添加到具有指定值的请求。 如果已存在该标头，则该值将覆盖现有值。 | String
删除 | 如果选择了此选项并且规则匹配并且规则中指定的标头存在，则将从请求中删除该选项。 | String

## <a name="modify-response-header"></a>修改响应标头

此操作允许你修改返回给你的最终客户端的响应中存在的标头

**必填字段**

操作 | HTTP 标头名称 | 值
-------|------------------|------
附加 | 如果选择此选项并且规则匹配，则 "标头名称" 中指定的标头将添加到具有指定值的响应中。 如果标头已存在，则将值追加到现有值。 | String
覆盖 | 如果选择此选项并且规则匹配，则 "标头名称" 中指定的标头将添加到具有指定值的响应中。 如果已存在该标头，则该值将覆盖现有值。 | String
删除 | 如果选择此选项并且规则匹配，并且规则中指定的标头存在，则将从响应中删除该选项。 | String

## <a name="url-redirect"></a>URL 重定向

此操作允许你将最终客户端重定向到新的 URL。 

**必填字段**

字段 | 说明 
------|------------
类型 | 选择将返回给请求者的响应类型。 选项为-302，已找到301，移动了，307了暂时重定向和308永久性重定向
协议 | 匹配请求、HTTP 或 HTTPS
主机名 | 选择将请求重定向到的主机名。 留空以保留传入的主机。
路径 | 定义要在重定向中使用的路径。 留空以保留传入路径。  
查询字符串 | 定义重定向中使用的查询字符串。 留空以保留传入查询字符串。 
片段 | 定义要在重定向中使用的片段。 留空以保留传入片段。 

强烈建议使用绝对 URL。 使用相对 URL 可能会将 CDN URL 重定向到无效的路径。 

## <a name="url-rewrite"></a>URL 重写

此操作允许你将请求的路径重写到源。

**必填字段**

字段 | 说明 
------|------------
源模式 | 在要替换的 URL 路径中定义源模式。 当前，源模式使用基于前缀的匹配项。 若要匹配所有 URL 路径，请使用 "/" 作为源模式值。
目标 | 定义要用于重写中的目标路径。 这将覆盖源模式
保留不匹配的路径 | 如果为 "是"，则会将源模式后面的剩余路径追加到新的目标路径。 


[返回页首](#actions)

</br>

## <a name="next-steps"></a>后续步骤

- [Azure 内容分发网络概述](cdn-overview.md)
- [规则引擎参考](cdn-standard-rules-engine-reference.md)
- [规则引擎匹配条件](cdn-standard-rules-engine-match-conditions.md)
- [使用标准规则引擎强制执行 HTTPS](cdn-standard-rules-engine.md)
