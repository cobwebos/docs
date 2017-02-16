---
title: "Azure 内容交付网络规则引擎功能 | Microsoft Docs"
description: "本主题介绍规则引擎的匹配条件和功能"
services: cdn
documentationcenter: 
author: Lichard
manager: akucer
editor: 
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2016
ms.author: rli
translationtype: Human Translation
ms.sourcegitcommit: 8a5d98bdc737fd9476b9db42100f58ed28619879
ms.openlocfilehash: a3d8199a9d5d067a4da1e1d40ff99bebc40a5097


---

# <a name="features-for-azure-content-delivery-network--cdn-rules-engine"></a>Azure 内容交付网络 (CDN) 规则引擎的功能
本主题列出了 Azure 内容交付网络 (CDN) [规则引擎](cdn-rules-engine.md)的可用功能的详细说明。

规则的第三部分是功能。 功能所定义的操作类型将应用于由一组匹配条件确定的请求类型。

## <a name="access"></a>Access

以下功能旨在控制对内容的访问。


Name | 目的
-----|--------
拒绝访问 | 通过“403 禁止访问”响应确定是否拒绝了所有请求。
令牌身份验证 | 确定是否会向请求应用基于令牌的身份验证。
令牌身份验证拒绝代码 | 确定以基于令牌的身份验证为由拒绝请求时为用户返回的响应类型。
令牌身份验证忽略 URL 大小写 | 确定通过基于令牌的身份验证进行的 URL 比较是否区分大小写。
令牌身份验证参数 | 确定是否应重命名基于令牌的身份验证查询字符串参数。

### <a name="deny-access"></a>拒绝访问
**目的**：通过“403 禁止访问”响应确定是否拒绝了所有请求。

值 | 结果
------|-------
Enabled| 导致系统发送“403 禁止访问”响应，拒绝满足匹配条件的所有请求。
已禁用| 还原默认行为。 默认行为是允许源服务器确定将返回的响应类型。

**默认行为**：禁用

> [!TIP]
   > 此功能的一项可能用途是将其与“请求标头”匹配条件关联，阻止访问那些使用内联链接访问用户内容的 HTTP 引用站点。

### <a name="token-auth"></a>令牌身份验证
**目的：**确定是否会向请求应用基于令牌的身份验证。

如果启用基于令牌的身份验证，则系统只会遵循提供了加密令牌且符合该令牌所指定要求的请求。

将用于加密和解密令牌值的加密密钥取决于“令牌身份验证”页上的“主密钥”和“备份密钥”选项。 请注意，加密密钥特定于平台。

值 | 结果
------|---------
Enabled | 通过基于令牌的身份验证保护请求的内容。 只遵循客户端发出的提供了有效令牌且符合其要求的请求。 FTP 事务不进行基于令牌的身份验证。
已禁用| 还原默认行为。 默认行为是允许基于令牌的身份验证配置，以便确定是否要对请求进行保护。

**默认行为：**禁用。

###<a name="token-auth-denial-code"></a>令牌身份验证拒绝代码
**目的：**确定以基于令牌的身份验证为由拒绝请求时为用户返回的响应类型。

下面列出了可用的响应代码。

响应代码|响应名称|说明
----------------|-----------|--------
301|已永久移动|此状态代码将未经授权的用户重定向到在 Location 标头中指定的 URL。
302|已找到|此状态代码将未经授权的用户重定向到在 Location 标头中指定的 URL。 此状态代码是执行重定向操作的行业标准方法。
307|临时重定向|此状态代码将未经授权的用户重定向到在 Location 标头中指定的 URL。
401|未授权|若要提示用户进行身份验证，可将此状态代码与 WWW-Authenticate 响应标头相结合。
403|禁止|这是未经授权的用户在尝试访问受保护的内容时会看到的标准“403 禁止访问”状态消息。
404|找不到文件|此状态代码表示 HTTP 客户端可以与服务器通信，但找不到请求的内容。

#### <a name="url-redirection"></a>URL 重定向

在配置为返回 3xx 状态代码时，此功能支持将 URL 重定向到用户定义的 URL。 可通过执行以下步骤指定此用户定义 URL：

1. 针对“令牌身份验证拒绝代码”功能选择 3xx 响应代码。
2. 从“可选标头名称”选项中选择“Location”。
3. 将“可选标头值”选项设置为所需的 URL。

如果未为 3xx 状态代码定义 URL，则会为用户返回 3xx 状态代码的标准响应页。

URL 重定向仅适用于 3xx 响应代码。

“可选标头值”选项支持字母数字字符、引号和空格。

#### <a name="authentication"></a>身份验证

此功能允许系统在响应未经授权的请求（请求的是基于令牌的身份验证所保护的内容）时包括 WWW-Authenticate 标头。 如果已在配置中将 WWW-Authenticate 标头设置为“基本”，则会提示未经授权的用户输入帐户凭据。

以上配置可通过执行以下步骤实现：

1. 针对“令牌身份验证拒绝代码”功能选择“401”作为响应代码。
2. 从“可选标头名称”选项中选择“WWW-Authenticate”。
3. 将“可选标头值”选项设置为“基本”。

WWW-Authenticate 标头仅适用于 401 响应代码。

### <a name="token-auth-ignore-url-case"></a>令牌身份验证忽略 URL 大小写
**目的：**确定通过基于令牌的身份验证进行的 URL 比较是否区分大小写。

受此功能影响的参数如下：

- ec_url_allow
- ec_ref_allow
- ec_ref_deny

有效值是：

值|结果
---|----
Enabled|导致边缘服务器在比较基于令牌的身份验证参数的 URL 时忽略大小写。
已禁用|还原默认行为。 默认行为是在针对令牌身份验证进行 URL 比较时区分大小写。

**默认行为：**禁用。
 
### <a name="token-auth-parameter"></a>令牌身份验证参数
**目的：**确定是否应重命名基于令牌的身份验证查询字符串参数。

重要信息：

- “值”选项所定义的查询字符串参数名称可用于指定令牌。
- 无法将“值”选项设置为“ec_token”。
- 请确保在“值”选项中定义的名称仅 
- 包含有效的 URL 字符。

值|结果
----|----
Enabled|“值”选项所定义的查询字符串参数名称应该用于定义令牌。
已禁用|可将令牌指定为请求 URL 中未定义的查询字符串参数。

**默认行为：**禁用。 可将令牌指定为请求 URL 中未定义的查询字符串参数。

## <a name="caching"></a>缓存

这些功能旨在自定义内容的缓存时间和方式。

