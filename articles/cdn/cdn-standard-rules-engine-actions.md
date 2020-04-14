---
title: Azure CDN 的标准规则引擎中的操作 |微软文档
description: Azure 内容交付网络 （Azure CDN） 的标准规则引擎中操作的参考文档。
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: allensu
ms.openlocfilehash: 29138b4fc6716ae5361cc4d7f97ceba41b90c2da
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259946"
---
# <a name="actions-in-the-standard-rules-engine-for-azure-cdn"></a>Azure CDN 的标准规则引擎中的操作

在 Azure 内容交付网络 （Azure CDN）[的标准规则引擎](cdn-standard-rules-engine.md)中，规则由一个或多个匹配条件和操作组成。 本文详细介绍了在 Azure CDN 的标准规则引擎中可以使用的操作。

规则的第二部分是操作。 操作定义应用于匹配条件或匹配条件集标识的请求类型的行为。

## <a name="actions"></a>操作

以下操作可用于 Azure CDN 的标准规则引擎。 

### <a name="cache-expiration"></a>缓存到期

使用此操作可以覆盖规则与条件匹配的请求的终结点的活存时间 （TTL） 值。

#### <a name="required-fields"></a>Required fields

缓存行为 |  说明              
---------------|----------------
旁路缓存 | 选择此选项且规则匹配时，不会缓存内容。
替代 | 选择此选项且规则匹配时，从原点返回的 TTL 值将被用操作中指定的值覆盖。
如果丢失，则设置 | 选择此选项且规则匹配时，如果未从源返回 TTL 值，则规则将 TTL 设置到操作中指定的值。

#### <a name="additional-fields"></a>其他字段

Days | 小时 | 分钟数 | 秒
-----|-------|---------|--------
Int | Int | Int | Int 

### <a name="cache-key-query-string"></a>缓存密钥查询字符串

使用此操作可基于查询字符串修改缓存密钥。

#### <a name="required-fields"></a>Required fields

行为 | 说明
---------|------------
包括 | 选择此选项且规则匹配时，在生成缓存键时包括参数中指定的查询字符串。 
缓存每个唯一的 URL | 选择此选项且规则匹配时，每个唯一 URL 都有自己的缓存密钥。 
排除 | 选择此选项且规则匹配时，生成缓存键时将排除参数中指定的查询字符串。
忽略查询字符串 | 选择此选项且规则匹配时，生成缓存键时不考虑查询字符串。 

### <a name="modify-request-header"></a>修改请求标头

使用此操作可以修改发送到您的源的请求中存在的标头。

#### <a name="required-fields"></a>Required fields

操作 | HTTP 标头名称 | 值
-------|------------------|------
附加 | 选择此选项且规则匹配时，**标题名称**中指定的标头将添加到具有指定值的请求中。 如果标头已存在，则该值将追加到现有值。 | 字符串
Overwrite | 选择此选项且规则匹配时，**标题名称**中指定的标头将添加到具有指定值的请求中。 如果标头已存在，则指定的值将覆盖现有值。 | 字符串
删除 | 选择此选项后，规则匹配，并且规则中指定的标头存在，标头将从请求中删除。 | 字符串

### <a name="modify-response-header"></a>修改响应标头

使用此操作可以修改返回给客户端的响应中存在的标头。

#### <a name="required-fields"></a>Required fields

操作 | HTTP 标头名称 | 值
-------|------------------|------
附加 | 选择此选项且规则匹配时，使用指定的**值**将标题**名称**中指定的标头添加到响应中。 如果标头已存在，则**将值**追加到现有值。 | 字符串
Overwrite | 选择此选项且规则匹配时，使用指定的**值**将标题**名称**中指定的标头添加到响应中。 如果标头已存在，**则"值**"将覆盖现有值。 | 字符串
删除 | 选择此选项后，规则匹配，并且规则中指定的标头存在，标头将从响应中删除。 | 字符串

### <a name="url-redirect"></a>URL 重定向

使用此操作将客户端重定向到新 URL。 

#### <a name="required-fields"></a>Required fields

字段 | 说明 
------|------------
类型 | 选择要返回到请求器的响应类型：找到 （302）、移动 （301）、临时重定向 （307） 和永久重定向 （308）。
协议 | 匹配请求，HTTP，HTTPS。
主机名 | 选择要将请求重定向到的主机名。 留空以保留传入主机。
路径 | 定义要在重定向中使用的路径。 留空以保留传入路径。  
查询字符串 | 定义重定向中使用的查询字符串。 留空以保留传入查询字符串。 
片段 | 定义要在重定向中使用的片段。 留空以保留传入的片段。 

我们强烈建议您使用绝对 URL。 使用相对 URL 可能会将 Azure CDN URL 重定向到无效路径。 

### <a name="url-rewrite"></a>URL 重写

使用此操作重写正在前往您的原点的请求的路径。

#### <a name="required-fields"></a>Required fields

字段 | 说明 
------|------------
源模式 | 定义要替换的 URL 路径中的源模式。 目前，源模式使用基于前缀的匹配。 要匹配所有 URL 路径，请使用正向斜**/** 杠 （ ） 作为源模式值。
目标 | 定义要在重写中使用的目标路径。 目标路径覆盖源模式。
保留无与伦比的路径 | 如果设置为 **"是**"，则源模式后的剩余路径将追加到新目标路径。 

## <a name="next-steps"></a>后续步骤

- [Azure CDN 概述](cdn-overview.md)
- [标准规则引擎参考](cdn-standard-rules-engine-reference.md)
- [标准规则引擎中的匹配条件](cdn-standard-rules-engine-match-conditions.md)
- [使用标准规则引擎强制执行 HTTPS](cdn-standard-rules-engine.md)
