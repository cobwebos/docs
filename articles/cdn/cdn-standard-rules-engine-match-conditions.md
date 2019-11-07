---
title: 来自 Microsoft 标准规则引擎匹配条件的 Azure CDN |Microsoft Docs
description: Microsoft 标准规则引擎匹配条件中的 Azure 内容分发网络参考文档。
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: a72452d37b152a9463a5aee0e199fd42ea852236
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615957"
---
# <a name="azure-cdn-from-microsoft-standard-rules-engine-match-conditions"></a>Microsoft 标准规则引擎匹配条件中的 Azure CDN

本文列出了 Microsoft[标准规则引擎](cdn-standard-rules-engine.md)提供的 Azure 内容分发网络（CDN）的可用匹配条件的详细说明。

规则的第一部分是一组匹配条件。 每个规则最多可以有4个匹配条件。 匹配条件标识将对规则中定义的操作执行的特定类型的请求。 如果使用多个匹配条件，它们将使用和逻辑分组在一起。

例如，可以使用匹配条件：

- 筛选从特定 IP 地址或国家/地区生成的请求。
- 按标头信息筛选请求。
- 筛选移动设备或桌面设备发出的请求。

## <a name="match-conditions"></a>匹配条件

以下匹配条件可供使用。 

### <a name="device-type"></a>设备类型 

设备类型匹配条件标识从移动设备或桌面设备基于其属性发出的请求。  

**必填字段**

运算符 | 支持的值
---------|----------------
等于，不等于 | 移动、桌面


### <a name="http-version"></a>HTTP 版本

HTTP 版本匹配条件根据请求所请求的 HTTP 版本来识别请求。

**必填字段**

运算符 | 支持的值
---------|----------------
等于，不等于 | 2.0、1.1、1.0、0.9、全部


### <a name="request-cookies"></a>请求 cookie

请求 cookie 匹配条件根据传入请求中的 cookie 信息识别请求。

**必填字段**