Name | 目的
-----|--------
带宽参数 | 确定是否会启用带宽限制参数（即 ec_rate 和 ec_prebuf）。
带宽限制 | 限制边缘服务器提供的响应的带宽。
绕过缓存 | 确定请求是否可以利用我们的缓存技术。
Cache-Control 标头处理 | 在“外部最大有效期”功能启用时，控制边缘服务器生成 Cache-Control 标头。
Cache-Key 查询字符串 | 确定 cache-key 是否会包括与请求关联的查询字符串参数。
Cache-Key 重写 | 重写与请求关联的 cache-key。
完成缓存填充 | 确定当请求导致边缘服务器部分缓存未命中时会发生什么情况。
压缩文件类型 | 定义将在服务器上压缩的文件的格式。
默认的内部最大有效期 | 确定在进行从边缘服务器到源服务器的缓存重新验证时，默认的最大有效期时间间隔。
Expires 标头处理 | 在“外部最大有效期”功能启用时，控制边缘服务器生成 Expires 标头。
外部最大有效期 | 确定在进行从浏览器到边缘服务器的缓存重新验证时的最大有效期时间间隔。
强制内部最大有效期 | 确定在进行从边缘服务器到源服务器的缓存重新验证时的最大有效期时间间隔。
H.264 支持（HTTP 渐进式下载） | 确定适用于流式处理内容的 H.264 文件格式的类型。
遵循 No-Cache 请求 | 确定是否会将 HTTP 客户端的 no-cache 请求转发到源服务器。
忽略源服务器 No-Cache | 确定 CDN 是否会忽略源服务器提供的某些指令。
忽略无法满足的范围 | 确定当请求生成“416 无法满足请求的范围”状态代码时，会为客户端返回的响应。
内部最大过时期限 | 控制在边缘服务器无法重新验证源服务器的缓存资产的情况下，允许边缘服务器在正常到期时间过后多长时间内提供缓存资产。
部分缓存共享 | 确定请求是否可以生成部分缓存的内容。
预验证缓存内容 | 确定缓存内容在其 TTL 到期之前是否适合进行早期重新验证。
刷新零字节缓存文件 | 确定边缘服务器如何处理 HTTP 客户端要求提供 0 字节缓存资产的请求。
设置“可缓存”状态代码 | 定义一组允许进行内容缓存的状态代码。
在出错时交付过时的内容 | 确定在缓存重新验证时出错或者在从客户源服务器检索请求内容时出错的情况下，是否交付到期的缓存内容。
在重新验证时交付过时的内容 | 允许边缘服务器在重新验证时将过时的客户端内容提供给请求者，以便提高性能。
注释 | 注释功能允许在规则中添加注释。

###<a name="bandwidth-parameters"></a>带宽参数
**目的：**确定是否会启用带宽限制参数（即 ec_rate 和 ec_prebuf）。

带宽限制参数决定了客户端请求的数据传输速率是否会受自定义速率的限制。

值|结果
--|--
Enabled|允许边缘服务器遵循带宽限制请求。
已禁用|导致边缘服务器忽略带宽限制参数。 请求的内容将正常提供（即没有带宽限制）。

**默认行为：**启用。

###<a name="bandwidth-throttling"></a>带宽限制
**目的：**限制边缘服务器提供的响应的带宽。

若要正确设置带宽限制，下面的两个选项都必须定义。

选项|说明
--|--
每秒千字节数|将此选项设置为可以用来提供响应的最大带宽 (Kb/s)。
预缓存秒数|将此选项设置为边缘服务器在限制带宽之前会等待的秒数。 在此时间段内不限制带宽，目的是防止媒体播放器因带宽限制而出现中断或缓冲问题。

**默认行为：**禁用。

###<a name="bypass-cache"></a>绕过缓存
**目的：**确定请求是否可以利用我们的缓存技术。

值|结果
--|--
Enabled|导致所有请求被转到源服务器，即使此前已在边缘服务器上缓存了内容。
已禁用|导致边缘服务器根据响应标头中定义的缓存策略缓存资产。

**默认行为：**

- **HTTP Large：**禁用
<!---
- **ADN:** Enabled
--->
###<a name="cache-control-header-treatment"></a>Cache-Control 标头处理
**目的：**在“外部最大有效期”功能启用时，控制边缘服务器生成 Cache-Control 标头。

若要实现此类配置，最简单的方式是将“外部最大有效期”和“Cache-Control 标头处理”功能置于同一语句中。

值|结果
--|--
覆盖|确保会执行以下操作：<br/> - 覆盖源服务器生成的 Cache-Control 标头。 <br/>- 向响应添加“外部最大有效期”功能生成的 Cache-Control 标头。
传递|确保不向响应添加“外部最大有效期”功能生成的 Cache-Control 标头。 <br/> 如果源服务器生成 Cache-Control 标头，该标头会传递给最终用户。 <br/> 如果源服务器不生成 Cache-Control 标头，则此选项可能会导致响应标头不包含 Cache-Control 标头。
缺失情况下添加|如果 Cache-Control 标头不是从源服务器接收的，则此选项会添加“外部最大有效期”功能生成的 Cache-Control 标头。 此选项用于确保为所有资产分配 Cache-Control 标头。
删除| 此选项可确保标头响应不包括 Cache-Control 标头。 如果已分配 Cache-Control 标头，则会将其从标头响应中去除。

**默认行为：**覆盖。

###<a name="cache-key-query-string"></a>Cache-Key 查询字符串
**目的：**确定 cache-key 是否会包括与请求关联的查询字符串参数。

重要信息：

- 指定一个或多个查询字符串参数名称。 每个参数名称都应使用单个空格进行分隔。
- 此功能决定了 cache-key 中是否会包括查询字符串参数。 以下每个选项都提供了详细信息。

类型|说明
--|--
 包括|  表示 cache-key 中应包括每个指定的参数。 将为每个包含唯一值的请求生成唯一 cache-key，该唯一值针对此功能中定义的查询字符串参数。 
 全部包括  |表示将为每个发送到资产的请求生成唯一 cache-key，该资产包括唯一查询字符串。 通常不建议此类配置，因为此类配置可能导致缓存命中百分比低。 这样会增加源服务器的负载，因为该服务器必须处理更多请求。 此配置复制“Query-String 缓存”页上名为“unique-cache”的缓存行为。 
 排除 | 表示只会从 cache-key 中排除指定的参数。 所有其他查询字符串参数将包括在 cache-key 中。 
 全部排除  |表示将会从 cache-key 中排除所有查询字符串参数。 此配置复制“Query-String 缓存”页上名为“standard-cache”的默认缓存行为。 

利用 HTTP 规则引擎，用户可以自定义查询字符串缓存的实现方式。 例如，可以指定仅针对特定位置或文件类型执行查询字符串缓存。

