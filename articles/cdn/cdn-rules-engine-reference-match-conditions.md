---
title: "Azure CDN 规则引擎匹配条件 | Microsoft Docs"
description: "Azure CDN 规则引擎匹配条件和功能的参考文档。"
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
ms.date: 01/23/2017
ms.author: rli
translationtype: Human Translation
ms.sourcegitcommit: dccb945e170bd3e3f23283359db25e574a2d4296
ms.openlocfilehash: f4886b1d78dfa87cf25737fb46c12b5963034f27


---

# <a name="azure-cdn-rules-engine-match-conditions"></a>Azure CDN 规则引擎匹配条件
本主题列出了 Azure 内容交付网络 (CDN) [规则引擎](cdn-rules-engine.md)的可用匹配条件的详细说明。

规则的第二部分是匹配条件。 可以使用匹配条件来确定特定类型的请求，以便执行一组相应的功能。

例如，可以使用匹配条件按特定位置、特定 IP 地址或国家/地区、标头信息来筛选请求。

## <a name="always"></a>始终

“始终”匹配条件旨在向所有请求应用默认的一组功能。

## <a name="device"></a>设备

“设备”匹配条件用于根据属性标识来自移动设备的请求。  可通过 [WURFL](http://wurfl.sourceforge.net/) 进行移动设备检测。  WURFL 功能及其 CDN 规则引擎变量列出如下。

> [!NOTE] 
> “修改客户端请求标头”和“修改客户端响应标头”功能支持以下变量。

功能 | 变量 | 说明 | 示例值
-----------|----------|-------------|----------------
品牌名称 | %{wurfl_cap_brand_name} | 一个字符串，表示设备的品牌名称。 | Samsung
设备 OS | %{wurfl_cap_device_os} | 一个字符串，表示设备上安装的操作系统。 | IOS
设备 OS 版本 | %{wurfl_cap_device_os_version} | 一个字符串，表示设备上安装的 OS 的版本号。 | 1.0.1
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
渐进式下载 | %{wurfl_cap_progressive_download} | 一个布尔值，表示设备是否允许音频/视频边下载边播放。 | 是
发布日期 | %{wurfl_cap_release_date} | 一个字符串，表示设备在何年何月添加到 WURFL 数据库。<br/><br/>格式：`yyyy_mm` | 2013_december
垂直分辨率 | %{wurfl_cap_resolution_height} | 一个整数，表示设备以像素为单位的高度。 | 768
水平分辨率 | %{wurfl_cap_resolution_width} | 一个整数，表示设备以像素为单位的宽度。 | 1024


## <a name="location"></a>位置

以下匹配条件旨在根据请求者的位置标识请求。

Name | 目的
-----|--------
AS 编号 | 标识源自特定网络的请求。
国家/地区 | 标识源自指定国家/地区的请求。


## <a name="origin"></a>源

以下匹配条件旨在标识指向 CDN 存储或客户源服务器的请求。

Name | 目的
-----|--------
CDN 源 | 标识存储在 CDN 存储上的内容的请求。
客户源 | 标识存储在特定客户源服务器上的内容的请求。


## <a name="request"></a>请求

以下匹配条件旨在根据属性标识请求。

Name | 目的
-----|--------
客户端 IP 地址 | 标识源自特定 IP 地址的请求。
Cookie 参数 | 查看与每个针对指定值的请求关联的 Cookie。
Cookie 参数正则表达式 | 查看与每个针对指定正则表达式的请求关联的 Cookie。
边缘 Cname | 标识指向特定的边缘 CNAME 的请求。
引用域 | 标识从指定主机名引用的请求。
请求标头文本 | 标识所含的指定标头已设置为指定值的请求。
请求标头正则表达式 | 标识所含的指定标头已设置为特定值的请求，该特定值与指定的正则表达式相符。
请求标头通配符 | 标识所含的指定标头已设置为特定值的请求，该特定值与指定的模式相符。
请求方法 | 按 HTTP 方法标识请求。
请求方案 | 按 HTTP 协议标识请求。

## <a name="url"></a>代码

以下匹配条件旨在根据 URL 标识请求。

Name | 目的
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


## <a name="next-steps"></a>后续步骤
* [Azure CDN 概述](cdn-overview.md)
* [规则引擎参考](cdn-rules-engine-reference.md)
* [规则引擎条件表达式](cdn-rules-engine-reference-conditional-expressions.md)
* [规则引擎功能](cdn-rules-engine-reference-features.md)
* [使用规则引擎重写默认 HTTP 行为](cdn-rules-engine.md)




<!--HONumber=Jan17_HO4-->


