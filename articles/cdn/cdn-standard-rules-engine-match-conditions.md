---
title: 匹配 Azure CDN 的标准规则引擎中的条件 |Microsoft Docs
description: 适用于 Azure 内容传送网络 (Azure CDN) 的标准规则引擎中的匹配条件的参考文档。
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: allensu
ms.openlocfilehash: b8050b973027ac91ede0ba98f4d1c76831da9828
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "81259914"
---
# <a name="match-conditions-in-the-standard-rules-engine-for-azure-cdn"></a>Azure CDN 的标准规则引擎中的匹配条件

在 Azure 内容分发网络 (Azure CDN) 的 [标准规则引擎](cdn-standard-rules-engine.md) 中，规则由一个或多个匹配条件和操作组成。 本文详细说明了可以在 Azure CDN 的标准规则引擎中使用的匹配条件。

规则的第一部分是一个匹配条件或一组匹配条件。 在 Azure CDN 的标准规则引擎中，每个规则最多可以有四个匹配条件。 匹配条件指定要对其执行所定义操作的特定请求类型。 如果使用多个匹配条件，则匹配条件使用 AND 逻辑组合在一起。

例如，可以使用匹配条件：

- 基于特定的 IP 地址、国家或地区筛选请求。
- 按标头信息筛选请求。
- 筛选来自移动设备或桌面设备的请求。

## <a name="match-conditions"></a>匹配条件

以下匹配条件可在 Azure CDN 的标准规则引擎中使用。 

### <a name="device-type"></a>设备类型 

识别请求是从移动设备发出的还是从桌面设备发出的。  

#### <a name="required-fields"></a>Required fields

操作员 | 支持的值
---------|----------------
等于、不等于 | 移动、桌面

### <a name="http-version"></a>HTTP 版本

根据请求的 HTTP 版本识别请求。

#### <a name="required-fields"></a>Required fields

操作员 | 支持的值
---------|----------------
等于、不等于 | 2.0、1.1、1.0、0.9、全部

### <a name="request-cookies"></a>请求 Cookie

标识基于传入请求中的 cookie 信息的请求。

#### <a name="required-fields"></a>Required fields