若要复制“Query-String 缓存”页上名为“no-cache”的查询字符串缓存行为，则需创建一项规则，其中包含“URL 查询通配符”匹配条件和“绕过缓存”功能。 “URL 查询通配符”匹配条件应设置为星号 (*)。

#### <a name="sample-scenarios"></a>示例方案

下面提供了此功能的示例用法。 下面提供了示例请求和默认 cache-key。

- **示例请求：**http://wpc.0001.&lt;域&gt;/800001/Origin/folder/asset.htm?sessionid=1234&language=EN&userid=01
- **默认 cache-key：**/800001/Origin/folder/asset.htm

##### <a name="include"></a>包括

示例配置：

- **类型：**包括
- **参数：**language

此类配置会生成以下查询字符串参数 cache-key：

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="include-all"></a>全部包括

示例配置：

- **类型：**全部包括

此类配置会生成以下查询字符串参数 cache-key：

    /800001/Origin/folder/asset.htm?sessionid=1234&language=EN&userid=01

##### <a name="exclude"></a>排除

示例配置：

- **类型：**排除
- **参数：**sessionid、userid

此类配置会生成以下查询字符串参数 cache-key：

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="exclude-all"></a>全部排除

示例配置：

- **类型：**全部排除

此类配置会生成以下查询字符串参数 cache-key：

    /800001/Origin/folder/asset.htm

###<a name="cache-key-rewrite"></a>Cache-Key 重写
**目的：**重写与请求关联的 cache-key。

cache-key 是一个相对路径，用于确定缓存的资产。 换言之，我们的服务器会根据 cache-key 所定义的路径查看缓存版的资产。

同时定义以下两个选项即可配置此功能：

选项|说明
--|--
原始路径| 定义要重新其 cache-key 的请求类型的相对路径。 可以先选择基础源路径，然后定义一个正则表达式模式，从而定义相对路径。
新建路径|定义新 cache-key 的相对路径。 可以先选择基础源路径，然后定义一个正则表达式模式，从而定义相对路径。 可以使用 HTTP 变量动态构造此相对路径
**默认行为：**请求的 cache-key 取决于请求 URI。

###<a name="complete-cache-fill"></a>完成缓存填充
**目的：**确定当请求导致边缘服务器部分缓存未命中时会发生什么情况。

部分缓存未命中描述的是未完全下载到边缘服务器的资产的缓存状态。 如果资产仅部分缓存在边缘服务器上，则会将下一个针对该资产的请求再次转发到源服务器。
<!---
This feature is not available for the ADN platform. The typical traffic on this platform consists of relatively small assets. The size of the assets served through these platforms helps mitigate the effects of partial cache misses, since the next request will typically result in the asset being cached on that POP.
--->
部分缓存未命中通常发生在用户中止下载之后，或者发生在单纯使用 HTTP 范围请求来请求资产的情况下。 此功能最适用于用户在下载时通常会半途而废的大型资产（例如视频）。 因此，HTTP Large 平台会默认启用此功能。 所有其他平台都禁用此功能。

建议保留 HTTP Large 平台的默认配置，因为这样会减少客户源服务器的负载，提高客户下载你的内容的速度。

考虑到缓存设置的跟踪方式，不能将此功能与以下匹配条件关联：边缘 Cname、请求标头文本、请求标头通配符、URL 查询文本，以及 URL 查询通配符。

值|结果
--|--
Enabled|还原默认行为。 默认行为是强制边缘服务器启动对源服务器中资产的后台获取。 然后，资产将位于边缘服务器的本地缓存中。
已禁用|防止边缘服务器执行资产的后台获取操作。 这意味着，下次从该区域请求该资产时，会导致边缘服务器从客户源服务器请求该资产。

**默认行为：**启用。

###<a name="compress-file-types"></a>压缩文件类型
**目的：**定义将在服务器上压缩的文件的格式。

文件格式可以通过其 Internet 媒体类型（即 Content-Type）指定。 Internet 媒体类型是独立于平台的元数据，服务器可以利用它来确定特定资产的文件格式。 下面提供了常见 Internet 媒体类型的列表。

Internet 媒体类型|说明
--|--
text/plain|纯文本文件
text/html| HTML 文件
text/css|级联样式表 (CSS)
application/x-javascript|Javascript
application/javascript|Javascript
重要信息：

- 指定多个 Internet 媒体类型时，可使用单个空格分隔每个类型。 
- 此功能将只压缩大小不到 1 MB 的资产。 我们的服务器不会压缩更大的资产。
- 某些类型的内容，例如图像、视频和音频媒体资产（JPG、MP3、MP4 等）已经压缩。 再对这些类型的资产进行压缩不会显著减少文件大小。 因此，建议不要对这些类型的资产启用压缩。
- 不支持通配符，例如星号。
- 向规则添加此功能之前，请确保在将要应用此规则的平台的“压缩”页上设置“禁用压缩”选项。

###<a name="default-internal-max-age"></a>默认的内部最大有效期
**目的：**确定在进行从边缘服务器到源服务器的缓存重新验证时，默认的最大有效期时间间隔。 也即在边缘服务器查看缓存资产是否与源服务器上存储的资产匹配之前需等待的时间。

重要信息：

- 执行此操作只是为了获得未在 Cache-Control 或 Expires 标头中分配最大有效期指示的源服务器的响应。
- 对于那些被视为无法缓存的资产，不会执行此操作。
- 此操作不影响从浏览器到边缘服务器的缓存重新验证。 这些类型的重新验证取决于发送给浏览器的 Cache-Control 或 Expires 标头，此类标头可以通过“外部最大有效期”功能自定义。
- 此操作的结果对于从内容所在的边缘服务器返回的响应标头和内容没有明显的影响，但可能会影响从边缘服务器发送到源服务器的重新验证流量。
- 通过以下方式配置此功能：
    - 选择可以为其应用默认内部最大有效期的状态代码。
    - 指定一个整数值，然后选择所需的时间单位（即秒、分钟、小时等）。 此值定义默认的内部最大有效期时间间隔。

- 将时间单位设置为“关”时，会为其 Cache-Control 或 Expires 标头中尚未分配最大有效期指示的请求分配默认的内部最大有效期时间间隔，即 7 天。
- 考虑到缓存设置的跟踪方式，不能将此功能与以下匹配条件关联： 
    - Edge 
    - Cname
    - 请求标头文本
    - 请求标头通配符
    - 请求方法
    - URL 查询文本
    - URL 查询通配符

**默认值：**7 天

###<a name="expires-header-treatment"></a>Expires 标头处理
**目的：**在“外部最大有效期”功能启用时，控制边缘服务器生成 Expires 标头。

若要实现此类配置，最简单的方式是将“外部最大有效期”和“Expires 标头处理”功能置于同一语句中。

