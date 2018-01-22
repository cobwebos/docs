---
title: "Azure CDN 规则引擎匹配条件 | Microsoft Docs"
description: "有关 Azure 内容交付网络规则引擎匹配条件的参考文档。"
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
ms.date: 12/21/2017
ms.author: rli
ms.openlocfilehash: 9986e654b076df099e3912f9da628728723b5c3d
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2018
---
# <a name="match-conditions-for-the-azure-cdn-rules-engine"></a>Azure CDN 规则引擎匹配条件
本文列出了 Azure 内容交付网络 (CDN) [规则引擎](cdn-rules-engine.md)的可用匹配条件的详细说明。

规则的第二部分是匹配条件。 可以使用匹配条件来确定特定类型的请求，以便执行一组相应的功能。

例如，可以使用匹配条件：
- 筛选对特定位置处的内容的请求。
- 筛选从特定 IP 地址或国家/地区生成的请求。
- 按标头信息筛选请求。

## <a name="always-match-condition"></a>“始终”匹配条件

“始终”匹配条件向所有请求应用默认的一组功能。

名称​​ | 目的
-----|--------
[始终](#always) | 向所有请求应用默认的一组功能。

## <a name="device-match-condition"></a>“设备”匹配条件

“设备”匹配条件用于根据属性标识来自移动设备的请求。  

名称​​ | 目的
-----|--------
[设备](#device) | 根据设备属性标识来自移动设备的请求。

## <a name="location-match-conditions"></a>“位置”匹配条件

“位置”匹配条件根据请求者的位置标识请求。

名称​​ | 目的
-----|--------
[AS 编号](#as-number) | 标识源自特定网络的请求。
[国家/地区](#country) | 标识源自指定国家/地区的请求。

## <a name="origin-match-conditions"></a>“源”匹配条件

“源”匹配条件标识指向内容交付网络存储或客户源服务器的请求。

名称​​ | 目的
-----|--------
[CDN 源](#cdn-origin) | 标识对存储在内容交付网络存储中的内容的请求。
[客户源](#customer-origin) | 标识存储在特定客户源服务器上的内容的请求。

## <a name="request-match-conditions"></a>“请求”匹配条件

“请求”匹配条件根据属性标识请求。

名称​​ | 目的
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

名称​​ | 目的
-----|--------
URL 路径目录 | 按相对路径标识请求。
URL 路径扩展名 | 按文件扩展名标识请求。
URL 路径文件名 | 按文件名标识请求。
URL 路径文本 | 将请求的相对路径与指定值比较。
URL 路径正则表达式 | 将请求的相对路径与指定正则表达式比较。
URL 路径通配符 | 将请求的相对路径与指定模式比较。
URL 查询文本 | 将请求的查询字符串与指定值比较。
URL 查询参数 | 标识所含的指定查询字符串参数已设置为特定值的请求，该特定值与指定的模式相符。
URL 查询正则表达式 | 标识所含的指定查询字符串参数已设置为特定值的请求，该特定值与指定的正则表达式相符。
URL 查询通配符 | 将指定值与请求的查询字符串比较。


## <a name="reference-for-rules-engine-match-conditions"></a>规则引擎匹配条件参考

---
### <a name="always"></a>始终

“始终”匹配条件向所有请求应用默认的一组功能。

[返回页首](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="as-number"></a>AS 编号 
AS 编号网络由其自治系统编号 (ASN) 定义。 提供了一个选项来指示当客户端网络“匹配”或“不匹配”指定的 ASN 时，是否满足此条件。

重要信息：
- 指定多个 ASN 时，可使用单个空格分隔每个 ASN。 例如，64514 64515 匹配来自 64514 或 64515 的请求。
- 某些请求可能不返回有效的 ASN。 问号 (?) 将匹配无法确定有效 ASN 的请求。
- 必须为所需网络指定整个 ASN。 将不匹配部分值。
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
- 已从内容交付网络存储请求内容。
- 请求 URI 使用此匹配条件中定义的内容访问点（例如，/000001）。
  - 内容交付网络 URL：请求 URI 必须包含所选内容访问点。
  - 边缘 CNAME URL：相应的边缘 CNAME 配置必须指向所选内容访问点。
  
重要信息：
 - 内容访问点标识应处理请求内容的服务。
 - 请勿使用 AND IF 语句组合某些匹配条件。 例如，将“CDN 源”匹配条件与“客户源”匹配条件组合会创建一个永远无法匹配的匹配模式。 为此，不能通过 AND IF 语句组合两个“CDN 源”匹配条件。

[返回页首](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="client-ip-address"></a>客户端 IP 地址
提供了一个选项来指示当客户端 IP 地址“匹配”或“不匹配”指定的 IP 地址时，是否满足客户端 IP 地址条件。

重要信息：
- 确保使用 CIDR 表示法。
- 指定多个 IP 地址和/或 IP 地址块时，可使用单个空格分隔每个地址和/或地址块。
  - **IPv4 示例**：1.2.3.4 10.20.30.40 匹配来自 1.2.3.4 或 10.20.30.40 的所有请求。
  - **IPv6 示例**：1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 匹配来自 1:2:3:4:5:6:7:8 或 10:20:30:40:50:60:70:80 的所有请求。
- IP 地址块的语法为 IP 基址后跟正斜杠和前缀大小。
  - **IPv4 示例**：5.5.5.64/26 匹配来自 5.5.5.64 到 5.5.5.127 的所有请求。
  - **IPv6 示例**：1:2:3:/48 匹配来自 1:2:3:0:0:0:0:0 到 1:2:3:ffff:ffff:ffff:ffff:ffff 的所有请求。
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
“匹配”/“不匹配”选项用于确定满足“Cookie 参数”匹配条件所需具备的条件。
- **匹配**：需要请求包含指定的 Cookie，其值与此匹配条件中定义的至少一个值匹配。
- **不匹配**：需要请求满足以下条件之一：
  - 它不包含指定的 Cookie。
  - 它包含指定的 Cookie，但其值与此匹配条件中定义的任何值都不匹配。
  
重要信息：
- Cookie 名称： 
  - 指定 Cookie 名称时不支持特殊字符，包括星号。 这意味着只有完全匹配的 Cookie 名称才能进行比较。
  - 此匹配条件的每个实例只能指定一个 Cookie 名称。
  - Cookie 名称比较不区分大小写。
- Cookie 值： 
  - 指定多个 Cookie 值时，可使用单个空格分隔每个值。
  - Cookie 值可以利用特殊字符。 
  - 如果未指定通配符，则只有完全匹配项才满足此匹配条件。 例如，指定“Value”将匹配“Value”，而不是“Value1”或“Value2”。
  - “忽略大小写”选项用于确定是否对请求的 Cookie 值进行区分大小写的比较。
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
“Cookie 参数正则表达式”匹配条件定义 Cookie 名称和值。 可使用正则表达式定义所需的 Cookie 值。 

“匹配”/“不匹配”选项用于确定满足此匹配条件所需具备的条件。
- **匹配**：需要请求包含指定的 Cookie，其值与指定的正则表达式匹配。
- **不匹配**：需要请求满足以下条件之一：
  - 它不包含指定的 Cookie。
  - 它包含指定的 Cookie，但其值与指定的正则表达式不匹配。
  
重要信息：
- Cookie 名称： 
  - 指定 Cookie 名称时不支持正则表达式和特殊字符，包括星号。 这意味着只有完全匹配的 Cookie 名称才能进行比较。
  - 此匹配条件的每个实例只能指定一个 Cookie 名称。
  - Cookie 名称比较不区分大小写。
- Cookie 值： 
  - Cookie 值可以利用正则表达式。
  - “忽略大小写”选项用于确定是否对请求的 Cookie 值进行区分大小写的比较。
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
可以通过国家/地区代码指定国家/地区。 提供了一个选项来指示当请求源自的国家/地区“匹配”或“不匹配”指定值时，是否满足此条件。

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

[返回页首](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="customer-origin"></a>客户源

重要信息： 
- 无论通过内容交付网络 URL 还是通过指向所选客户源的边缘 CNAME URL 请求内容，都满足“客户源”匹配条件。
- 无法从“客户源”页删除被某个规则引用的客户源配置。 尝试删除客户源配置之前，请确保以下配置未引用它：
  - “客户源”匹配条件
  - 边缘 CNAME 配置
- 请勿使用 AND IF 语句组合某些匹配条件。 例如，将“客户源”匹配条件与“CDN 源”匹配条件组合会创建一个永远无法匹配的匹配模式。 为此，不能通过 AND IF 语句组合两个“客户源”匹配条件。

[返回页首](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="device"></a>设备

“设备”匹配条件用于根据属性标识来自移动设备的请求。 可通过 [WURFL](http://wurfl.sourceforge.net/) 进行移动设备检测。 下表列出了内容交付网络规则引擎的 WURFL 功能及其变量。
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
是平板电脑 | %{wurfl_cap_is_tablet} | 一个布尔值，表示设备是否为平板电脑。 这是独立于 OS 的说明。 | 是
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
- 可用边缘 CNAME 列表仅包含与正在配置 HTTP 规则引擎的平台相对应的“边缘 CNAME”页上配置的边缘 CNAME。
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
与用于请求内容的引用域关联的主机名可确定是否满足“引用域”条件。 提供了一个选项来指示当引用域主机名“匹配”或“不匹配”指定值时，是否满足此条件。

重要信息：
- 指定多个主机名时，可使用单个空格分隔每个主机名。
- 此匹配条件支持特殊字符。
- 如果指定值不包含星号，则它必须与引用域的主机名完全匹配。 例如，指定“mydomain.com”不会匹配“www.mydomain.com”。
- “忽略大小写”选项用于确定是否执行区分大小写的比较。
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
“匹配”/“不匹配”选项用于确定满足此匹配条件所需具备的条件。
- **匹配**：需要请求包含指定的标头。 其值必须与此匹配条件中定义的值匹配。
- **不匹配**：需要请求满足以下条件之一：
  - 它不包含指定的标头。
  - 它包含指定的标头，但其值与此匹配条件中定义的值不匹配。
  
重要信息：
- 标头名称比较始终不区分大小写。 “忽略大小写”选项用于确定标头值的比较是否区分大小写。
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
“匹配”/“不匹配”选项用于确定满足“请求标头正则表达式”匹配条件所需具备的条件。
- **匹配**：需要请求包含指定的标头。 其值必须与指定正则表达式中定义的模式匹配。
- **不匹配**：需要请求满足以下条件之一：
  - 它不包含指定的标头。
  - 它包含指定的标头，但其值与指定的正则表达式不匹配。

重要信息：
- 标头名称： 
  - 标头名称比较不区分大小写。
  - 标头名称中的空格应替换为“%20”。 
- 标头值： 
  - 标头值可以利用正则表达式。
  - “忽略大小写”选项用于确定标头值的比较是否区分大小写。
  - 只有与至少一个指定模式完全匹配的标头值才满足此条件。
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
“匹配”/“不匹配”选项用于确定满足“请求标头通配符”匹配条件所需具备的条件。
- **匹配**：需要请求包含指定的标头。 其值必须与此匹配条件中定义的至少一个值匹配。
- **不匹配**：需要请求满足以下条件之一：
  - 它不包含指定的标头。
  - 它包含指定的标头，但其值与任何指定值都不匹配。
  
重要信息：
- 标头名称： 
  - 标头名称比较不区分大小写。
  - 标头名称中的空格应替换为“%20”。 也可以使用此值指定标头值中的空格。
- 标头值： 
  - 标头值可以利用特殊字符。
  - “忽略大小写”选项用于确定标头值的比较是否区分大小写。
  - 只有与至少一个指定模式完全匹配的标头值才满足此条件。
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
只有通过所选请求方法请求的资产才满足“请求方法”条件。 可用请求方法包括：
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
只有通过所选协议请求的资产才满足“请求方案”条件。 可用协议为 HTTP 和 HTTPS。

重要信息：
- 考虑到缓存设置的跟踪方式，此匹配条件与以下功能不兼容：
  - 完成缓存填充
  - 默认的内部最大有效期
  - 强制内部最大有效期
  - 忽略源服务器 No-Cache
  - 内部最大过时期限

[返回页首](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

## <a name="next-steps"></a>后续步骤
* [Azure 内容交付网络概述](cdn-overview.md)
* [规则引擎参考](cdn-rules-engine-reference.md)
* [规则引擎条件表达式](cdn-rules-engine-reference-conditional-expressions.md)
* [规则引擎功能](cdn-rules-engine-reference-features.md)
* [使用规则引擎重写默认 HTTP 行为](cdn-rules-engine.md)

