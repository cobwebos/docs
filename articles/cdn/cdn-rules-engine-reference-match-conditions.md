---
title: Azure CDN 规则引擎匹配条件 | Microsoft Docs
description: 有关 Azure 内容交付网络规则引擎匹配条件的参考文档。
services: cdn
documentationcenter: ''
author: Lichard
manager: akucer
editor: ''
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2017
ms.author: rli
ms.openlocfilehash: f8dac5469e7160fae93e8251ab7f4195a383f8b4
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2018
ms.locfileid: "30173315"
---
# <a name="azure-cdn-rules-engine-match-conditions"></a>Azure CDN 规则引擎匹配条件 
本文列出了 Azure 内容交付网络 (CDN) [规则引擎](cdn-rules-engine.md)的可用匹配条件的详细说明。

规则的第二部分是匹配条件。 可以使用匹配条件来确定特定类型的请求，以便执行一组相应的功能。

例如，可以使用匹配条件：
- 筛选对特定位置处的内容的请求。
- 筛选从特定 IP 地址或国家/地区生成的请求。
- 按标头信息筛选请求。

## <a name="always-match-condition"></a>“始终”匹配条件

“始终”匹配条件向所有请求应用默认的一组功能。

名称 | 目的
-----|--------
[始终](#always) | 向所有请求应用默认的一组功能。

## <a name="device-match-condition"></a>“设备”匹配条件

“设备”匹配条件用于根据属性标识来自移动设备的请求。  

名称 | 目的
-----|--------
[设备](#device) | 根据设备属性标识来自移动设备的请求。

## <a name="location-match-conditions"></a>“位置”匹配条件

“位置”匹配条件根据请求者的位置标识请求。

名称 | 目的
-----|--------
[AS 编号](#as-number) | 标识源自特定网络的请求。
[国家/地区](#country) | 标识源自指定国家/地区的请求。

## <a name="origin-match-conditions"></a>“源”匹配条件

“源”匹配条件标识指向内容交付网络存储或客户源服务器的请求。

名称 | 目的
-----|--------
[CDN 源](#cdn-origin) | 标识对存储在内容交付网络存储中的内容的请求。
[客户源](#customer-origin) | 标识存储在特定客户源服务器上的内容的请求。

## <a name="request-match-conditions"></a>“请求”匹配条件

“请求”匹配条件根据属性标识请求。

名称 | 目的
-----|--------
[客户端 IP 地址](#client-ip-address) | 标识源自特定 IP 地址的请求。
[Cookie 参数](#cookie-parameter) | 查看与每个针对指定值的请求关联的 Cookie。
[Cookie 参数正则表达式](#cookie-parameter-regex) | 查看与每个针对指定正则表达式的请求关联的 Cookie。
[边缘 Cname](#edge-cname) | 标识指向特定的边缘 CNAME 的请求。
[引用域](#referring-domain) | 标识从指定主机名引用的请求。
[请求标头文本](#request-header-literal) | 标识所含的指定标头已设置为指定值的请求。
[请求标头正则表达式](#request-header-regex) | 标识所含的指定标头已设置为特定值的请求，该特定值与指定的正则表达式相符。
[请求标头通配符](#request-header-wildcard) | 标识所含的指定标头已设置为特定值的请求，该特定值与指定的模式相符。
[请求方法](#request-method) | 按 HTTP 方法标识请求。
[请求方案](#request-scheme) | 按 HTTP 协议标识请求。

## <a name="url-match-conditions"></a>“URL”匹配条件

“URL”匹配条件根据 URL 标识请求。

名称 | 目的
-----|--------
[URL 路径目录](#url-path-directory) | 按相对路径标识请求。
[URL 路径扩展名](#url-path-extension) | 按文件扩展名标识请求。
[URL 路径文件名](#url-path-filename) | 按文件名标识请求。
[URL 路径文本](#url-path-literal) | 将请求的相对路径与指定值比较。
[URL 路径正则表达式](#url-path-regex) | 将请求的相对路径与指定正则表达式比较。
[URL 路径通配符](#url-path-wildcard) | 将请求的相对路径与指定模式比较。
[URL 查询文本](#url-query-literal) | 将请求的查询字符串与指定值比较。
[URL 查询参数](#url-query-parameter) | 标识所含的指定查询字符串参数已设置为特定值的请求，该特定值与指定的模式相符。
[URL 查询正则表达式](#url-query-regex) | 标识所含的指定查询字符串参数已设置为特定值的请求，该特定值与指定的正则表达式相符。
[URL 查询通配符](#url-query-wildcard) | 将指定值与请求的查询字符串比较。


## <a name="reference-for-rules-engine-match-conditions"></a>规则引擎匹配条件参考

---
### <a name="always"></a>始终

“始终”匹配条件向所有请求应用默认的一组功能。

[返回页首](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="as-number"></a>AS 编号 
AS 编号网络由其自治系统编号 (ASN) 定义。 

“匹配”/“不匹配”选项用于确定在哪种条件下满足 AS 编号匹配条件：
- **匹配**：要求客户端网络的 ASN 与指定的 ASN 之一匹配。 
- **不匹配**：要求客户端网络的 ASN 不与任何指定的 ASN 匹配。

重要信息：
- 指定多个 ASN 时，可使用单个空格分隔每个 ASN。 例如，64514 64515 匹配来自 64514 或 64515 的请求。
- 某些请求可能不返回有效的 ASN。 问号 (?) 将匹配无法确定有效 ASN 的请求。
- 为所需网络指定整个 ASN。 将不匹配部分值。
- 考虑到缓存设置的跟踪方式，此匹配条件与以下功能不兼容：
  - 完成缓存填充
  - 默认的内部最大有效期
  - 强制内部最大有效期
  - 忽略源服务器 No-Cache
  - 内部最大过时期限

[返回页首](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="cdn-origin"></a>CDN 源
如果满足以下两个条件，则满足“CDN 源”匹配条件：
- 已从 CDN 存储请求内容。
- 请求 URI 使用此匹配条件中定义的内容访问点类型（例如，/000001）：
  - CDN URL：请求 URI 必须包含所选内容访问点。
  - 边缘 CNAME URL：相应的边缘 CNAME 配置必须指向所选内容访问点。
  
重要信息：
 - 内容访问点标识应处理请求内容的服务。
 - 请勿使用 AND IF 语句组合某些匹配条件。 例如，将“CDN 源”匹配条件与“客户源”匹配条件组合会创建一个永远无法匹配的匹配模式。 为此，不能通过 AND IF 语句组合两个“CDN 源”匹配条件。

[返回页首](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="client-ip-address"></a>客户端 IP 地址
“匹配”/“不匹配”选项用于确定在哪种条件下满足客户端 IP 地址匹配条件：
- **匹配**：要求客户端的 IP 地址与指定的 IP 地址之一匹配。 
- **匹配**：要求客户端的 IP 地址不与任何指定的 IP 地址匹配。 

重要信息：
- 使用 CIDR 表示法。
- 指定多个 IP 地址和/或 IP 地址块时，可使用单个空格分隔每个地址和/或地址块。 例如：
  - **IPv4 示例**：1.2.3.4 10.20.30.40 匹配来自地址 1.2.3.4 或 10.20.30.40 的所有请求。
  - **IPv6 示例**：1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 匹配来自地址 1:2:3:4:5:6:7:8 或 10:20:30:40:50:60:70:80 的所有请求。
- IP 地址块的语法为 IP 基址后跟正斜杠和前缀大小。 例如：
  - **IPv4 示例**：5.5.5.64/26 匹配来自地址 5.5.5.64 到 5.5.5.127 的所有请求。
  - **IPv6 示例**：1:2:3:/48 匹配来自地址 1:2:3:0:0:0:0:0 到 1:2:3:ffff:ffff:ffff:ffff:ffff 的所有请求。
- 考虑到缓存设置的跟踪方式，此匹配条件与以下功能不兼容：
  - 完成缓存填充
  - 默认的内部最大有效期
  - 强制内部最大有效期
  - 忽略源服务器 No-Cache
  - 内部最大过时期限

[返回页首](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="cookie-parameter"></a>Cookie 参数
“匹配”/“不匹配”选项用于确定在哪种条件下满足 Cookie 参数匹配条件。
- **匹配**：需要请求包含指定的 Cookie，其值与此匹配条件中定义的至少一个值匹配。
- **不匹配**：要求请求满足以下条件之一：
  - 它不包含指定的 Cookie。
  - 它包含指定的 Cookie，但其值与此匹配条件中定义的任何值都不匹配。
  
重要信息：
- Cookie 名称： 
  - 由于指定 Cookie 名称时不支持通配符值，包括星号 (*)，因此，只有完全匹配的 Cookie 名称才能进行比较。
  - 此匹配条件的每个实例只能指定一个 Cookie 名称。
  - Cookie 名称比较不区分大小写。
- Cookie 值： 
  - 指定多个 Cookie 值时，可使用单个空格分隔每个值。
  - Cookie 值可以利用[通配符值](cdn-rules-engine-reference.md#wildcard-values)。 
  - 如果未指定通配符值，则只有完全匹配项才满足此匹配条件。 例如，指定“Value”将匹配“Value”，而不是“Value1”或“Value2”。
  - 使用“忽略大小写”选项可以控制是否对请求的 Cookie 值进行区分大小写的比较。
- 考虑到缓存设置的跟踪方式，此匹配条件与以下功能不兼容：
  - 完成缓存填充
  - 默认的内部最大有效期
  - 强制内部最大有效期
  - 忽略源服务器 No-Cache
  - 内部最大过时期限

[返回页首](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="cookie-parameter-regex"></a>Cookie 参数正则表达式
“Cookie 参数正则表达式”匹配条件定义 Cookie 名称和值。 可以使用[正则表达式](cdn-rules-engine-reference.md#regular-expressions)定义所需的 Cookie 值。 

“匹配”/“不匹配”选项用于确定在哪种条件下满足 Cookie 参数正则表达式匹配条件。
- **匹配**：需要请求包含指定的 Cookie，其值与指定的正则表达式匹配。
- **不匹配**：要求请求满足以下条件之一：
  - 它不包含指定的 Cookie。
  - 它包含指定的 Cookie，但其值与指定的正则表达式不匹配。
  
重要信息：
- Cookie 名称： 
  - 由于指定 Cookie 名称时不支持正则表达式和通配符值，包括星号 (*)，因此，只有完全匹配的 Cookie 名称才能进行比较。
  - 此匹配条件的每个实例只能指定一个 Cookie 名称。
  - Cookie 名称比较不区分大小写。
- Cookie 值： 
  - Cookie 值可以利用正则表达式。
  - 使用“忽略大小写”选项可以控制是否对请求的 Cookie 值进行区分大小写的比较。
- 考虑到缓存设置的跟踪方式，此匹配条件与以下功能不兼容：
  - 完成缓存填充
  - 默认的内部最大有效期
  - 强制内部最大有效期
  - 忽略源服务器 No-Cache
  - 内部最大过时期限

[返回页首](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

--- 
### <a name="country"></a>国家/地区
可以通过国家/地区代码指定国家/地区。 

“匹配”/“不匹配”选项用于确定在哪种条件下满足国家/地区匹配条件：
- **匹配**：要求请求包含指定的国家/地区代码值。 
- **不匹配**：要求请求不包含指定的国家/地区代码值。

重要信息：
- 指定多个国家/地区代码时，可使用单个空格分隔每个代码。
- 指定国家/地区代码时不支持通配符。
- “EU”和“AP”国家/地区代码不包括这些区域中的所有 IP 地址。
- 某些请求可能不返回有效的国家/地区代码。 问号 (?) 将匹配无法确定有效国家/地区代码的请求。
- 国家/地区代码区分大小写。
- 考虑到缓存设置的跟踪方式，此匹配条件与以下功能不兼容：
  - 完成缓存填充
  - 默认的内部最大有效期
  - 强制内部最大有效期
  - 忽略源服务器 No-Cache
  - 内部最大过时期限

#### <a name="implementing-country-filtering-by-using-the-rules-engine"></a>使用规则引擎实现国家/地区筛选
使用此匹配条件可以基于请求的发起位置执行多种自定义。 例如，可通过以下配置复制国家/地区筛选功能的行为：

- URL 路径通配符匹配：将 [URL 路径通配符匹配条件](#url-path-wildcard)设置为要保护的目录。 
    在相对路径的末尾追加一个星号可确保对其所有子级的访问受此规则的限制。

- 国家/地区匹配：将国家/地区匹配条件设置为所需的国家/地区集。
   - 允许：将国家/地区匹配条件设置为“不匹配”，以只允许指定的国家/地区访问 URL 路径通配符匹配条件定义的位置中存储的内容。
   - 阻止：将国家/地区匹配条件设置为“匹配”，以阻止指定的国家/地区访问 URL 路径通配符匹配条件定义的位置中存储的内容。

- 拒绝访问 (403) 功能：启用[拒绝访问 (403) 功能](cdn-rules-engine-reference-features.md#deny-access-403)，以复制国家/地区筛选功能的允许或阻止部分。

[返回页首](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="customer-origin"></a>客户源

重要信息： 
- 无论通过 CDN URL 还是通过指向所选客户源的边缘 CNAME URL 请求内容，都满足客户源匹配条件。
- 无法从“客户源”页删除被某个规则引用的客户源配置。 尝试删除客户源配置之前，请确保以下配置未引用它：
  - “客户源”匹配条件
  - 边缘 CNAME 配置
- 请勿使用 AND IF 语句组合某些匹配条件。 例如，将“客户源”匹配条件与“CDN 源”匹配条件组合会创建一个永远无法匹配的匹配模式。 为此，不能通过 AND IF 语句组合两个“客户源”匹配条件。

[返回页首](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="device"></a>设备

“设备”匹配条件用于根据属性标识来自移动设备的请求。 可通过 [WURFL](http://wurfl.sourceforge.net/) 进行移动设备检测。 

“匹配”/“不匹配”选项用于确定在哪种条件下满足设备匹配条件：
- **匹配**：要求请求方的设备匹配指定的值。 
- **不匹配**：要求请求方的设备不匹配指定的值。

重要信息：

- 使用“忽略大小写”选项来指定指定的值是否区分大小写。
- 考虑到缓存设置的跟踪方式，此匹配条件与以下功能不兼容：
  - 完成缓存填充
  - 默认的内部最大有效期
  - 强制内部最大有效期
  - 忽略源服务器 No-Cache
  - 内部最大过时期限

#### <a name="string-type"></a>字符串类型
WURFL 功能通常接受数字、字母和符号的任意组合。 由于此功能的灵活性，必须选择如何解释与此匹配条件关联的值。 下表描述了可用的选项集：

Type     | 说明
---------|------------
文本  | 选择此选项可防止大多数字符使用其[文本值](cdn-rules-engine-reference.md#literal-values)产生特殊的含义。
通配符 | 选择此选项可以利用所有 [通配符]（[通配符值](cdn-rules-engine-reference.md#wildcard-values)）。
正则表达式    | 选择此选项可以使用[正则表达式](cdn-rules-engine-reference.md#regular-expressions)。 正则表达式可用于定义字符模式。

#### <a name="wurfl-capabilities"></a>WURFL 功能
WURFL 功能引用描述移动设备的类别。 选定的功能确定用于识别请求的移动设备说明类型。

下表列出了规则引擎的 WURFL 功能及其变量。
<br>
> [!NOTE] 
> “修改客户端请求标头”和“修改客户端响应标头”功能支持以下变量。

功能 | 变量 | 说明 | 示例值
-----------|----------|-------------|----------------
品牌名称 | %{wurfl_cap_brand_name} | 一个字符串，表示设备的品牌名称。 | Samsung
设备 OS | %{wurfl_cap_device_os} | 一个字符串，表示设备上安装的操作系统。 | IOS
设备 OS 版本 | %{wurfl_cap_device_os_version} | 一个字符串，表示设备上安装的操作系统的版本号。 | 1.0.1
双方向 | %{wurfl_cap_dual_orientation} | 一个布尔值，表示设备是否支持双方向。 | 是
HTML 首选 DTD | %{wurfl_cap_html_preferred_dtd} | 一个字符串，表示移动设备针对 HTML 内容的首选文档类型定义 (DTD)。 | 无<br/>xhtml_basic<br/>html5
图像内联 | %{wurfl_cap_image_inlining} | 一个布尔值，表示设备是否支持 Base64 编码图像。 | false
是 Android | %{wurfl_vcap_is_android} | 一个布尔值，表示设备是否使用 Android OS。 | 是
是 IOS | %{wurfl_vcap_is_ios} | 一个布尔值，表示设备是否使用 iOS。 | false
是智能电视 | %{wurfl_cap_is_smarttv} | 一个布尔值，表示设备是否为智能电视。 | false
是智能手机 | %{wurfl_vcap_is_smartphone} | 一个布尔值，表示设备是否为智能手机。 | 是
是平板电脑 | %{wurfl_cap_is_tablet} | 一个布尔值，表示设备是否为平板电脑。 此说明独立于 OS。 | 是
是无线设备 | %{wurfl_cap_is_wireless_device} | 一个布尔值，表示是否可将设备视为无线设备。 | 是
市场营销名称 | %{wurfl_cap_marketing_name} | 一个字符串，表示设备的市场营销名称。 | BlackBerry 8100 Pearl
移动浏览器 | %{wurfl_cap_mobile_browser} | 一个字符串，表示用于从设备请求内容的浏览器。 | Chrome
移动浏览器版本 | %{wurfl_cap_mobile_browser_version} | 一个字符串，表示用于从设备请求内容的浏览器的版本。 | 31
模型名称 | %{wurfl_cap_model_name} | 一个字符串，表示设备的模型名称。 | s3
渐进式下载 | %{wurfl_cap_progressive_download} | 一个布尔值，表示设备是否允许音频和视频边下载边播放。 | 是
发布日期 | %{wurfl_cap_release_date} | 一个字符串，表示设备在何年何月添加到 WURFL 数据库。<br/><br/>格式：`yyyy_mm` | 2013_december
垂直分辨率 | %{wurfl_cap_resolution_height} | 一个整数，表示设备以像素为单位的高度。 | 768
水平分辨率 | %{wurfl_cap_resolution_width} | 一个整数，表示设备以像素为单位的宽度。 | 1024

[返回页首](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="edge-cname"></a>边缘 Cname
重要信息： 
- 可用边缘 CNAME 列表仅包含与正在配置规则引擎的平台相对应的“边缘 CNAME”页上配置的边缘 CNAME。
- 尝试删除边缘 CNAME 配置之前，请确保“边缘 Cname”匹配条件未引用它。 无法从“边缘 CNAME”页删除已在某个规则中定义的边缘 CNAME 配置。 
- 请勿使用 AND IF 语句组合某些匹配条件。 例如，将“边缘 Cname”匹配条件与“客户源”匹配条件组合会创建一个永远无法匹配的匹配模式。 为此，不能通过 AND IF 语句组合两个“边缘 Cname”匹配条件。
- 考虑到缓存设置的跟踪方式，此匹配条件与以下功能不兼容：
  - 完成缓存填充
  - 默认的内部最大有效期
  - 强制内部最大有效期
  - 忽略源服务器 No-Cache
  - 内部最大过时期限

[返回页首](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="referring-domain"></a>引用域
与用于请求内容的引用域关联的主机名可确定是否满足“引用域”条件。 

“匹配”/“不匹配”选项用于确定在哪种条件下满足引用域匹配条件：
- **匹配**：要求引用主机名匹配指定的值。 
- **不匹配**：要求引用主机名不匹配指定的值。

重要信息：
- 指定多个主机名时，可使用单个空格分隔每个主机名。
- 此匹配条件支持[通配符值](cdn-rules-engine-reference.md#wildcard-values)。
- 如果指定值不包含星号，则它必须与引用域的主机名完全匹配。 例如，指定“mydomain.com”不会匹配“www.mydomain.com”。
- 使用“忽略大小写”选项可以控制是否进行区分大小写的比较。
- 考虑到缓存设置的跟踪方式，此匹配条件与以下功能不兼容：
  - 完成缓存填充
  - 默认的内部最大有效期
  - 强制内部最大有效期
  - 忽略源服务器 No-Cache
  - 内部最大过时期限

[返回页首](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---  
### <a name="request-header-literal"></a>请求标头文本
“匹配”/“不匹配”选项用于确定在哪种条件下满足请求标头文本匹配条件。
- **匹配**：需要请求包含指定的标头。 其值必须与此匹配条件中定义的值匹配。
- **不匹配**：要求请求满足以下条件之一：
  - 它不包含指定的标头。
  - 它包含指定的标头，但其值与此匹配条件中定义的值不匹配。
  
重要信息：
- 标头名称比较始终不区分大小写。 使用“忽略大小写”选项可以控制标头值的区分大小写比较。
- 考虑到缓存设置的跟踪方式，此匹配条件与以下功能不兼容：
  - 完成缓存填充
  - 默认的内部最大有效期
  - 强制内部最大有效期
  - 忽略源服务器 No-Cache
  - 内部最大过时期限

[返回页首](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---  
### <a name="request-header-regex"></a>请求标头正则表达式
“匹配”/“不匹配”选项用于确定在哪种条件下满足请求标头正则表达式匹配条件。
- **匹配**：需要请求包含指定的标头。 其值必须与指定的[正则表达式](cdn-rules-engine-reference.md#regular-expressions)中定义的模式匹配。
- **不匹配**：要求请求满足以下条件之一：
  - 它不包含指定的标头。
  - 它包含指定的标头，但其值与指定的正则表达式不匹配。

重要信息：
- 标头名称： 
  - 标头名称比较不区分大小写。
  - 将标头名称中的空格替换为“%20”。 
- 标头值： 
  - 标头值可以利用正则表达式。
  - 使用“忽略大小写”选项可以控制标头值的区分大小写比较。
  - 仅当标头值与至少一个指定的模式完全匹配时，才满足此条件。
- 考虑到缓存设置的跟踪方式，此匹配条件与以下功能不兼容：
  - 完成缓存填充
  - 默认的内部最大有效期
  - 强制内部最大有效期
  - 忽略源服务器 No-Cache
  - 内部最大过时期限 

[返回页首](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="request-header-wildcard"></a>请求标头通配符
“匹配”/“不匹配”选项用于确定在哪种条件下满足请求标头通配符匹配条件。
- **匹配**：需要请求包含指定的标头。 其值必须与此匹配条件中定义的至少一个值匹配。
- **不匹配**：要求请求满足以下条件之一：
  - 它不包含指定的标头。
  - 它包含指定的标头，但其值与任何指定值都不匹配。
  
重要信息：
- 标头名称： 
  - 标头名称比较不区分大小写。
  - 标头名称中的空格应替换为“%20”。 也可以使用此值指定标头值中的空格。
- 标头值： 
  - 标头值可以利用[通配符值](cdn-rules-engine-reference.md#wildcard-values)。
  - 使用“忽略大小写”选项可以控制标头值的区分大小写比较。
  - 当标头值与至少一个指定的模式完全匹配时满足此条件。
  - 指定多个值时，可使用单个空格分隔每个值。
- 考虑到缓存设置的跟踪方式，此匹配条件与以下功能不兼容：
  - 完成缓存填充
  - 默认的内部最大有效期
  - 强制内部最大有效期
  - 忽略源服务器 No-Cache
  - 内部最大过时期限

[返回页首](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="request-method"></a>请求方法
仅当通过选定请求方法请求资产时，才满足请求方法匹配条件。 可用请求方法包括：
- GET
- HEAD 
- POST 
- OPTIONS 
- PUT 
- 删除 
- TRACE 
- CONNECT 

重要信息：
- 默认情况下，只有 GET 请求方法可在网络上生成缓存内容。 其他所有请求方法均通过网络代理。
- 考虑到缓存设置的跟踪方式，此匹配条件与以下功能不兼容：
  - 完成缓存填充
  - 默认的内部最大有效期
  - 强制内部最大有效期
  - 忽略源服务器 No-Cache
  - 内部最大过时期限

[返回页首](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="request-scheme"></a>请求方案
仅当通过选定协议请求资产时，才满足请求方案匹配条件。 可用的协议为： 
- HTTP
- HTTPS

重要信息：
- 考虑到缓存设置的跟踪方式，此匹配条件与以下功能不兼容：
  - 完成缓存填充
  - 默认的内部最大有效期
  - 强制内部最大有效期
  - 忽略源服务器 No-Cache
  - 内部最大过时期限

[返回页首](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-path-directory"></a>URL 路径目录
按相对路径（不包括所请求资产的文件名）标识请求。

“匹配”/“不匹配”选项用于确定在哪种条件下满足 URL 路径目录匹配条件。
- **匹配**：要求请求包含与指定的 URL 模式匹配的相对 URL 路径（不包括文件名）。
- **不匹配**：要求请求包含与指定的 URL 模式不匹配的相对 URL 路径（不包括文件名）。

重要信息：
- 使用“相对于”选项可以指定是要在内容访问点的前面还是后面开始比较 URL。 内容访问点是显示在 Verizon CDN 主机名与所请求资产的相对路径（例如 /800001/CustomerOrigin）之间的路径部分。 它按服务器类型（例如 CDN 或客户来源）和客户帐号标识某个位置。

   “相对于”选项可以使用以下值：
   - **根**：表示 URL 比较点紧接在 CDN 主机名后面开始。 

     例如：http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder**/index.htm

   - **源**：表示 URL 比较点在内容访问点后面开始（例如 /000001 或 /800001/myorigin）。 由于 \*.azureedge.net CNAME 默认是在相对于 Verizon CDN 主机名上源目录的位置创建的，因此，Azure CDN 用户应使用“源”值。 

     例如：https:\//&lt;endpoint&gt;.azureedge.net/**myfolder**/index.htm 

     此 URL 指向以下 Verizon CDN 主机名：http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder**/index.htm

- 在 URL 比较之前，边缘 CNAME URL 将重写入 CDN URL。

    例如，以下两个 URL 指向同一个资产，因此具有相同的 URL 路径。
    - CDN URL：\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
    
    - 边缘 CNAME URL：http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    其他信息：
    - 自定义域：https:\//my.domain.com/path/asset.htm
    
    - URL 路径（相对于根）：/800001/CustomerOrigin/path/
    
    - URL 路径（相对于源）：/path/

- 用于 URL 比较的 URL 部分紧靠在所请求资产的文件名的前面结束。 尾随的正斜杠是此路径类型中的最后一个字符。
    
- 将 URL 路径模式中的所有空格替换为“%20”。
    
- 每个 URL 路径模式可以包含一个或多个星号 (*)，其中每个星号与包含一个或多个字符的序列匹配。
    
- 可以在模式中指定多个 URL 路径，并使用单个空格分隔每个路径。

    例如：*/sales/ */marketing/

- URL 路径规范可以利用[通配符值](cdn-rules-engine-reference.md#wildcard-values)。

- 使用“忽略大小写”选项可以控制是否执行区分大小写的比较。

[返回页首](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-path-extension"></a>URL 路径扩展名
按所请求资产的文件扩展名标识请求。

“匹配”/“不匹配”选项用于确定在哪种条件下满足 URL 路径扩展名匹配条件。
- **匹配**：要求请求的 URL 包含与指定的模式完全匹配的文件扩展名。

   例如，如果指定“htm”，则匹配“htm”资产，但不匹配“html”资产。  

- **不匹配**：要求请求的 URL 包含与指定的模式不匹配的文件扩展名。

重要信息：
- 在“值”框中指定要匹配的文件扩展名。 不要包含前导句点；例如，可以使用 htm，但不能使用 .htm。

- 使用“忽略大小写”选项可以控制是否执行区分大小写的比较。

- 可以指定多个文件扩展名，并使用单个空格分隔每个扩展名。 

    例如：htm html

- 例如，指定“htm”可匹配“htm”资产，但不匹配“html”资产。


#### <a name="sample-scenario"></a>示例方案

以下示例配置假设当某个请求与指定的扩展名匹配时满足此匹配条件。

值规范：asp aspx php html

当找到末尾为以下扩展名的 URL 时，将满足此匹配条件：
- .asp
- .aspx
- .php
- .html

[返回页首](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-path-filename"></a>URL 路径文件名
按所请求资产的文件名标识请求。 要满足此匹配条件，文件名必须由所请求资产的名称、句点和文件扩展名（例如，index.html）组成。

“匹配”/“不匹配”选项用于确定在哪种条件下满足 URL 路径文件名匹配条件。
- **匹配**：要求请求在其 URL 路径中包含与指定的模式匹配的文件名。
- **不匹配**：要求请求在其 URL 路径中包含与指定的模式不匹配的文件名。

重要信息：
- 使用“忽略大小写”选项可以控制是否执行区分大小写的比较。

- 可以指定多个文件扩展名，并使用单个空格分隔每个扩展名。

    例如：index.htm index.html

- 将文件名值中的空格替换为“%20”。
    
- 文件名值可以利用[通配符值](cdn-rules-engine-reference.md#wildcard-values)。 例如，每个文件名模式可以包含一个或多个星号 (*)，其中每个星号与包含一个或多个字符的序列匹配。
    
- 如果未指定通配符，则只有完全匹配项才满足此匹配条件。

    例如，指定“presentation.ppt”可匹配名为“presentation.ppt”的资产，但不匹配名为“presentation.pptx”的资产。

[返回页首](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-path-literal"></a>URL 路径文本
将请求的 URL 路径（包括文件名）与指定的值进行比较。

“匹配”/“不匹配”选项用于确定在哪种条件下满足 URL 路径文本匹配条件。
- **匹配**：要求请求包含与指定的模式匹配的 URL 路径。
- **不匹配**：要求请求包含与指定的模式不匹配的 URL 路径。

重要信息：
- 使用“相对于”选项可以指定 URL 比较点是在内容访问点的前面还是后面。 

    “相对于”选项可以使用以下值：
     - **根**：表示 URL 比较点紧接在 CDN 主机名后面开始。

       例如：http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder/index.htm**

     - **源**：表示 URL 比较点在内容访问点后面开始（例如 /000001 或 /800001/myorigin）。 由于 \*.azureedge.net CNAME 默认是在相对于 Verizon CDN 主机名上源目录的位置创建的，因此，Azure CDN 用户应使用“源”值。 

       例如：https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

     此 URL 指向以下 Verizon CDN 主机名：http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder/index.htm**

- 在 URL 比较之前，边缘 CNAME URL 将重写入 CDN URL。

    例如，以下两个 URL 指向同一个资产，因此具有相同的 URL 路径：
    - CDN URL：\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
    - 边缘 CNAME URL：http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    其他信息：
    
    - URL 路径（相对于根）：/800001/CustomerOrigin/path/asset.htm
   
    - URL 路径（相对于源）：/path/asset.htm

- 将忽略 URL 中的查询字符串。
- 使用“忽略大小写”选项可以控制是否执行区分大小写的比较。
- 为此匹配条件指定的值与客户端发出的确切请求的相对路径进行比较。

- 若要匹配针对特定目录发出的所有请求，请使用 [URL 路径目录](#url-path-directory)或 [URL 路径通配符](#url-path-wildcard)匹配条件。

[返回页首](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-path-regex"></a>URL 路径正则表达式
将请求的 URL 路径与指定的[正则表达式](cdn-rules-engine-reference.md#regular-expressions)进行比较。

“匹配”/“不匹配”选项用于确定在哪种条件下满足 URL 路径正则表达式匹配条件。
- **匹配**：要求请求包含与指定的正则表达式匹配的 URL 路径。
- **不匹配**：要求请求包含与指定的正则表达式不匹配的 URL 路径。

重要信息：
- 在 URL 比较之前，边缘 CNAME URL 将重写入 CDN URL。 
 
    例如，两个 URL 指向同一个资产，因此具有相同的 URL 路径。

     - CDN URL：\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm

     - 边缘 CNAME URL：http:\//my.domain.com/path/asset.htm
    
    其他信息：
    
     - URL 路径：/800001/CustomerOrigin/path/asset.htm

- 将忽略 URL 中的查询字符串。
    
- 使用“忽略大小写”选项可以控制是否执行区分大小写的比较。
    
- URL 路径中的空格应替换为“%20”。

[返回页首](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-path-wildcard"></a>URL 路径通配符
将请求的相对 URL 路径与指定的通配符模式进行比较。

“匹配”/“不匹配”选项用于确定在哪种条件下满足 URL 路径通配符匹配条件。
- **匹配**：要求请求包含与指定的通配符模式匹配的 URL 路径。
- **不匹配**：要求请求包含与指定的通配符模式不匹配的 URL 路径。

重要信息：
- “相对于”选项：此选项确定 URL 比较点是在内容访问点的前面还是后面。

   此选项可以采用以下值：
     - **根**：表示 URL 比较点紧接在 CDN 主机名后面开始。

       例如：http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder/index.htm**

     - **源**：表示 URL 比较点在内容访问点后面开始（例如 /000001 或 /800001/myorigin）。 由于 \*.azureedge.net CNAME 默认是在相对于 Verizon CDN 主机名上源目录的位置创建的，因此，Azure CDN 用户应使用“源”值。 

       例如：https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

     此 URL 指向以下 Verizon CDN 主机名：http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder/index.htm**

- 在 URL 比较之前，边缘 CNAME URL 将重写入 CDN URL。

    例如，以下两个 URL 指向同一个资产，因此具有相同的 URL 路径：
     - CDN URL：http://wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
     - 边缘 CNAME URL：http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    其他信息：
    
     - URL 路径（相对于根）：/800001/CustomerOrigin/path/asset.htm
    
     - URL 路径（相对于源）：/path/asset.htm
    
- 可以指定多个 URL 路径，并使用单个空格分隔每个路径。

   例如：/marketing/asset.* /sales/*.htm

- 将忽略 URL 中的查询字符串。
    
- 使用“忽略大小写”选项可以控制是否执行区分大小写的比较。
    
- 将 URL 路径中的空格替换为“%20”。
    
- 为 URL 路径指定的值可以利用[通配符值](cdn-rules-engine-reference.md#wildcard-values)。 每个 URL 路径模式可以包含一个或多个星号 (*)，其中每个星号可与包含一个或多个字符的序列匹配。

#### <a name="sample-scenarios"></a>示例方案

下表中的示例配置假设当某个请求与指定的 URL 模式匹配时满足此匹配条件：

值                   | 相对于    | 结果 
------------------------|----------------|-------
\*/test.html \*/test.php  | 根或源 | 针对任何文件夹中名为“test.html”或“test.php”的资产发出的请求匹配此模式。
/80ABCD/origin/text/*   | 根           | 当请求的资产满足以下条件时匹配此模式： <br />- 该资产必须位于名为“origin”的客户来源位置。 <br />- 相对路径必须以名为“text”的文件夹开头。 即，请求的资产可以位于“text”文件夹中，或者位于该文件夹的某个递归子文件夹中。
*/css/* */js/*          | 根或源 | 包含 css 或 js 文件夹的所有 CDN 或边缘 CNAME URL 都匹配此模式。
*.jpg *.gif *.png       | 根或源 | 以 .jpg、.gif 或 .png 结尾的所有 CDN 或边缘 CNAME URL 都匹配此模式。 指定此模式的一种替代方式是使用 [URL 路径扩展名匹配条件](#url-path-extension)。
/images/\* /media/\*      | 源         | 相对路径以“images”或“media”文件夹开头的 CDN 或边缘 CNAME URL 匹配此模式。 <br />- CDN URL：http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/images/sales/event1.png<br />- 示例边缘 CNAME URL：http:\//cdn.mydomain.com/images/sales/event1.png

[返回页首](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-query-literal"></a>URL 查询文本
将请求的查询字符串与指定值比较。

“匹配”/“不匹配”选项用于确定在哪种条件下满足 URL 查询文本匹配条件。
- **匹配**：要求请求包含与指定的查询字符串匹配的 URL 查询字符串。
- **不匹配**：要求请求包含与指定的查询字符串不匹配的 URL 查询字符串。

重要信息：

- 只有确切的查询字符串匹配项才满足此匹配条件。
    
- 使用“忽略大小写”选项可以控制查询字符串的区分大小写比较。
    
- 不要在查询字符串值文本中包含前导问号 (?)。
    
- 某些字符需要 URL 编码。 使用百分比符号对以下字符进行 URL 编码：

   Character | URL 编码
   ----------|---------
   空格     | %20
   &         | %25

- 考虑到缓存设置的跟踪方式，此匹配条件与以下功能不兼容：
   - 完成缓存填充
   - 默认的内部最大有效期
   - 强制内部最大有效期
   - 忽略源服务器 No-Cache
   - 内部最大过时期限

[返回页首](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-query-parameter"></a>URL 查询参数
标识包含指定查询字符串参数的请求。 此参数设置为与指定的模式匹配的值。 请求 URL 中的查询字符串参数（例如 parameter=value）确定是否满足此条件。 此匹配条件按名称标识查询字符串参数，并接受一个或多个值作为参数值。 

“匹配”/“不匹配”选项用于确定在哪种条件下满足 URL 查询参数匹配条件。
- **匹配**：要求请求包含指定的参数，其值与此匹配条件中定义的至少一个值匹配。
- **不匹配**：要求请求满足以下条件之一：
  - 它不包含指定的参数。
  - 它包含指定的参数，但其值与此匹配条件中定义的任何值都不匹配。

使用此匹配条件可以轻松指定参数名称/值组合。 若要更灵活地匹配查询字符串参数，请考虑使用 [URL 查询通配符](#url-query-wildcard)匹配条件。

重要信息：
- 对于此匹配条件的每个实例，只能指定一个 URL 查询参数名称。
    
- 由于指定了参数名称时不支持通配符值，因此，只能比较确切的参数名称匹配项。
- 参数值可以包含[通配符值](cdn-rules-engine-reference.md#wildcard-values)。
   - 每个参数值模式可以包含一个或多个星号 (*)，其中每个星号可与包含一个或多个字符的序列匹配。
   - 某些字符需要 URL 编码。 使用百分比符号对以下字符进行 URL 编码：

       Character | URL 编码
       ----------|---------
       空格     | %20
       &         | %25

- 可以指定多个查询字符串参数值，并使用单个空格分隔每个值。 当请求包含一个指定的名称/值组合时满足此匹配条件。

   - 示例 1：

     - 配置:

       ValueA ValueB

     - 此配置与以下查询字符串参数匹配：

       Parameter1=ValueA
    
       Parameter1=ValueB

   - 示例 2：

     - 配置: 

        Value%20A Value%20B

     - 此配置与以下查询字符串参数匹配：

       Parameter1=Value%20A

       Parameter1=Value%20B

- 仅当至少与一个指定的查询字符串名称/值组合完全匹配时，才满足此匹配条件。

   例如，如果在前面的示例中使用该配置，则不会将参数名称/值组合“Parameter1=ValueAdd”视为匹配项。 但是，如果指定以下值之一，则与该名称/值组合匹配：

   - ValueA ValueB ValueAdd
   - ValueA* ValueB

- 使用“忽略大小写”选项可以控制查询字符串的区分大小写比较。
    
- 考虑到缓存设置的跟踪方式，此匹配条件与以下功能不兼容：
   - 完成缓存填充
   - 默认的内部最大有效期
   - 强制内部最大有效期
   - 忽略源服务器 No-Cache
   - 内部最大过时期限

#### <a name="sample-scenarios"></a>示例方案
以下示例演示此选项在特定场合下的工作方式：

名称  | 值 |  结果
------|-------|--------
用户  | Joe   | 当所请求 URL 的查询字符串为“?user=joe”时匹配此模式。
用户  | *     | 当所请求 URL 的查询字符串包含 User 参数时匹配此模式。
Email | Joe\* | 当所请求 URL 的查询字符串包含以“Joe”开头的 Email 参数时匹配此模式。

[返回页首](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-query-regex"></a>URL 查询正则表达式
标识包含指定查询字符串参数的请求。 此参数设置为与指定的[正则表达式](cdn-rules-engine-reference.md#regular-expressions)匹配的值。

“匹配”/“不匹配”选项用于确定在哪种条件下满足 URL 查询正则表达式匹配条件。
- **匹配**：要求请求包含与指定的正则表达式匹配的 URL 查询字符串。
- **不匹配**：要求请求包含与指定的正则表达式不匹配的 URL 查询字符串。

重要信息：
- 只有指定的正则表达式的完全匹配项才满足此匹配条件。
    
- 使用“忽略大小写”选项可以控制查询字符串的区分大小写比较。
    
- 使用此选项时，查询字符串必须以查询字符串问号 (?) 分隔符后面的第一个字符开头。
    
- 某些字符需要 URL 编码。 使用百分比符号对以下字符进行 URL 编码：

   Character | URL 编码 | 值
   ----------|--------------|------
   空格     | %20          | \%20
   &         | %25          | \%25

   请注意，必须将百分比符号转义。

- 将特殊正则表达式字符双重转义（例如 \^$.+）可在正则表达式中包含反斜杠。

   例如：

   值 | 解释为 
   ------|---------------
   \\+    | +
   \\\\+   | \\+

- 考虑到缓存设置的跟踪方式，此匹配条件与以下功能不兼容：
   - 完成缓存填充
   - 默认的内部最大有效期
   - 强制内部最大有效期
   - 忽略源服务器 No-Cache
   - 内部最大过时期限


[返回页首](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-query-wildcard"></a>URL 查询通配符
将指定值与请求的查询字符串比较。

“匹配”/“不匹配”选项用于确定在哪种条件下满足 URL 查询通配符匹配条件。
- **匹配**：要求请求包含与指定的通配符值匹配的 URL 查询字符串。
- **不匹配**：要求请求包含与指定的通配符值不匹配的 URL 查询字符串。

重要信息：
- 使用此选项时，查询字符串必须以查询字符串问号 (?) 分隔符后面的第一个字符开头。
- 参数值可以包含[通配符值](cdn-rules-engine-reference.md#wildcard-values)：
   - 每个参数值模式可以包含一个或多个星号 (*)，其中每个星号可与包含一个或多个字符的序列匹配。
   - 某些字符需要 URL 编码。 使用百分比符号对以下字符进行 URL 编码：

     Character | URL 编码
     ----------|---------
     空格     | %20
     &         | %25

- 指定多个值时，可使用单个空格分隔每个值。

   例如：*Parameter1=ValueA* *ValueB* *Parameter1=ValueC&Parameter2=ValueD*

- 只有与至少一个指定的查询字符串模式完全匹配的项才满足此条件。
    
- 使用“忽略大小写”选项可以控制查询字符串的区分大小写比较。
    
- 考虑到缓存设置的跟踪方式，此匹配条件与以下功能不兼容：
   - 完成缓存填充
   - 默认的内部最大有效期
   - 强制内部最大有效期
   - 忽略源服务器 No-Cache
   - 内部最大过时期限

#### <a name="sample-scenarios"></a>示例方案
以下示例演示此选项在特定场合下的工作方式：

 名称                 | 说明
 ---------------------|------------
user=joe              | 当所请求 URL 的查询字符串为“?user=joe”时匹配此模式。
\*user=\* \*optout=\* | 当 CDN URL 查询包含 user 或 optout 参数时匹配此模式。

[返回页首](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

## <a name="next-steps"></a>后续步骤
* [Azure 内容交付网络概述](cdn-overview.md)
* [规则引擎参考](cdn-rules-engine-reference.md)
* [规则引擎条件表达式](cdn-rules-engine-reference-conditional-expressions.md)
* [规则引擎功能](cdn-rules-engine-reference-features.md)
* [使用规则引擎重写默认 HTTP 行为](cdn-rules-engine.md)