值|结果
--|--
覆盖|确保会执行以下操作：<br/>- 覆盖源服务器生成的 Expires 标头。<br/>- 向响应添加“外部最大有效期”功能生成的 Expires 标头。
传递|确保不向响应添加“外部最大有效期”功能生成的 Expires 标头。 <br/> 如果源服务器生成 Expires 标头，该标头会传递给最终用户。 <br/>如果源服务器不生成 Expires 标头，则此选项可能会导致响应标头不包含 Expires 标头。
缺失情况下添加| 如果 Expires 标头不是从源服务器接收的，则此选项会添加“外部最大有效期”功能生成的 Expires 标头。 此选项用于确保为所有资产分配 Expires 标头。
删除| 确保标头响应不包括 Expires 标头。 如果已分配 Expires 标头，则会将其从标头响应中去除。

**默认行为：**覆盖

###<a name="external-max-age"></a>外部最大有效期
**目的：**确定在进行从浏览器到边缘服务器的缓存重新验证时的最大有效期时间间隔。 也即在浏览器查看边缘服务器中是否存在新版资产之前需等待的时间。

启用此功能时，会在边缘服务器中生成 Cache-Control:max-age 和 Expires 标头并将其发送到 HTTP 客户端。 默认情况下，这些标头会覆盖源服务器创建的标头。 但是，可以使用“Cache-Control 标头处理”和“Expires 标头处理”功能更改此行为。

重要信息：

- 此操作不影响从边缘服务器到源服务器的缓存重新验证。 这些类型的重新验证取决于从源服务器接收的 Cache-Control/Expires 标头，可以通过“默认的内部最大有效期”和“强制内部最大有效期”功能自定义。
- 配置此功能时，可以指定一个整数值，然后选择所需的时间单位（即秒、分钟、小时等）。
- 将此功能设置为负值时，会导致边缘服务器将过去针对每个响应设置的 Cache-Control:no-cache 和 Expires 时间发送至浏览器。 虽然 HTTP 客户端不会缓存响应，但此设置不会影响边缘服务器缓存源服务器响应的功能。
- 将时间单位设置为“关”会禁用此功能。 缓存在源服务器响应中的 Cache-Control/Expires 标头会传递给浏览器。

**默认行为：**关

###<a name="force-internal-max-age"></a>强制内部最大有效期
**目的：**确定在进行从边缘服务器到源服务器的缓存重新验证时的最大有效期时间间隔。 也即在边缘服务器查看缓存资产是否与源服务器上存储的资产匹配之前需等待的时间。

重要信息：

- 此功能将重写在源服务器生成的 Cache-Control 或 Expires 标头中定义的最大有效期时间间隔。
- 此功能不影响从浏览器到边缘服务器的缓存重新验证。 这些类型的重新验证取决于发送给浏览器的 Cache-Control 或 Expires 标头。
- 此功能对边缘服务器发送给请求者的响应没有明显的影响， 但可能会影响从边缘服务器发送到源服务器的重新验证流量。
- 通过以下方式配置此功能：
    - 选择将为其应用内部最大有效期的状态代码。
    - 指定一个整数值，然后选择所需的时间单位（即秒、分钟、小时等）。 此值定义请求的最大有效期时间间隔。

- 将时间单位设置为“关”会禁用此功能。 不会将内部最大有效期时间间隔分配给请求的资产。 如果原始标头不包含缓存指令，则会根据“默认的内部最大有效期”功能中的有效设置对资产进行缓存。
- 考虑到缓存设置的跟踪方式，不能将此功能与以下匹配条件关联： 
    - Edge 
    - Cname
    - 请求标头文本
    - 请求标头通配符
    - 请求方法
    - URL 查询文本
    - URL 查询通配符

**默认行为：**关

###<a name="h264-support-http-progressive-download"></a>H.264 支持（HTTP 渐进式下载）
**目的：**确定适用于流式处理内容的 H.264 文件格式的类型。

重要信息：

- 在“文件扩展名”选项中定义一组使用空格分隔的、系统允许的 H.264 文件扩展名。 “文件扩展名”选项将重写默认行为。 通过在设置此选项时包括这些文件扩展名，保留对 MP4 和 F4V 的支持。 
- 确保在指定每个文件扩展名（例如 .mp4、.f4v）时包括句点。

**默认行为：**HTTP 渐进式下载默认支持 MP4 和 F4V 媒体。

###<a name="honor-no-cache-request"></a>遵循 no-cache 请求
**目的：**确定是否会将 HTTP 客户端的 no-cache 请求转发到源服务器。

当 HTTP 客户端在 HTTP 请求中发送 Cache-Control:no-cache 和/或 Pragma:no-cache 标头时，就会出现 no-cache 请求。

值|结果
--|--
Enabled|允许将 HTTP 客户端的 no-cache 请求转发给源服务器，然后源服务器就会将响应标头和正文通过边缘服务器返回给 HTTP 客户端。
已禁用|还原默认行为。 默认行为是为了防止系统将 no-cache 请求转发到源服务器。

对于所有生产流量，强烈建议将此功能保留为默认禁用状态。 否则，如果最终用户在刷新网页时无意触发多个 no-cache 请求，或者多个常用媒体播放器根据编码在每次进行视频请求时都发送 no-cache 标头，源服务器就会受到影响。 尽管如此，仍可将此功能应用到某些非生产性的分段或测试目录，以便根据需要从源服务器拉取全新的内容。

对于因为此功能而获允转发到源服务器的请求，将要报告的缓存状态为 TCP_Client_Refresh_Miss。 核心报告模块中提供的缓存状态报告按缓存状态提供统计信息。 用户可以据此跟踪根据此功能而需要转发到源服务器的请求的数目和百分比。

**默认行为：**禁用。

###<a name="ignore-origin-no-cache"></a>忽略源服务器 no-cache
**目的：**确定 CDN 是否会忽略源服务器提供的以下指令：

- Cache-Control: private
- Cache-Control: no-store
- Cache-Control: no-cache
- Pragma: no-cache

重要信息：

- 配置此功能时，可以定义一个空格分隔的列表，其中包含的状态代码对应于需要忽略的上述指令。
- 此功能的有效状态代码包括：200、203、300、301、302、305、307、400、401、402、403、404、405、406、407、408、409、410、411、412、413、414、415、416、417、500、501、502、503、504、505。
- 禁用此功能的方法是将其设置为空值。
- 考虑到缓存设置的跟踪方式，不能将此功能与以下匹配条件关联： 
    - Edge 
    - Cname
    - 请求标头文本
    - 请求标头通配符
    - 请求方法
    - URL 查询文本
    - URL 查询通配符

**默认行为：**默认行为是遵循上述指令。