Cookie 名称 | 操作员 | Cookie 值 | 大小写转换
------------|----------|--------------|---------------
字符串 | [标准运算符列表](#standard-operator-list) | 字符串、整数 | 无转换，转换为大写，到小写

#### <a name="key-information"></a>重要信息

- 指定 cookie 名称时，不能使用通配符值 (包括星号 (\*) # A3; 必须使用精确的 cookie 名称。
- 对于此匹配条件的每个实例，只能指定一个 cookie 名称。
- Cookie 名称比较不区分大小写。
- 若要指定多个 cookie 值，请在每个 cookie 值之间使用一个空格。 
- Cookie 值可以利用通配符值。
- 如果未指定通配符值，则只有完全匹配项才满足此匹配条件。 例如，"Value" 将匹配 "Value"，而不是 "Value1"。 

### <a name="post-argument"></a>Post 参数

根据为请求中使用的 POST 请求方法定义的参数识别请求。 

#### <a name="required-fields"></a>Required fields

参数名称 | 操作员 | 参数值 | 大小写转换
--------------|----------|----------------|---------------
字符串 | [标准运算符列表](#standard-operator-list) | 字符串、整数 | 无转换，转换为大写，到小写

### <a name="query-string"></a>查询字符串

标识那些包含特定查询字符串参数的请求。 此参数设置为一个与特定模式匹配的值。 请求 URL 中的查询字符串参数（例如 parameter=value）确定是否满足此条件。 此匹配条件按名称标识查询字符串参数，并接受一个或多个值作为参数值。

#### <a name="required-fields"></a>Required fields

操作员 | 查询字符串 | 大小写转换
---------|--------------|---------------
[标准运算符列表](#standard-operator-list) | 字符串、整数 | 无转换，转换为大写，到小写

### <a name="remote-address"></a>远程地址

根据请求者的位置或 IP 地址识别请求。

#### <a name="required-fields"></a>Required fields

操作员 | 支持的值
---------|-----------------
Any | 不可用
地理位置匹配 | 国家/地区代码
IP 匹配 | IP 地址（以空格分隔）
不是任何 | 不可用
非地理位置匹配 | 国家/地区代码
非 IP 匹配 | IP 地址（以空格分隔）

#### <a name="key-information"></a>重要信息

- 使用 CIDR 表示法。
- 若要指定多个 IP 地址和 IP 地址块，请在各个值之间使用单个空格：
  - **IPv4 示例**：1.2.3.4 10.20.30.40 匹配来自地址 1.2.3.4 或 10.20.30.40 的所有请求。
  - **IPv6 示例**：1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 匹配来自地址 1:2:3:4:5:6:7:8 或 10:20:30:40:50:60:70:80 的所有请求。
- IP 地址块的语法为 IP 基址后跟正斜杠和前缀大小。 例如：
  - **IPv4 示例**：5.5.5.64/26 匹配来自地址 5.5.5.64 到 5.5.5.127 的所有请求。
  - **IPv6 示例**：1:2:3:/48 匹配来自地址 1:2:3:0:0:0:0:0 到 1:2:3:ffff:ffff:ffff:ffff:ffff 的所有请求。

### <a name="request-body"></a>请求正文

根据请求正文中出现的特定文本识别请求。

#### <a name="required-fields"></a>Required fields

操作员 | 请求正文 | 大小写转换
---------|--------------|---------------
[标准运算符列表](#standard-operator-list) | 字符串、整数 | 无转换，转换为大写，到小写

### <a name="request-header"></a>请求标头

识别那些使用了特定标头的请求。

#### <a name="required-fields"></a>Required fields

标头名称 | 操作员 | 标头值 | 大小写转换
------------|----------|--------------|---------------
字符串 | [标准运算符列表](#standard-operator-list) | 字符串、整数 | 无转换，转换为大写，到小写

### <a name="request-method"></a>请求方法

识别那些使用了指定请求方法的请求。

#### <a name="required-fields"></a>Required fields

操作员 | 支持的值
---------|----------------
等于、不等于 | GET、POST、PUT、DELETE、HEAD、OPTIONS、TRACE

#### <a name="key-information"></a>重要信息

- 只有 GET request 方法才能在 Azure CDN 中生成缓存内容。 其他所有请求方法均通过网络代理。 

### <a name="request-protocol"></a>请求协议

识别那些使用了指定协议的请求。

#### <a name="required-fields"></a>Required fields

操作员 | 支持的值
---------|----------------
等于、不等于 | HTTP、HTTPS

### <a name="request-url"></a>请求 URL

识别那些与指定 URL 匹配的请求。

#### <a name="required-fields"></a>Required fields

操作员 | 请求 URL | 大小写转换
---------|-------------|---------------
[标准运算符列表](#standard-operator-list) | 字符串、整数 | 无转换，转换为大写，到小写

#### <a name="key-information"></a>重要信息

- 使用此规则条件时，请确保包含协议信息。 例如： https://www.\<yourdomain\>.com。

### <a name="url-file-extension"></a>URL 文件扩展名

识别那些在请求 URL 的文件名中包含了指定文件扩展名的请求。

#### <a name="required-fields"></a>Required fields

操作员 | 分机 | 大小写转换
---------|-----------|---------------
[标准运算符列表](#standard-operator-list) | 字符串、整数 | 无转换，转换为大写，到小写

#### <a name="key-information"></a>重要信息

- 对于扩展名，请不要包含前导句点，例如，使用 html 而非 .html。

### <a name="url-file-name"></a>URL 文件名

识别那些在请求 URL 中包含了指定文件名的请求。

#### <a name="required-fields"></a>Required fields

操作员 | 文件名 | 大小写转换
---------|-----------|---------------
[标准运算符列表](#standard-operator-list) | 字符串、整数 | 无转换，转换为大写，到小写

#### <a name="key-information"></a>重要信息

- 若要指定多个文件名，请使用单个空格分隔每个文件名。 

### <a name="url-path"></a>URL 路径

识别那些在请求 URL 中包含了指定路径的请求。

#### <a name="required-fields"></a>Required fields

操作员 | 值 | 大小写转换
---------|-------|---------------
[标准运算符列表](#standard-operator-list) | 字符串、整数 | 无转换，转换为大写，到小写

#### <a name="key-information"></a>重要信息

- 文件名值可以利用通配符值。 例如，每个文件名模式可以包含一个或多个星号 (*)，其中每个星号与包含一个或多个字符的序列匹配。

## <a name="reference-for-rules-engine-match-conditions"></a>规则引擎匹配条件参考

### <a name="standard-operator-list"></a>标准运算符列表

对于那些接受标准运算符列表中的值的规则，以下运算符有效：

- Any
- 等于 
- 包含 
- 开头为 
- 结尾为 
- 小于
- 小于或等于
- 大于
- 大于或等于
- 非任何项
- 不包含
- 开头不是 
- 结尾不是 
- 不小于
- 不小于或等于
- 不大于
- 不大于或等于

对于数字运算符（例如“小于”和“大于或等于”），使用的比较基于长度。 在这种情况下，匹配条件中的值应该是一个等于要比较的长度的整数。 

## <a name="next-steps"></a>后续步骤

- [Azure CDN 概述](cdn-overview.md)
- [标准规则引擎参考](cdn-standard-rules-engine-reference.md)
- [标准规则引擎中的操作](cdn-standard-rules-engine-actions.md)
- [使用标准规则引擎强制执行 HTTPS](cdn-standard-rules-engine.md)