Cookie 名称 | 运算符 | Cookie 值 | 大小写转换
------------|----------|--------------|---------------
String | [标准操作员列表](#standard-operator-list) | String、Int | 无转换，转换为大写，到小写

重要信息
- 指定 cookie 名称时不支持通配符值，包括星号（*），仅精确的 cookie 名称匹配项符合比较条件。
- 此匹配条件的每个实例只能指定一个 Cookie 名称。
- Cookie 名称比较不区分大小写。
- 指定多个 Cookie 值时，可使用单个空格分隔每个值。 
- Cookie 值可以利用通配符值。
- 如果未指定通配符值，则只有完全匹配项才满足此匹配条件。 例如，指定 "Value" 将匹配 "Value"，但不匹配 "Value1"。 

### <a name="post-argument"></a>Post 参数

**必填字段**

参数名称 | 运算符 | 参数值 | 大小写转换
--------------|----------|----------------|---------------
String | [标准操作员列表](#standard-operator-list) | String、Int | 无转换，转换为大写，到小写

### <a name="query-string"></a>查询字符串

查询字符串匹配条件标识包含指定查询字符串参数的请求。 此参数设置为与指定的模式匹配的值。 请求 URL 中的查询字符串参数（例如 parameter=value）确定是否满足此条件。 此匹配条件按名称标识查询字符串参数，并接受一个或多个值作为参数值。

**必填字段**

运算符 | 查询字符串 | 大小写转换
---------|--------------|---------------
[标准操作员列表](#standard-operator-list) | String、Int | 无转换，转换为大写，到小写

### <a name="remote-address"></a>远程地址

远程地址匹配条件根据请求者的位置或 IP 地址来识别请求。

**必填字段**

运算符 | 支持的值
---------|-----------------
任意 | 不适用
地域匹配 | 国家/地区代码
IP 匹配 | IP 地址（空格分隔）
不是任何 | 不适用
非地理匹配 | 国家/地区代码
不匹配 IP | IP 地址（空格分隔）

重要信息：

- 使用 CIDR 表示法。
- 指定多个 IP 地址和/或 IP 地址块时，可使用单个空格分隔每个地址和/或地址块。 例如：
  - **IPv4 示例**：1.2.3.4 10.20.30.40 匹配来自地址 1.2.3.4 或 10.20.30.40 的所有请求。
  - **IPv6 示例**：1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 匹配来自地址 1:2:3:4:5:6:7:8 或 10:20:30:40:50:60:70:80 的所有请求。
- IP 地址块的语法为 IP 基址后跟正斜杠和前缀大小。 例如：
  - **IPv4 示例**：5.5.5.64/26 匹配来自地址 5.5.5.64 到 5.5.5.127 的所有请求。
  - **IPv6 示例**：1:2:3:/48 匹配来自地址 1:2:3:0:0:0:0:0 到 1:2:3:ffff:ffff:ffff:ffff:ffff 的所有请求。

### <a name="request-body"></a>请求正文

**必填字段**

运算符 | 请求正文 | 大小写转换
---------|--------------|---------------
[标准操作员列表](#standard-operator-list) | String、Int | 无转换，转换为大写，到小写

### <a name="request-header"></a>请求标头

**必填字段**
标头名称 | 运算符 | 标头值 | 大小写转换
------------|----------|--------------|---------------
String | [标准操作员列表](#standard-operator-list) | String、Int | 无转换，转换为大写，到小写

### <a name="request-method"></a>请求方法

**必填字段**

运算符 | 支持的值
---------|----------------
等于，不等于 | GET、POST、PUT、DELETE、HEAD、OPTIONS、TRACE

重要信息：

- 只有 GET 请求方法可以生成 CDN 上的缓存内容。 其他所有请求方法均通过网络代理。 

### <a name="request-protocol"></a>请求协议

**必填字段**

运算符 | 支持的值
---------|----------------
等于，不等于 | HTTP、HTTPS

### <a name="request-url"></a>请求 URL

**必填字段**

运算符 | 请求 URL | 大小写转换
---------|-------------|---------------
[标准操作员列表](#standard-operator-list) | String、Int | 无转换，转换为大写，到小写

重要信息：

- 输入请求 URL 时，请确保包含协议信息。 例如 "https://www。[yourdomain] .com "

### <a name="url-file-extension"></a>URL 文件扩展名

**必填字段**

运算符 | 分机 | 大小写转换
---------|-----------|---------------
[标准操作员列表](#standard-operator-list) | String、Int | 无转换，转换为大写，到小写

重要信息：

- 对于扩展，不包括前导句点;例如，使用 html 而不是 .html。

### <a name="url-file-name"></a>URL 文件名

**必填字段**

运算符 | 文件名 | 大小写转换
---------|-----------|---------------
[标准操作员列表](#standard-operator-list) | String、Int | 无转换，转换为大写，到小写

重要信息：

- 若要指定多个文件名，请使用单个空格分隔每个文件名。 

### <a name="url-path"></a>URL 路径

**必填字段**

运算符 | 值 | 大小写转换
---------|-------|---------------
[标准操作员列表](#standard-operator-list) | String、Int | 无转换，转换为大写，到小写

重要信息：

- 文件名值可以利用通配符值。 例如，每个文件名模式可以包含一个或多个星号 (*)，其中每个星号与包含一个或多个字符的序列匹配。

## <a name="reference-for-rules-engine-match-conditions"></a>规则引擎匹配条件参考

### <a name="standard-operator-list"></a>标准操作员列表

对于包含标准运算符列表的规则，以下运算符是有效的：

- 任意
- 等于 
- Contains 
- 开头为 
- 结尾为 
- 小于
- 小于或等于
- 大于
- 大于或等于
- 不是任何
- 不包含
- 开头不为 
- 结尾不为 
- 不小于
- 不小于或等于
- 不大于
- 不大于或等于

对于数字运算符，如 "小于" 或 "大于或等于"，所使用的比较将基于长度。 在这种情况下，匹配条件中的值应为一个整数，该整数等于要比较的长度。 

---

[返回页首](#match-conditions)

</br>

## <a name="next-steps"></a>后续步骤

- [Azure 内容分发网络概述](cdn-overview.md)
- [规则引擎参考](cdn-standard-rules-engine-reference.md)
- [规则引擎操作](cdn-standard-rules-engine-actions.md)
- [使用标准规则引擎强制执行 HTTPS](cdn-standard-rules-engine.md)