###<a name="ignore-unsatisfiable-ranges"></a>忽略无法满足的范围 
**目的：**确定当请求生成“416 无法满足请求的范围”状态代码时，会为客户端返回的响应。

默认情况下，当边缘服务器无法满足指定的 byte-range 请求时，以及当 If-Range 请求标头字段未被指定时，将会返回此状态代码。

值|结果
-|-
Enabled|防止我们的边缘服务器使用“416 无法满足请求的范围”状态代码响应无效的 byte-range 请求。 我们的服务器会改为交付请求的资产并为客户端返回“200 正常”。
已禁用|还原默认行为。 默认行为是遵循“416 无法满足请求的范围”状态代码。

**默认行为：**禁用。

###<a name="internal-max-stale"></a>内部最大过时期限
**目的：**控制在边缘服务器无法重新验证源服务器的缓存资产的情况下，允许边缘服务器在正常到期时间过后多长时间内提供缓存资产。

通常情况下，当资产的最大有效期时间到期时，边缘服务器会向源服务器发送重新验证请求。 然后，源服务器会使用“304 未修改”进行响应，为边缘服务器提供缓存资产的全新租约，或者使用“200 正常”进行响应，为边缘服务器提供更新版的缓存资产。

如果边缘服务器在尝试此类重新验证时无法建立与源服务器的连接，则此“内部最大过时期限”功能会控制是否允许边缘服务器继续提供现已过时的资产，以及在多长时间内提供。

请注意，此时间间隔是在资产的最大有效期到期时开始的，而不是在重新验证失败时开始的。 因此，不需重新验证成功即可提供资产的最大时段是指组合使用最大有效期和最大过时期限指定的时间段。 例如，假设资产已在 9:00 进行缓存，最大有效期为 30 分钟，最大过时期限为 15 分钟，这种情况下，如果在 9:44 进行的重新验证尝试失败，则会导致最终用户收到过时的缓存资产；如果在 9:46 进行的重新验证尝试失败，则会导致最终用户收到“504 网关超时”。

为此功能配置的任何值都会被从源服务器收到的 Cache-Control:must-revalidate 或 Cache-Control:proxy-revalidate 标头取代。 如果在初次缓存资产时从源服务器收到了这其中的一个标头，则边缘服务器不会提供过时的缓存资产。 这种情况下，如果边缘服务器在资产的最大有效期时间间隔到期后无法通过源服务器重新进行验证，则边缘服务器会返回“504 网关超时”。

重要信息：

- 通过以下方式配置此功能：
    - 选择将为其应用最大过时期限的状态代码。
    - 指定一个整数值，然后选择所需的时间单位（即秒、分钟、小时等）。 此值定义将要应用的内部最大过时期限。

- 将时间单位设置为“关”会禁用此功能。 在正常的到期时间过后，将不会提供缓存资产。
- 考虑到缓存设置的跟踪方式，不能将此功能与以下匹配条件关联： 
    - Edge 
    - Cname
    - 请求标头文本
    - 请求标头通配符
    - 请求方法
    - URL 查询文本
    - URL 查询通配符

**默认行为：**2 分钟

###<a name="partial-cache-sharing"></a>部分缓存共享
**目的：**确定请求是否可以生成部分缓存的内容。

然后，可以使用这个部分缓存履行对该内容的新请求，直到所请求的内容完全缓存。

值|结果
-|-
Enabled|请求可以生成部分缓存的内容。
已禁用|请求只能生成所请求内容的完全缓存版本。

**默认行为：**禁用。

###<a name="prevalidate-cached-content"></a>预验证缓存内容
**目的：**确定缓存内容在其 TTL 到期之前是否适合进行早期重新验证。

定义在所请求内容的 TTL 到期之前的时间段，在此期间可以进行早期重新验证。

重要信息：

- 选择“关”作为时间单位时，需在缓存内容的 TTL 到期之后重新进行验证。 不应指定时间，时间会被忽略。

**默认行为：**关。 只能在缓存内容的 TTL 到期后，才能重新进行验证。

###<a name="refresh-zero-byte-cache-files"></a>刷新零字节缓存文件
**目的：**确定边缘服务器如何处理 HTTP 客户端要求提供 0 字节缓存资产的请求。

有效值是：

值|结果
--|--
Enabled|导致边缘服务器重新获取源服务器的资产。
已禁用|还原默认行为。 默认行为是在收到请求后提供有效的缓存资产。
此功能不是正确地进行缓存和内容交付所必需的，但可用作一种解决方法。 例如，源服务器上的动态内容生成器可能会意外地导致 0 字节响应被发送至边缘服务器。 这些类型的响应通常由边缘服务器缓存。 如果确定 0 字节响应不是此类内容 

的有效响应，则可利用此功能防止系统将这些类型的资产提供给客户端。

**默认行为：**禁用。

###<a name="set-cacheable-status-codes"></a>设置“可缓存”状态代码
**目的：**定义一组允许进行内容缓存的状态代码。

默认情况下，仅为“200 正常”响应启用缓存。

定义一组所需的、空格分隔的状态代码。

重要信息：

- 另请启用“忽略源服务器 No-Cache”功能。 如果未启用该功能，则不会缓存“非 200 正常”响应。
- 此功能的有效状态代码包括：203、300、301、302、305、307、400、401、402、403、404、405、406、407、408、409、410、411、412、413、414、415、416、417、500、501、502、503、504、505。
- 对于生成“200 正常”状态代码的响应，不能通过此功能禁用缓存。

**默认行为：**仅为生成“200 正常”状态代码的响应启用缓存。
###<a name="stale-content-delivery-on-error"></a>在出错时交付过时的内容
**目的：** 

确定在缓存重新验证时出错或者在从客户源服务器检索请求内容时出错的情况下，是否交付到期的缓存内容。

值|结果
-|-
Enabled|如果在连接到源服务器的过程中发生错误，则会向请求者提供过时的内容。
已禁用|源服务器的错误将转发给请求者。

**默认行为：**禁用

###<a name="stale-while-revalidate"></a>在重新验证时交付过时的内容
**目的：**允许边缘服务器在重新验证时将过时的内容提供给请求者，以便提高性能。

重要信息：

- 此功能的行为因所选时间单位而异。
    - **时间单位：**指定时间长度并选择时间单位（例如秒、分钟、小时等），以便交付过时的内容。 此类设置允许 CDN 延长时间长度，以便在需要验证之前交付内容，所依据的公式如下：**TTL** + **在重新验证时交付过时内容的时间** 
    - **关：**选择“关”表示在处理针对过时内容的请求之前需重新验证。
        - 请勿指定时间长度，因为时间长度不适用，会被系统忽略。

**默认行为：**关。 在提交请求的内容之前，必须重新进行验证。

