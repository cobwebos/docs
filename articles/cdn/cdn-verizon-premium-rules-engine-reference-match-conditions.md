---
title: Verizon Premium Azure CDN 规则引擎匹配条件
description: 有关 Verizon Premium Azure 内容分发网络规则引擎匹配条件的参考文档。
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: efd6e6a93cd4ca79e6c4b6de69f8514e2d71b252
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84323308"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-match-conditions"></a>Verizon Premium Azure CDN 规则引擎匹配条件

本文列出了 Verizon Premium Azure 内容分发网络 (CDN) [规则引擎](cdn-verizon-premium-rules-engine.md)的可用匹配条件的详细说明。

规则的第二部分是匹配条件。 可以使用匹配条件来确定特定类型的请求，以便执行一组相应的功能。

例如，可以使用匹配条件：

- 筛选对特定位置处的内容的请求。
- 筛选从特定 IP 地址或国家/地区生成的请求。
- 按标头信息筛选请求。

## <a name="match-conditions"></a><a name="top"></a>匹配条件

* [始终](#always)
* [设备](#device)
* [位置](#location)
* [源](#origin)
* [请求](#request)
* [URL](#url)

### <a name="always"></a><a name="always"></a>始终

[Always match 条件](https://docs.vdms.com/cdn/Content/HRE/M/Always.htm) 旨在向所有请求应用默认的一组功能。

### <a name="device"></a><a name="device"></a>装置

这些匹配条件旨在根据客户端的用户代理来识别请求。

| 名称       | 目的                                                           |
|------------|-------------------------------------------------------------------|
| 品牌名称 | 通过设备的品牌名称是否匹配来识别请求： <br> **-**[品牌名称文本](https://docs.vdms.com/cdn/Content/HRE/M/D-Brand-Name-Literal.htm) (特定值)  <br> **-** ([品牌名称 Regex](https://docs.vdms.com/cdn/Content/HRE/M/D-Brand-Name-Regex.htm)) 正则表达式 <br> **-** 特定模式 ([品牌名称通配符](https://docs.vdms.com/cdn/Content/HRE/M/D-Brand-Name-Wildcard.htm))  |
| 设备 OS | 根据设备的操作系统是否匹配，确定请求： <br> **-**[设备 OS 文本](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Literal.htm))  (特定值 <br> **-** ([设备 OS Regex](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Regex.htm)) 的正则表达式 <br> **-** ([设备操作系统通配符](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Wildcard.htm) 的特定模式)  |
| 设备 OS 版本 | 根据设备的操作系统版本是否匹配来识别请求： <br> **-** ([设备 OS 版本文字](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Version-Literal.htm)) 的特定值 <br> **-** ([设备 OS 版本 Regex](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Version-Regex.htm)) 的正则表达式 <br> **-** ([设备 OS 版本通配符](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Version-Wildcard.htm) 的特定模式)  |
| [双重方向？](https://docs.vdms.com/cdn/Content/HRE/M/D-Dual-Orientation.htm) | 通过设备是否支持双重方向来识别请求。 |
| HTML 首选 DTD | 通过设备的 HTML 首选 DTD 是否匹配来识别请求： <br> **-**[HTML 首选 DTD 文本](https://docs.vdms.com/cdn/Content/HRE/M/D-HTML-Preferred-DTD-Literal.htm))  (特定值 <br> **-** 正则表达式 ([HTML 首选 DTD Regex](https://docs.vdms.com/cdn/Content/HRE/M/D-HTML-Preferred-DTD-Regex.htm))  <br> **-** 特定模式 ([HTML 首选 DTD 通配符](https://docs.vdms.com/cdn/Content/HRE/M/D-HTML-Preferred-DTD-Wildcard.htm))  |
| [图像内联？](https://docs.vdms.com/cdn/Content/HRE/M/D-Image-Inlining.htm) | 通过设备是否支持 Base64 编码的图像来识别请求。 |
| [是否为 Android？](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Android.htm) | 通过设备是否使用 Android OS 来识别请求。 |
| [应用？](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-App.htm) | 确定本机应用程序是否请求内容时的请求。 |
| [是否为完整桌面？](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Full-Desktop.htm) | 通过设备是否提供完全桌面体验来识别请求。 |
| [IOS 是 Io？](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-iOS.htm) | 通过设备是否使用 iOS 来识别请求。 |
| [机器人？](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Robot.htm) | 确定是否将设备视为自动 HTTP 客户端 (例如，机器人爬网程序) 的请求。 |
| [智能电视？](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Smart-TV.htm) | 通过设备是否为智能电视来识别请求。 |
| [智能手机？](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Smartphone.htm) | 通过设备是否为智能手机来识别请求。
| [是否为平板电脑？](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Tablet.htm) | 通过设备是否为平板电脑来识别请求。 |
| [是否为触摸屏？](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Touchscreen.htm) | 通过设备的主指针设备是否为触摸屏来识别请求。 |
| [Windows Phone？](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Windows-Phone.htm) | 通过设备是否为 Windows Mobile 6.5/Windows Phone 7 或更高版本来识别请求。 |
| [无线设备？](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Wireless-Device.htm) | 通过设备是否为无线来识别请求。 
| 市场营销名称 | 根据设备的营销名称是否匹配来识别请求： <br> **-** ([行销名称文本](https://docs.vdms.com/cdn/Content/HRE/M/D-Marketing-Name-Literal.htm)) 的特定值 <br> **-** ([市场营销名称 Regex](https://docs.vdms.com/cdn/Content/HRE/M/D-Marketing-Name-Regex.htm)) 正则表达式 <br> **-** 特定模式 ([行销名称通配符](https://docs.vdms.com/cdn/Content/HRE/M/D-Marketing-Name-Wildcard.htm))  |
| 移动浏览器 | 通过设备浏览器是否匹配来识别请求： <br> **-**[移动浏览器文本](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Literal.htm) (的特定值)  <br> **-**[移动浏览器 Regex](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Regex.htm) (正则表达式)  <br> **-**[移动浏览器通配符](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Wildcard.htm) (特定模式)  |
| 移动浏览器版本 | 通过设备的浏览器版本是否匹配来识别请求： <br> **-**[移动浏览器版本文字](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Version-Literal.htm) (特定值)  <br> **-**[移动浏览器版本 Regex](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Version-Regex.htm) (正则表达式)  <br> **-**[移动浏览器版本通配符](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Version-Wildcard.htm) (特定模式)  |
| 模型名称 | 根据设备的模型名称是否匹配来识别请求： <br> **-** ([模型名称文本](https://docs.vdms.com/cdn/Content/HRE/M/D-Model-Name-Literal.htm)) 的特定值 <br> **-** 正则表达式 ([模型名称 Regex](https://docs.vdms.com/cdn/Content/HRE/M/D-Model-Name-Regex.htm))  <br> **-** 特定模式 ([型号名称通配符](https://docs.vdms.com/cdn/Content/HRE/M/D-Model-Name-Wildcard.htm))  |
| [渐进式下载？](https://docs.vdms.com/cdn/Content/HRE/M/D-Progressive-Download.htm) | 通过设备是否支持渐进式下载来确定请求。 |
| 发布日期 | 根据设备的发布日期是否匹配来识别请求： <br> **-** ([发布日期文本](https://docs.vdms.com/cdn/Content/HRE/M/D-Release-Date-Literal.htm)) 的特定值 <br> **-** 正则表达式 ([发布日期正则](https://docs.vdms.com/cdn/Content/HRE/M/D-Release-Date-Regex.htm) 表达式)  <br> **-** 特定模式 ([发布日期通配符](https://docs.vdms.com/cdn/Content/HRE/M/D-Release-Date-Wildcard.htm))  |
| [垂直分辨率](https://docs.vdms.com/cdn/Content/HRE/M/D-Resolution-Height.htm) | 标识按设备高度列出的请求。 |
| [水平分辨率](https://docs.vdms.com/cdn/Content/HRE/M/D-Resolution-Width.htm) | 标识按设备宽度列出的请求。 |

**[返回页首](#top)**

### <a name="location"></a><a name="location"></a>定位

以下匹配条件旨在根据请求者的位置标识请求。

| 名称       | 目的                                                           |
|------------|-------------------------------------------------------------------|
| [AS 编号](https://docs.vdms.com/cdn/Content/HRE/M/AS-Number.htm) | 标识源自特定网络的请求。 |
| 城市名称 | 确定请求是否来自其名称与匹配的城市： <br> **-**[城市名称文本](https://docs.vdms.com/cdn/Content/HRE/M/City-Name-Literal.htm))  (特定值 <br> **-** 正则表达式 ([城市名称 Regex](https://docs.vdms.com/cdn/Content/HRE/M/City-Name-Regex.htm))  |
| [洲](https://docs.vdms.com/cdn/Content/HRE/M/Continent.htm) | 标识源自指定洲的请求。 |
| [国家/地区](https://docs.vdms.com/cdn/Content/HRE/M/Country.htm) | 标识源自指定国家/地区的请求。 |
| [DMA 代码](https://docs.vdms.com/cdn/Content/HRE/M/DMA-Code.htm) | 标识源自指定 metros (指定市场领域的请求) 。 |
| [纬度](https://docs.vdms.com/cdn/Content/HRE/M/Latitude.htm) | 标识源自指定纬度的请求。 |
| [精度](https://docs.vdms.com/cdn/Content/HRE/M/Longitude.htm) | 标识源自指定经度的请求。 |
| [地铁代码](https://docs.vdms.com/cdn/Content/HRE/M/Metro-Code.htm) | 标识源自指定 metros (指定市场领域的请求) 。 |
| [邮政编码](https://docs.vdms.com/cdn/Content/HRE/M/Postal-Code.htm) | 标识源自指定邮政编码的请求。 |
| [区域代码](https://docs.vdms.com/cdn/Content/HRE/M/Region-Code.htm) | 标识源自指定区域的请求。 |

> [!NOTE]
> **我应该使用地铁代码还是 DMA 代码？** <br>
这两个匹配条件都提供相同的功能。 但是，我们建议使用地铁代码匹配条件来识别 DMA 发出的请求。

**[返回页首](#top)**

### <a name="origin"></a><a name="origin"></a>格式

以下匹配条件旨在标识指向 CDN 存储或客户源服务器的请求。

| 名称       | 目的                                                           |
|------------|-------------------------------------------------------------------|
| [CDN 源](https://docs.vdms.com/cdn/Content/HRE/M/CDN-Origin.htm) | 标识存储在 CDN 存储上的内容的请求。 |
| [客户源](https://docs.vdms.com/cdn/Content/HRE/M/Customer-Origin.htm) | 标识存储在特定客户源服务器上的内容的请求。 |

**[返回页首](#top)**

### <a name="request"></a><a name="request"></a>请求

以下匹配条件旨在根据属性标识请求。

| 名称              | 目的                                                                |
|-------------------|------------------------------------------------------------------------|
| [客户端 IP 地址](https://docs.vdms.com/cdn/Content/HRE/M/Client-IP-Address.htm) | 标识源自特定 IP 地址的请求。 |
| Cookie 参数  | 确定请求是否包含与匹配的 cookie： <br> **-**[Cookie 参数文本](https://docs.vdms.com/cdn/Content/HRE/M/Cookie-Parameter-Literal.htm) (的特定值)  <br> **-** 正则表达式 ([Cookie 参数正则](https://docs.vdms.com/cdn/Content/HRE/M/Cookie-Parameter-Regex.htm)表达式 <br> **-** 特定模式 ([Cookie 参数通配符](https://docs.vdms.com/cdn/Content/HRE/M/Cookie-Parameter-Wildcard.htm))  |
| [边缘 CNAME](https://docs.vdms.com/cdn/Content/HRE/M/Edge-CNAME.htm) | 标识指向特定的边缘 CNAME 的请求。 |
| 引用域 | 确定请求是否由与匹配的主机名引用： <br> **-**[引用域文本](https://docs.vdms.com/cdn/Content/HRE/M/Referring-Domain-Literal.htm) (特定值)  <br> **-**[引用域通配符](https://docs.vdms.com/cdn/Content/HRE/M/Referring-Domain-Wildcard.htm) (特定模式)  |
| 请求头 | 确定请求是否包含与匹配的标头： <br> **-** ([请求标头文本](https://docs.vdms.com/cdn/Content/HRE/M/Request-Header-Literal.htm)) 的特定值 <br> **-** 正则表达式 ([请求标头正则](https://docs.vdms.com/cdn/Content/HRE/M/Request-Header-Regex.htm) 表达式)  <br> **-** 特定模式 ([请求标头通配符](https://docs.vdms.com/cdn/Content/HRE/M/Request-Header-Wildcard.htm))  |
| [Request 方法](https://docs.vdms.com/cdn/Content/HRE/M/Request-Method.htm) | 按 HTTP 方法标识请求。 |
| [请求方案](https://docs.vdms.com/cdn/Content/HRE/M/Request-Scheme.htm) | 按 HTTP 协议标识请求。 |

**[返回页首](#top)**

### <a name="url"></a><a name="url"></a>URL

| 名称              | 目的                                                                |
|-------------------|------------------------------------------------------------------------|
| URL 路径 | 根据请求的相对路径（包括文件名）来确定请求是否与匹配： <br> **-**[URL 路径文本](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Literal.htm) (特定值)  <br> **-** 正则表达式 ([URL 路径 Regex](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Regex.htm))  <br> **-** 特定模式 ([URL 路径通配符](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Wildcard.htm))  |
| URL 路径目录 | 根据请求的相对路径是否匹配来识别请求： <br> **-**[URL 路径目录文本](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Directory-Literal.htm) (特定值)  <br> **-** 特定模式 ([URL 路径目录通配符](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Directory-Wildcard.htm))  |
| URL 路径扩展名 | 根据请求的文件扩展名是否匹配来识别请求： <br> **-**[URL 路径扩展文字](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Extension-Literal.htm) (特定值)  <br> **-** 特定模式 ([URL 路径扩展名通配符](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Extension-Wildcard.htm))  |
| URL 路径文件名 | 根据请求的文件名是否匹配来识别请求： <br> **-** ([URL 路径文件名文本](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Filename-Literal.htm)) 的特定值 <br> **-** 特定模式 ([URL 路径文件名通配符](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Filename-Wildcard.htm))  |
| URL 查询 | 根据请求的查询字符串是否匹配来识别请求： <br> **-** ([URL 查询文本](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Literal.htm)) 的特定值 <br> **-** 正则表达式 ([URL 查询正](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Regex.htm) 则表达式)  <br> **-** 特定模式 ([URL 查询通配符](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Wildcard.htm))  |
| URL 查询参数 | 确定请求是否包含一个查询字符串参数，该参数设置为与匹配的值： <br> **-** ([URL 查询参数文本](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Parameter-Literal.htm)) 的特定值 <br> **-** 特定模式 ([URL 查询参数通配符](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Parameter-Wildcard.htm))  |

**[返回页首](#top)**

有关最新匹配条件，请参阅 [Verizon 规则引擎文档](https://docs.vdms.com/cdn/index.html#Quick_References/HRE_QR.htm#Conditio)。

## <a name="next-steps"></a>后续步骤

- [Azure 内容分发网络概述](cdn-overview.md)
- [规则引擎参考](cdn-verizon-premium-rules-engine-reference.md)
- [规则引擎条件表达式](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [规则引擎功能](cdn-verizon-premium-rules-engine-reference-features.md)
- [使用规则引擎重写默认 HTTP 行为](cdn-verizon-premium-rules-engine.md)
