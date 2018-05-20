---
title: Azure CDN 规则引擎的 HTTP 变量 | Microsoft Docs
description: HTTP 变量可用于检索 HTTP 请求和响应元数据。
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: v-deasim
ms.openlocfilehash: 36c1b20219fabd1b7c02247d9a93bb7b7cfc898d
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
---
# <a name="http-variables-for-azure-cdn-rules-engine"></a>Azure CDN 规则引擎的 HTTP 变量
HTTP 变量提供各种检索 HTTP 请求和响应元数据的方法。 随后可使用此元数据动态更改请求或响应。 以下规则引擎功能对 HTTP 变量的使用具有一定的限制：

- [Cache-Key 重写](cdn-rules-engine-reference-features.md#cache-key-rewrite)
- [修改客户端请求标头](cdn-rules-engine-reference-features.md#modify-client-request-header)
- [修改客户端响应标头](cdn-rules-engine-reference-features.md#modify-client-response-header)
- [URL 重定向](cdn-rules-engine-reference-features.md#url-redirect)
- [URL 重写](cdn-rules-engine-reference-features.md#url-rewrite)

## <a name="definitions"></a>定义
下表介绍了受支持的 HTTP 变量。 如果某个特定请求无法使用 GEO 元数据（例如邮政编码），则会返回空值。


| 名称 | 变量 | 说明 | 示例值 |
| ---- | -------- | ----------- | ------------ |
| ASN（申请者） | %{geo_asnum} | 指示申请者的 AS 号码。 <br /><br />**已弃用：**%{virt_dst_asnum}。 <br />此变量已被弃用，以支持 %{geo_asnum}。 虽然规则使用此已被弃用的变量后仍然有效，但还是应更新规则，以使用新变量。 | AS15133 |
| 市/县（申请者） | %{geo_city} | 指示申请者所在的市/县。 | 洛杉矶 |
| 洲（申请者） | %{geo_continent} | 指示申请者所在的大洲（缩写形式） <br />有效值是： <br />AF：非洲<br />AS：亚洲<br />EU：欧洲<br />NA：北美洲<br />OC：大洋洲<br />SA：南美洲<br /><br />**已弃用：**%{virt_dst_continent}。 <ber />此变量已被弃用，以支持 %{geo_continent}。 <br />虽然规则使用此已被弃用的变量后仍然有效，但还是应更新规则，以使用新变量。| 不适用 |
| Cookie 值 | %{cookie_Cookie} | 返回的值对应于由 Cookie 术语标识的 cookie 项。 | 示例用法： <br />%{cookie__utma}<br /><br />示例值：<br />111662281.2.10.1222100123 |
| 国家/地区（申请者） | %{geo_country} | 指示申请者所在的国家/地区（用国家/地区代码表示） <br />**已弃用：**%{virt_dst_country}。 <br /><br />此变量已被弃用，以支持 %{geo_country}。 虽然规则使用此已被弃用的变量后仍然有效，但还是应更新规则，以使用新变量。 | 美国 |
| 指定的市场区域（申请者） | %{geo_dma_code} |表示申请者所在的媒体市场（用地区代码表示）。 <br /><br />此字段仅适用于源自美国国内的请求。| 745 |
| HTTP 请求方法 | %{request_method} | 指示 HTTP 请求方法。 | GET |
| HTTP 状态代码 | %{status} | 指示响应的 HTTP 状态代码。 | 200 |
| IP 地址（申请者） | %{virt_dst_addr} | 指示请求方的 IP 地址。 | 192.168.1.1 |
| 纬度（申请者） | %{geo_latitude} | 指示申请者所处的纬度。 | 34.0995 |
| 经度（申请者） | %{geo_longitude} | 指示申请者所处的经度。 | -118.4143 |
| 大城市统计区（申请者） | %{geo_metro_code} | 指示申请者所在的大城市区域。 <br /><br />此字段仅适用于源自美国国内的请求。<br />| 745 |
| 端口（申请者） | %{virt_dst_port} | 指示申请者的临时端口。 | 55885 |
| 邮政编码（申请者） | %{geo_postal_code} | 指示申请者所在地的邮政编码。 | 90210 |
| 找到查询字符串 | %{is_args} | 此变量的值会根据请求是否包含查询字符串而不同。<br /><br />- 找到查询字符串：?<br />- 无查询字符串：NULL | ? |
| 找到查询字符串参数 | %{is_amp} | 此变量的值会根据请求是否包含至少一个查询字符串而不同。<br /><br />- 找到参数：&<br />- 无参数：NULL | & |
| 查询字符串参数值 | %{arg_&lt;parameter&gt;} | 返回的值对应于由 &lt;parameter&gt; 术语标识的查询字符串参数。 | 示例用法： <br />%{arg_language}<br /><br />示例查询字符串参数： <br />?language=en<br /><br />示例值：en |
| 查询字符串值 | %{query_string} | 指示在请求 URL 中定义的整个查询字符串值。 |key1=val1&key2=val2&key3=val3 |
| 引用站点域 | %{referring_domain} | 表示引用站点请求标头中定义的域。 | www.google.com |
| 地区（申请者） | %{geo_region} | 指示申请者所在的地区（例如州、省、直辖市或自治区），采用首字母缩写形式。 | CA |
| 请求标头值 | %{http_RequestHeader} | 返回的值对应于由 RequestHeader 术语标识的请求标头。 <br /><br />如果请求标头的名称包含短划线（例如，User-Agent），则使用下划线（例如，User_Agent）进行替换。| 示例用法：%{http_Connection}<br /><br />示例值：Keep-Alive | 
| 请求主机 | %{host} | 指示在请求 URL 中定义的主机。 | www.mydomain.com |
| 请求协议 | %{request_protocol} | 指示请求协议。 | HTTP/1.1 |
| 请求方案 | %{scheme} | 指示请求方案。 |http |
| 请求 URI（相对） | %{request_uri} | 指示相对路径，包括请求 URI 中定义的查询字符串。 | /marketing/foo.js?loggedin=true |
| 请求 URI（不含查询字符串的相对路径） | %{uri} | 指示请求的内容的相对路径。 <br /><br/>重要信息：<br />- 此相对路径不包括查询字符串。<br />- 此相对路径反映 URL 重写。 在以下情况下，将重写 URL：<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- URL 重写功能：此功能重写在请求 URI 中定义的相对路径。<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- 边缘 CNAME URL：这种类型的请求会被相应的 CDN URL 重写。 |/800001/corigin/rewrittendir/foo.js |
| 请求 URI | %{request} | 描述请求。 <br />语法：&lt;HTTP 方法&gt;&lt;相对路径&gt;&lt;HTTP 协议&gt; | GET /marketing/foo.js?loggedin=true HTTP/1.1 |
| 响应标头值 | %{resp_&lt;ResponseHeader&gt;} | 返回的值对应于由 &lt;ResponseHeader&gt; 术语标识的响应标头。 <br /><br />如果响应标头的名称包含短划线（例如，User-Agent），则使用下划线（例如，User_Agent）进行替换。 | 示例用法：%{resp_Content_Length}<br /><br />示例值：100 |

## <a name="usage"></a>使用情况
下表介绍用于指定 HTTP 变量的正确语法。


| 语法 | 示例 | 说明 |
| ------ | -------- | ---------- |
| %{&lt;HTTPVariable&gt;} | %{host} | 使用此语法获取对应于指定的 &lt;HTTPVariable&gt; 的整个值。 |
| %{&lt;HTTPVariableDelimiter&gt;} | %{host,} | 使用此语法设置对应于指定的 &lt;HTTPVariableDelimiter&gt; 的整个值。 |
| %{&lt;HTTPVariableDelimiterExpression&gt;} | %{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80} | 使用 &lt;HTTPVariableDelimiterExpression&gt; 的正则表达式替换、删除或操作 HTTP 变量值。 |

HTTP 变量名仅支持字母字符和下划线。 请将不支持的字符转换为下划线。

## <a name="delimiter-reference"></a>分隔符引用
可在 HTTP 变量后面指定分隔符，实现任意以下效果：

- 转换与该变量关联的值。

     示例：将整个值转换为小写。

- 删除与该变量关联的值。

- 操作与该变量关联的值。

     示例：使用正则表达式更改与 HTTP 变量关联的值。

下表描述了这些分隔符。

| 分隔符 | 说明 |
| --------- | ----------- |
| := | 指示变量为以下情况时将向其分配默认值： <br />- 缺失 <br />- 设置为 NULL。 |
| :+ | 指示变量已分配有一个值时将向其分配默认值。 |
| : | 指示将扩展分配给变量的值的子字符串。 |
| # | 指示此分隔符出现在与变量关联的值的开头时，应删除在其后面指定的模式。 |
| % | 指示此分隔符出现在与变量关联的值的末尾时，应删除在其后面指定的模式。 <br />此定义仅适用于 % 符号用作分隔符的情况。 |
| / | 分隔 HTTP 变量或模式。 |
| // | 查找和替换指定模式的所有实例。 |
| /= | 查找、复制和重写指定模式的所有匹配项。 |
| ， | 将与 HTTP 变量关联的值转换为小写。 |
| ^ | 将与 HTTP 变量关联的值转换为大写。 |
| ,, | 将与 HTTP 变量关联的值中的指定字符的所有实例转换为小写。 |
| ^^ | 将与 HTTP 变量关联的值中的指定字符的所有实例转换为大写。 |

## <a name="exceptions"></a>例外
下表描述了指定文本未被视为 HTTP 变量的情况。

| 条件 | 说明 | 示例 |
| --------- | ----------- | --------|
| 转义 % 符号 | 可使用反斜杠转义百分比符号。 <br />右侧的示例值将被视为文本值，不会被视为 HTTP 变量。| \%{host} |
| 未知变量 | 未知变量始终返回空字符串。 | %{unknownvariable} |
| 字符或语法无效 | 包含无效字符或语法的变量将被视为文本值。 <br /><br />示例 #1：指定的值包含无效字符（例如 , -）。 <br /><br />示例 #2：指定的值包含两对大括号。 <br /><br />示例 #3：指定的值缺少右大括号。<br /> | 示例 #1：%{resp_user-agent} <br /><br />示例 #2：%{{host}} <br /><br />示例 #3：%{host |
| 缺少变量名 | 未指定变量时，始终返回 NULL 值。 | %{} |
| 尾随字符 | 结尾为变量的字符被视为文本值。 <br />右侧的示例值包含将被视为文本值的尾随大括号。 | %{host}} |

## <a name="setting-default-header-values"></a>设置默认标头值
当满足以下任何条件时，可以将默认值分配给标头：
- 缺少/取消设置
- 设置为 NULL。

下表介绍了如何定义默认值。

| 条件 | 语法 | 示例 | 说明 |
| --------- | ------ | --------| ----------- |
| 当满足以下任何条件时，可将标头设置为默认值： <br /><br />- 缺少标头 <br /><br />- 标头值设置为 NULL。| %{Variable:=Value} | %{http_referer:=unspecified} | 当 Referer 标头缺失或设置为 NULL 时，只能将其设置为“未指定”。 如果已设置，则无需执行任何操作。 |
| 如果标头未设置，请将其设置为默认值。 | %{Variable=Value} | %{http_referer=unspecified} | 缺少 Referer 标头时，只能将其设置为“未指定”。 如果已设置，则无需执行任何操作。 |
| 如果不满足以下任何条件，请将标头设置为默认值： <br /><br />- 缺失<br /><br /> - 设置为 NULL。 | %{Variable:+Value} | %{http_referer:+unspecified} | 如果 Referer 标头分配有一个值，则只能将其设置为“未指定”。 如果该标头丢失或设置为 NULL，则无需执行任何操作。 |

## <a name="manipulating-variables"></a>操作变量
可以通过以下方式操作变量：

- 扩展子字符串
- 删除模式

### <a name="substring-expansion"></a>子字符串扩展
默认情况下，变量将扩展到其完整值。 使用以下语法仅扩展变量值的子字符串。

`%<Variable>:<Offset>:<Length>}`

重要信息：

- 分配到 Offset 术语的值确定子字符串的起始字符：

     - 正值：子字符串的起始字符从字符串中的第一个字符进行计算。
     - 零值：子字符串的起始字符是字符串中的第一个字符。
     - 负值：子字符串的起始字符从字符串中的最后一个字符进行计算。

- 子字符串的长度由 Length 术语确定：

     - 省略：省略 Length 术语后，可在子字符串的起始字符和结束字符之间包括所有字符。
     - 正值：确定从起始字符到右侧的子字符串长度。
     - 负值：确定从起始字符到左侧的子字符串长度。

#### <a name="example"></a>示例：

下例依赖于以下示例请求 URL：

https:\//cdn.mydomain.com/folder/marketing/myconsultant/proposal.html

以下字符串演示用于操作变量的各种方法：

https:\//www%{http_host:3}/mobile/%{request_uri:7:10}/%{request_uri:-5:-8}.htm

根据示例请求 URL，上述变量操作将生成以下值：

https:\//www.mydomain.com/mobile/marketing/proposal.htm


### <a name="pattern-removal"></a>删除模式
可从变量值的开始处或末尾删除与特定模式匹配的文本。

| 语法 | 操作 |
| ------ | ------ |
| %{Variable#Pattern} | 在变量值的开头出现指定模式时删除文本。 |
| %{Variable%Pattern} | 在变量值的末尾出现指定模式时删除文本。 |

#### <a name="example"></a>示例：

在此示例方案中，request_uri 变量设置为：

`/800001/myorigin/marketing/product.html?language=en-US`

下表说明了此语法的工作原理。

| 示例语法 | 结果 |
| ------------- | ------- |
| %{request_uri#/800001}/customerorigin | /customerorigin/myorigin/marketing/product.html?language=zh-CN | 由于变量以此模式开头，因此该变量已被替换。 |
| %{request_uri%html}htm | /800001/myorigin/marketing/product.html?language=zh-CN | 该变量未以此模式结尾，因此未进行任何更改。|

### <a name="find-and-replace"></a>查找和替换
下表说明查找和替换语法。

| 语法 | 操作 |
| ------ | ------ |
| %{Variable/Find/Replace} | 查找和替换指定模式中的第一个匹配项。 |
| %{Variable//Find/Replace} | 查找和替换指定模式中的所有匹配项。 |
| %{Variable^} |将整个值转换为大写。 |
| %{Variable^Find} | 将指定模式中的第一个匹配项转换为大写。 |
| %{Variable,} | 将整个值转换为小写。 |
| %{Variable,Find} | 将指定模式中的第一个匹配项转换为小写。 |

### <a name="find-and-rewrite"></a>查找和重写
对于查找和替换中的一个变体，请使用重写时与指定模式匹配的文本。 下表说明查找和重写语法。

| 语法 | 操作 |
| ------ | ------ |
| %{Variable/=Find/Rewrite} | 查找、复制和重写指定模式的所有匹配项。 |
| %{Variable/^Find/Rewrite} | 查找、复制和重写出现在变量开头的指定模式。 |
| %{Variable/$Find/Rewrite} | 查找、复制和重写出现在变量末尾的指定模式。 |
| %{Variable/Find} | 查找和删除指定模式中的所有匹配项。 |

重要信息：

- 在整数前面指定一个美元符号（例如 $1），展开与指定模式匹配的文本。

- 可以指定多个模式。 模式的指定顺序可确定其分配到的整数。 在下例中，第一个模式与“www.,”匹配，第二个模式与第二级域匹配，第三个模式与顶级域匹配：

    `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80}`

- 重写的值可以包含文本和这些占位符的任意组合。

    在上例中，主机名被重写为 `cdn.$2.$3:80`（例如，cdn.mydomain.com:80）。

- 可通过以下标志修改这种模式占位符（例如，$1）：
     - U：大写扩展的值。

         示例语法：

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$U2.$3:80}`

     - L：大写扩展的值。

         示例语法：

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$L2.$3:80}`

- 必须在模式前面指定运算符。 指定的运算符可确定捕获模式的行为：

     - `=`：指示必须捕获和重写指定模式的所有匹配项。
     - `^`：指示将捕获以指定模式开头的文本。
     - `$`：指示将捕获以指定模式结尾的文本。
 
- 如果省略 /Rewrite值，则会删除与模式匹配的文本。