###<a name="comment"></a>注释
**目的：**允许在规则中添加注释。

此功能的一个用途是提供其他信息，说明规则的常规用途或者向规则添加具体匹配条件或功能的原因。

重要信息：

- 最多可以指定 150 个字符。
- 请确保仅使用字母数字字符。
- 此功能不影响规则的行为。 它只是用于为未来的引用提供信息，或者用于对规则进行故障诊断。
 
## <a name="headers"></a>标头

以下功能旨在添加、修改或删除请求或响应中的标头。

Name | 目的
-----|--------
Age 响应标头 | 确定是否会在发送给请求者的响应中包括 Age 响应标头。
调试缓存响应标头 | 确定是否在响应中包括 X-EC-Debug 响应标头，用于说明所请求资产的缓存策略。
修改客户端请求标头 | 覆盖、追加或删除请求的标头。
修改客户端响应标头 | 覆盖、追加或删除响应的标头。
设置客户端 IP 自定义标头 | 允许将请求客户端的 IP 地址作为自定义请求标头添加到请求。

###<a name="age-response-header"></a>Age 响应标头
**目的**：确定是否会在发送给请求者的响应中包括 Age 响应标头。
值|结果
--|--
Enabled | 将在发送给请求者的响应中包括 Age 响应标头。
已禁用 | Age 响应标头将不包括在发送给请求者的响应中。

**默认行为：**禁用。

###<a name="debug-cache-response-headers"></a>调试缓存响应标头
**目的：**确定是否在响应中包括 X-EC-Debug 响应标头，用于说明所请求资产的缓存策略。

当以下两个条件均为 ture 时，调试缓存响应标头将包括在响应中：

- 已针对所需请求启用“调试缓存响应标头”功能。
- 上述请求所定义的一组调试缓存响应标头将包括在响应中。

请求调试缓存响应标头时，可以将以下标头和所需指令包括在请求中：

X-EC-Debug: _Directive1_,_Directive2_,_DirectiveN_

**示例：**

X-EC-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state

值|结果
-|-
Enabled|请求调试缓存响应标头时，会返回包括 X-EC-Debug 标头的响应。
已禁用|X-EC-Debug 响应标头将不包括在响应中。

**默认行为：**禁用。

###<a name="modify-client-response-header"></a>修改客户端响应标头
**目的：**每个请求包含一组用于描述该请求的 [请求标头]()。 此功能可以：

- 追加或覆盖分配给请求标头的值。 如果指定的请求标头不存在，则可使用此功能将其添加到请求。
- 从请求中删除请求标头。

转发给源服务器的请求会反映通过此功能所做的更改。

可以对请求标头执行以下操作之一：

选项|说明|示例
-|-|-
附加|指定的值将添加到现有请求标头值的末尾。|**请求标头值（客户端）：**Value1 <br/> **请求标头值（HTTP 规则引擎）：**Value2 <br/>**新的请求标头值：**Value1Value2
覆盖|请求标头值将设置为指定的值。|**请求标头值（客户端）：**Value1 <br/>**请求标头值（HTTP 规则引擎）：**Value2 <br/>**新的请求标头值：**Value2 <br/>
删除|删除指定的请求标头。|**请求标头值（客户端）：**Value1 <br/> **修改客户端请求标头配置：**删除相关的请求标头。 <br/>**结果：**指定的请求标头不会转发给源服务器。

重要信息：

- 请确保在“名称”选项中指定的值与所需请求标头完全匹配。
- 在标识标头时不考虑大小写。 例如，可以使用 Cache-Control 标头名称的任何下述变体来标识该标头：
    - cache-control
    - CACHE-CONTROL
    - cachE-Control
- 请确保在指定标头名称时仅使用字母数字字符、短划线或下划线。
- 删除标头即可防止边缘服务器将其转发给源服务器。
- 以下标头为保留标头，不能通过此功能进行修改：
    - forwarded
    - 主机
    - via
    - 警告
    - x-forwarded-for
    - 所有以“x-ec”开头的标头名称均为保留名称。

###<a name="modify-client-response-header"></a>修改客户端响应标头
每个响应都包含一组用于描述该响应的 [响应标头]()。 此功能可以：

- 追加或覆盖分配给响应标头的值。 如果指定的请求标头不存在，则可使用此功能将其添加到响应。
- 从响应中删除响应标头。

默认情况下，由源服务器和边缘服务器定义响应标头值。

可以对响应标头执行以下操作之一：

选项|说明|示例
-|-|-
附加|指定的值将添加到现有请求标头值的末尾。|**响应标头值（客户端）：**Value1 <br/> **响应标头值（HTTP 规则引擎）：**Value2 <br/>**新的响应标头值：**Value1Value2
覆盖|请求标头值将设置为指定的值。|**响应标头值（客户端）：**Value1 <br/>**响应标头值（HTTP 规则引擎）：**Value2 <br/>**新的响应标头值：**Value2 <br/>
删除|删除指定的请求标头。|**请求标头值（客户端）：**Value1 <br/> **修改客户端请求标头配置：**删除相关的响应标头。 <br/>**结果：**指定的响应标头不会转发给请求者。

重要信息：

- 请确保在“名称”选项中指定的值与所需响应标头完全匹配。 
- 在标识标头时不考虑大小写。 例如，可以使用 Cache-Control 标头名称的任何下述变体来标识该标头：
    - cache-control
    - CACHE-CONTROL
    - cachE-Control
- 删除标头即可防止系统将其转发给请求者。
- 以下标头为保留标头，不能通过此功能进行修改：
    - accept-encoding
    - age
    - 连接
    - content-encoding
    - content-length
    - content-range
    - 日期
    - server
    - trailer
    - transfer-encoding
    - 升级
    - vary
    - via
    - 警告
    - 所有以“x-ec”开头的标头名称均为保留名称。

###<a name="set-client-ip-custom-header"></a>设置客户端 IP 自定义标头
**目的：**向请求添加自定义标头，以便按 IP 地址标识请求客户端。

“标头名称”选项用于定义自定义请求标头（将在其中存储客户端的 IP 地址）的名称。

此功能允许客户源服务器通过自定义请求标头查找客户端 IP 地址。 如果请求是从缓存提供的，则不会将客户端的 IP 地址告知源服务器。 因此，建议将此功能与 ADN 或不会进行缓存的资产配合使用。

请确保指定的标头名称与任何下述项都不匹配：

- 标准请求标头名称。 可以在 [RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html) 中找到标准标头名称的列表。
- 保留的标头名称：
    - forwarded-for
    - 主机
    - vary
    - via
    - 警告
    - x-forwarded-for
    - 所有以“x-ec”开头的标头名称均为保留名称。
 
