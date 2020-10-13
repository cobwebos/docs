---
title: Azure Front Door 规则引擎操作
description: 本文提供了一系列可使用 Azure Front Door 规则引擎执行的各种操作。
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2020
ms.author: duau
ms.openlocfilehash: ff61af192471bcfc9bdb9f1ce3970d5c22f39579
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91569781"
---
# <a name="azure-front-door-rules-engine-actions"></a>Azure Front Door 规则引擎操作

在 [AFD 规则引擎](front-door-rules-engine.md)中，规则由零个或多个匹配条件和操作组成。 本文将详细介绍可在 AFD 规则引擎中使用的操作。

操作定义了要应用于某个匹配条件或匹配条件集所识别的请求类型的行为。 在 AFD 规则引擎中，一个规则最多可以包含五个操作。 其中只有一个可以是路由配置替代操作（正向或重定向）。

可在 Azure Front Door 规则引擎中使用以下操作。  

## <a name="modify-request-header"></a>修改请求标头

使用此操作可以修改发送到源的请求中提供的标头。

### <a name="required-fields"></a>Required fields

操作 | HTTP 标头名称 | 值
-------|------------------|------
附加 | 如果选择此选项并且规则匹配，则会将“标头名称”中指定的标头添加到请求并使用指定的值。 如果该标头已存在，则会将该值追加到现有值后面。 | 字符串
Overwrite | 如果选择此选项并且规则匹配，则会将“标头名称”中指定的标头添加到请求并使用指定的值。 如果该标头已存在，则指定的值将替代现有值。 | 字符串
删除 | 如果选择此选项，规则匹配，并且在规则中指定的标头存在，则会从请求中删除该标头。 | 字符串

## <a name="modify-response-header"></a>修改响应标头

使用此操作可以修改返回给客户端的响应中提供的标头。

### <a name="required-fields"></a>Required fields

操作 | HTTP 标头名称 | 值
-------|------------------|------
附加 | 如果选择此选项并且规则匹配，则会通过使用指定的值将“标头名称”中指定的标头添加到响应 。 如果该标头已存在，则会将该“值”追加到现有值后面。 | 字符串
Overwrite | 如果选择此选项并且规则匹配，则会将“标头名称”中指定的标头添加到响应并使用指定的“值” 。 如果该标头已存在，则该“值”将替代现有值。 | 字符串
删除 | 如果选择此选项，规则匹配，并且在规则中指定的标头存在，则会从请求中删除该标头。 | 字符串

## <a name="route-configuration-overrides"></a>路由配置替代 

### <a name="route-type-redirect"></a>路由类型：重定向

使用此操作可将客户端重定向到一个新 URL。 

#### <a name="required-fields"></a>Required fields

字段 | 说明 
------|------------
重定向类型 | 选择要返回给请求方的响应类型：“已找到”(302)、“已移动”(301)、“临时重定向”(307) 和“永久重定向”(308)。
重定向协议 | 匹配请求、HTTP、HTTPS。
目标主机 | 选择要将请求重定向到的主机名。 留空会保留传入主机。
目标路径 | 定义要在重定向中使用的路径。 留空会保留传入路径。  
查询字符串 | 定义重定向中使用的查询字符串。 留空会保留传入的查询字符串。 
目标片段 | 定义要在重定向中使用的片段。 留空会保留传入片段。 


### <a name="route-type-forward"></a>路由类型：前进

使用此操作可将客户端转发到新 URL。 此操作还包含用于 URL 重写和缓存的子操作。 

字段 | 说明 
------|------------
后端池 | 选择后端池来替代和处理请求，这还将显示当前在 Front Door 配置文件中的所有预配置的后端池。 
转发协议 | 匹配请求、HTTP、HTTPS。
URL 重写 | 使用此操作可以重写路由到源的请求的路径。 如果已启用，请参阅下面的其他必填字段
Caching | “Enabled”、“Disabled”。 如果已启用，请参阅下面的其他必填字段。 

#### <a name="url-rewrite"></a>URL 重写

使用此设置可以配置可选的“自定义转发路径”，以便在构造要转发到后端的请求时使用。

字段 | 说明 
------|------------
自定义转发路径 | 定义要将请求转发到的路径。 

#### <a name="caching"></a>Caching

使用这些设置来控制为包含查询字符串的请求缓存文件的方式。 是否根据所有参数或所选参数来缓存内容。 可以使用其他设置来替代生存时间 (TTL) 值，以控制内容在缓存中保留多长时间。 若要强制缓存，请将缓存字段设置为“已启用”。 强制执行缓存时，将显示以下选项： 

缓存行为 |  说明              
---------------|----------------
忽略查询字符串 | 缓存资产后，所有后续请求将忽略查询字符串，直至缓存的资产过期。
缓存每个唯一的 URL | 具有唯一 URL 的每个请求（包括查询字符串）将被视为具有其自己的缓存的唯一资产。
忽略指定的查询字符串 | 缓存时将忽略在“查询参数”设置中列出的请求 URL 查询字符串。
包括指定的查询字符串 | 缓存时将使用“查询参数”设置中列出的请求 URL 查询字符串。

其他字段 |  说明 
------------------|---------------
动态压缩 | Front Door 可在边缘动态压缩内容，从而更快地做出响应。
查询参数 | 允许（或禁止）的参数的逗号分隔列表，用作缓存的基础。
缓存持续时间 | 缓存有效期持续时间，以天、小时、分钟、秒为单位。 所有值都必须为整数。 

## <a name="next-steps"></a>后续步骤

- 了解如何配置你的第一个[规则引擎](front-door-tutorial-rules-engine.md)。 
- 详细了解[规则引擎匹配条件](front-door-rules-engine-match-conditions.md)
- 详细了解 [Azure Front Door 规则引擎](front-door-rules-engine.md)