## <a name="logs"></a>日志

这些功能旨在自定义存储在原始日志文件中的数据。

Name | 目的
-----|--------
自定义日志字段 1 | 确定将要在原始日志文件中分配给自定义日志字段的格式和内容。
日志查询字符串 | 确定是否会将查询字符串和 URL 一起存储在访问日志中。

###<a name="custom-log-field-1"></a>自定义日志字段 1
**目的：**确定将要在原始日志文件中分配给自定义日志字段的格式和内容。

此自定义字段的主要目的是确定要存储在日志文件中的请求和响应标头值。

默认情况下，自定义日志字段称为“x-ec_custom-1”。 但是，可以在[“原始日志设置”页]()中自定义该字段的名称。

应该用来指定请求和响应标头的格式定义如下：

标头类型|格式|示例
-|-|-
请求标头|%{[RequestHeader]()}[i]() | %{Accept-Encoding}i <br/> {Referer}i <br/> %{Authorization}i
响应标头|%{[ResponseHeader]()}[o]()| %{Age}o <br/> %{Content-Type}o <br/> %{Cookie}o

重要信息：

- 自定义日志字段可能包含标头字段和纯文本的任意组合。
- 此字段的有效字符包括：字母数字（即 0-9、a-z 和 A-Z）、短划线、冒号、分号、撇号、逗号、句点、下划线、等号、括号、方括号、空格。 百分比符号和大括号只能用于指定标头字段。
- 每个指定的标头字段的拼写必须与所需的请求/响应标头名称匹配。
- 若要指定多个标头，则建议使用分隔符来指示每个标头。 例如，可以对每个标头使用缩写。 示例语法提供如下。
    - AE: %{Accept-Encoding}i A: %{Authorization}i CT: %{Content-Type}o 

**默认值：** -

###<a name="log-query-string"></a>日志查询字符串
**目的：**确定是否会将查询字符串和 URL 一起存储在访问日志中。

值|结果
-|-
Enabled|在访问日志中记录 URL 时，允许存储查询字符串。 如果 URL 中不含查询字符串，则此选项无效。
已禁用|还原默认行为。 默认行为是将 URL 记录到访问日志中时忽略查询字符串。

**默认行为：**禁用。

<!---
## Optimize

These features determine whether a request will undergo the optimizations provided by Edge Optimizer.

Name | Purpose
-----|--------
Edge Optimizer | Determines whether Edge Optimizer can be applied to a request.
Edge Optimizer – Instantiate Configuration | Instantiates or activates the Edge Optimizer configuration associated with a site.

###Edge Optimizer
**Purpose:** Determines whether Edge Optimizer can be applied to a request.

If this feature has been enabled, then the following criteria must also be met before the request will be processed by Edge Optimizer:

- The requested content must use an edge CNAME URL.
- The edge CNAME referenced in the URL must correspond to a site whose configuration has been activated in a rule.

This feature requires the ADN platform and the Edge Optimizer feature.

Value|Result
-|-
Enabled|Indicates that the request is eligible for Edge Optimizer processing.
Disabled|Restores the default behavior. The default behavior is to deliver content over the ADN platform without any additional processing.

**Default Behavior:** Disabled
 

###Edge Optimizer - Instantiate Configuration
**Purpose:** Instantiates or activates the Edge Optimizer configuration associated with a site.

This feature requires the ADN platform and the Edge Optimizer feature.

Key information:

- Instantiation of a site configuration is required before requests to the corresponding edge CNAME can be processed by Edge Optimizer.
- This instantiation only needs to be performed a single time per site configuration. A site configuration that has been instantiated will remain in that state until the Edge Optimizer – Instantiate Configuration feature that references it is removed from the rule.
- The instantiation of a site configuration does not mean that all requests to the corresponding edge CNAME will automatically be processed by Edge Optimizer. The Edge Optimizer feature determines whether an individual request will be processed.

If the desired site does not appear in the list, then you should edit its configuration and verify that the Active option has been marked.

**Default Behavior:** Site configurations are inactive by default.
--->

## <a name="origin"></a>源

以下功能旨在控制 CDN 与源服务器的通信方式。

Name | 目的
-----|--------
最大 Keep-Alive 请求数 | 定义 Keep-Alive 连接在关闭前的最大请求数。
代理特殊标头 | 定义一组特定于 CDN 的请求标头，这些标头将从边缘服务器转发给源服务器。


###<a name="maximum-keep-alive-requests"></a>最大 Keep-Alive 请求数
**目的：**定义 Keep-Alive 连接在关闭前的最大请求数。

强烈建议不要将最大请求数设置过低，否则会导致性能下降。

重要信息：

- 将此值指定为整数。
- 不要在指定的值中包括逗号或句点。

**默认值：**10,000 个请求

###<a name="proxy-special-headers"></a>代理特殊标头
**目的：**定义一组[特定于 CDN 的请求标头]()，这些标头将从边缘服务器转发给源服务器。

重要信息：

- 此功能中定义的每个特定于 CDN 的请求标头都会转发给源服务器。
- 将特定于 CDN 的请求标头从此列表中删除即可防止系统将其转发给源服务器。

**默认行为：**所有[特定于 CDN 的请求标头]()都会转发给源服务器。

## <a name="specialty"></a>特殊功能

以下功能属高级功能，仅供高级用户使用。

Name | 目的
-----|--------
可缓存的 HTTP 方法 | 确定一组可以在我们的网络上缓存的其他 HTTP 方法。
可缓存请求正文大小 | 定义的阈值用于确定 POST 响应是否可以缓存。

###<a name="cacheable-http-methods"></a>可缓存的 HTTP 方法
**目的：**确定一组可以在我们的网络上缓存的其他 HTTP 方法。

重要信息：

- 此功能假定应始终缓存 GET 响应。 因此，在设置此功能时，不应包括 GET HTTP 方法。
- 此功能仅支持 POST HTTP 方法。 将此功能设置为 :POST 即可启用 POST 响应缓存 
- 默认情况下，仅缓存正文小于 14 Kb 的请求。 使用“可缓存请求正文大小”功能设置请求正文的最大大小。

**默认行为：**仅缓存 GET 响应。

###<a name="cacheable-request-body-size"></a>可缓存请求正文大小

**目的：**定义的阈值用于确定 POST 响应是否可以缓存。

通过指定请求正文的最大大小来确定此阈值。 不会缓存所含请求正文超出此大小的请求。

重要信息：

- 此功能仅适用于 POST 响应可以进行缓存的情况。 使用“可缓存的 HTTP 方法”功能可启用 POST 请求缓存。
- 以下情况需考虑请求正文：
    - x-www-form-urlencoded 值
    - 确保 cache-key 的唯一
- 将请求正文的最大大小定义得过大可能影响数据交付性能。
    - **建议值：**14 Kb
    - **最小值：**1 Kb

**默认行为：**14 Kb
 
## <a name="url"></a>代码

以下功能允许将请求重定向到其他 URL 或者将其重写。

Name | 目的
-----|--------
遵循重定向 | 确定是否可以将请求重定向到在客户源服务器返回的 Location 标头中定义的主机名。
URL 重定向 | 通过 Location 标头重定向请求。
URL 重写  | 重写请求 URL。

###<a name="follow-redirects"></a>遵循重定向
**目的：**确定是否可以将请求重定向到在客户源服务器返回的 Location 标头中定义的主机名。

重要信息：

- 只能将请求重定向到与同一平台相对应的边缘 CNAME。

值|结果
-|-
Enabled|可以重定向请求。
已禁用|不会重定向请求。

**默认行为：**禁用。
###<a name="url-redirect"></a>URL 重定向
**目的：**通过 Location 标头重定向请求。

此功能的配置需要设置以下选项：

选项|说明
-|-
代码|选择会返回给请求者的响应代码。
源和模式| 这些设置定义的请求 URI 模式用于标识可重定向请求的类型。 只会重定向其 URL 同时满足下述两个条件的请求： <br/> <br/> **源**（或内容访问点）：选择用于标识源服务器的相对路径。 该路径是“/XXXX/”部分和终结点名。 <br/> **源（模式）：**必须定义一个可通过相对路径标识请求的模式。 此正则表达式模式必须定义一个路径，该路径直接开始于以前选择的内容访问点（见上）之后。 <br/> - 确保上面定义的请求 URI 条件（即源和模式）不与为此功能定义的任何匹配条件冲突。 <br/> - 确保指定模式。 使用空值作为模式只会将请求匹配到所选源服务器的根文件夹（例如 http://cdn.mydomain.com/）。
目标| 定义要将上述请求重定向到的 URL。 <br/> 通过以下方式动态构造此 URL： <br/> - 正则表达式模式 <br/>- HTTP 变量 <br/> 使用 $_n_ 将源模式中捕获的值替换到目标模式中，其中 _n_ 用于按捕获顺序来标识值。 例如，$1 代表按源模式捕获的第一个值，而 $2 则代表第二个值。 <br/> 
强烈建议使用绝对 URL。 使用相对 URL 可能会将 CDN URL 重定向到无效的路径。

**示例方案**

在此示例中，我们将演示如何重定向可解析成以下基 CDN URL 的边缘 CNAME URL：http://marketing.azureedge.net/brochures

符合条件的请求将重定向到以下基边缘 CNAME URL：http://cdn.mydomain.com/resources

该 URL 重定向可通过以下配置实现：![](./media/cdn-rules-engine-reference/cdn-rules-engine-redirect.png)

**要点：**

- “URL 重定向”功能定义将重定向的请求 URL。 因此，不需要其他匹配条件。 虽然匹配条件被定义为“始终”，但只会重定向指向“marketing”客户源服务器上“brochures”文件夹的请求。 
- 所有匹配的请求都会重定向到“目标”选项中定义的边缘 CNAME URL。 
    - 示例方案 1： 
        - 示例请求 (CDN URL)：http://marketing.azureedge.net/brochures/widgets.pdf 
        - 请求 URL（重定向后）：http://cdn.mydomain.com/resources/widgets.pdf  
    - 示例方案 2： 
        - 示例请求（边缘 CNAME URL）：http://marketing.mydomain.com/brochures/widgets.pdf 
        - 请求 URL（重定向后）：http://cdn.mydomain.com/resources/widgets.pdf  示例方案
    - 示例方案 3： 
        - 示例请求（边缘 CNAME URL）：http://brochures.mydomain.com/campaignA/final/productC.ppt 
        - 请求 URL（重定向后）：http://cdn.mydomain.com/resources/campaignA/final/productC.ppt  
- “请求方案”(%{scheme}) 变量已在“说明”选项中使用。 这可确保请求的方案在重定向后保持不变。
- 从请求中捕获的 URL 段通过“$1”追加到新的 URL。
 
###<a name="url-rewrite"></a>URL 重写
**目的：**重写请求 URL。

重要信息：

- 此功能的配置需要设置以下选项：

选项|说明
-|-
 源和模式 | 这些设置定义的请求 URI 模式用于标识可重写请求的类型。 只会重写其 URL 同时满足下述两个条件的请求： <br/>     - **源（或内容访问点）：**选择用于标识源服务器的相对路径。 该路径是“/XXXX/”部分和终结点名。 <br/> - **源（模式）：**必须定义一个可通过相对路径标识请求的模式。 此正则表达式模式必须定义一个路径，该路径直接开始于以前选择的内容访问点（见上）之后。 <br/> 确保上面定义的请求 URI 条件（即源和模式）不与为此功能定义的任何匹配条件冲突。 确保指定模式。 使用空值作为模式只会将请求匹配到所选源服务器的根文件夹（例如 http://cdn.mydomain.com/）。 
 目标  |定义要通过其将上述请求重写的相对 URL： <br/>    1.选择用于标识源服务器的内容访问点。 <br/>    2.使用以下方式定义相对路径： <br/>        - 正则表达式模式 <br/>        - HTTP 变量 <br/> <br/> 使用 $_n_ 将源模式中捕获的值替换到目标模式中，其中 _n_ 用于按捕获顺序来标识值。 例如，$1 代表按源模式捕获的第一个值，而 $2 则代表第二个值。 
 此功能允许边缘服务器重写 URL，而不需执行传统的重定向。 这意味着，请求者会收到与请求重写 URL 相同的响应代码。

**示例方案**

###<a name="compatibility"></a>兼容性

此功能包含的匹配条件必须满足，然后才能将其应用到请求。 为了防止设置相冲突的匹配条件，此功能不兼容以下匹配条件：

- AS 编号
- CDN 源
- 客户端 IP 地址
- 客户源
- 请求方案
- URL 路径目录
- URL 路径扩展名
- URL 路径文件名
- URL 路径文本
- URL 路径正则表达式
- URL 路径通配符
- URL 查询文本
- URL 查询参数
- URL 查询正则表达式
- URL 查询通配符


## <a name="next-steps"></a>后续步骤
* [规则引擎参考](cdn-rules-engine-reference.md)
* [规则引擎条件表达式](cdn-rules-engine-reference-conditional-expressions.md)
* [规则引擎的匹配条件](cdn-rules-engine-reference-match-conditions.md)
* [使用规则引擎重写默认 HTTP 行为](cdn-rules-engine.md)
* [Azure CDN 概述](cdn-overview.md)



<!--HONumber=Dec16_HO3-->


