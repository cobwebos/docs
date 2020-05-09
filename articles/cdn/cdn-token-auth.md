---
title: 使用令牌身份验证保护 Azure CDN 资产 | Microsoft Docs
description: 了解如何使用令牌身份验证保护对 Azure CDN 资产的访问。
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: ''
ms.assetid: 837018e3-03e6-4f9c-a23e-4b63d5707a64
ms.service: azure-cdn
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/17/2017
ms.author: mazha
ms.openlocfilehash: 491f413f9bf189b1a46d04042fd7223a47af1f24
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82929122"
---
# <a name="securing-azure-cdn-assets-with-token-authentication"></a>使用令牌身份验证保护 Azure CDN 资产

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>概述

令牌身份验证是一种机制，允许阻止 Azure 内容分发网络 (CDN) 向未经授权的客户端提供资产。 进行令牌身份验证通常是为了防止内容的“热链接”**，即不同的网站（如信息板）未经允许就使用资产。 热链接可能会影响内容交付费用。 在 CDN 上启用令牌身份验证，将先由 CDN 边缘服务器对请求进行身份验证，再由 CDN 交付内容。 

## <a name="how-it-works"></a>工作原理

令牌身份验证会验证请求是否由受信任的站点通过要求请求包含令牌值（该值包含关于请求者的编码信息）而生成。 内容只会在编码信息满足要求时提供给请求者，否则请求会被拒绝。 可使用以下一个或多个参数来设置需求：

- 国家/地区：允许或拒绝来自国家/地区[代码](/previous-versions/azure/mt761717(v=azure.100))所指定国家/地区的请求。
- URL：仅允许与指定资产或路径相匹配的请求。
- 主机：允许或拒绝在请求标头中使用指定主机的请求。
- 引用网站：允许或拒绝来自指定引用网站的请求。
- IP 地址：仅允许源自特定 IP 地址或 IP 子网的请求。
- 协议：根据用于请求内容的协议，允许或阻止请求。
- 过期时间：分配日期和时间段，以确保链接仅在有限的时间段内保持有效。

有关详细信息，请参阅[设置令牌身份验证](#setting-up-token-authentication)中每个参数的详细配置示例。

>[!IMPORTANT] 
> 如果对此帐户中的任何路径启用了令牌授权，则标准缓存模式是可用于查询字符串缓存的唯一模式。 有关详细信息，请参阅[使用查询字符串控制 Azure CDN 缓存行为](cdn-query-string-premium.md)。

## <a name="reference-architecture"></a>参考体系结构

以下工作流关系图介绍了CDN 通过令牌身份验证来使用 Web 应用的工作原理。

![CDN 令牌身份验证工作流](./media/cdn-token-auth/cdn-token-auth-workflow2.png)

## <a name="token-validation-logic-on-cdn-endpoint"></a>CDN 终结点上的令牌验证逻辑
    
以下流程图描述了在 CDN 终结点上配置令牌身份验证时，Azure CDN 如何验证客户端请求。

![CDN 令牌验证逻辑](./media/cdn-token-auth/cdn-token-auth-validation-logic.png)

## <a name="setting-up-token-authentication"></a>设置令牌身份验证

1. 在 [Azure门户](https://portal.azure.com)中，浏览到 CDN 配置文件，并选择“管理”按钮启动补充门户****。

    ![CDN 配置文件管理按钮](./media/cdn-token-auth/cdn-manage-btn.png)

2. 将鼠标指针悬停在“HTTP Large”**** 上，然后在浮出控件中选择“令牌身份验证”****。 然后即可设置加密密钥和加密参数，如下所示：

   1. 创建一个或多个加密密钥。 加密密钥区分大小写，可以包含字母数字字符的任意组合。 不允许其他任何类型的字符，包括空格。 最大长度为 250 个字符。 为了确保加密密钥是随机的，建议使用[OpenSSL 工具](https://www.openssl.org/)创建它们。 

      OpenSSL 工具采用以下语法：

      ```rand -hex <key length>```

      例如：

      ```OpenSSL> rand -hex 32``` 

      为了避免停机，请创建主密钥和备用密钥。 更新主密钥时，可以使用备用密钥不间断地访问内容。
    
   2. 在“主密钥”框中输入唯一的加密密钥，并在“备份密钥”框中输入备份密钥（可选）********。

   3. 从每个密钥的“最低加密版本”**** 列表中选择其最低加密版本，然后选择“更新”****：
      - **V2**：指示该密钥可用于生成版本 2.0 和 3.0 令牌。 仅当要从旧版 2.0 加密密钥过渡到 3.0 版密钥时，才使用此选项。
      - **V3**：（建议）指示该密钥只可用于生成 3.0 令牌。

      ![CDN 令牌身份验证设置密钥](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
   4. 使用加密工具设置加密参数并生成令牌。 利用加密工具，你可以根据过期时间、国家/地区、引用者、协议和客户端 IP （任意组合）来允许或拒绝请求。 尽管可以组合用来构成令牌的参数数目与组合没有限制，但令牌的总长度限制为 512 个字符。 

      ![CDN 加密工具](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

      在“加密工具”部分中，输入以下加密参数中的一个或多个值：**** 

      > [!div class="mx-tdCol2BreakAll"] 
      > <table>
      > <tr>
      >   <th>参数名称</th> 
      >   <th>说明</th>
      > </tr>
      > <tr>
      >    <td><b>ec_expire</b></td>
      >    <td>为令牌分配一个过期时间，超过该时间令牌即过期。 过期时间之后提交的请求会被拒绝。 此参数使用 Unix 时间戳，该时间戳基于自 `1/1/1970 00:00:00 GMT` 的标准 Unix 时期开始所累计的总秒数。 （可以使用在线工具在标准时间与 Unix 时间之间进行转换。）> 
      >    例如，如果要令牌在 `12/31/2016 12:00:00 GMT` 时过期，请输入 Unix 时间戳值 `1483185600`。 
      > </tr>
      > <tr>
      >    <td><b>ec_url_allow</b></td> 
      >    <td>允许为特定资产或路径定制令牌。 它限制对 URL 以特定相对路径开头的请求的访问。 URL 区分大小写。 输入多个路径（每个路径之间用逗号分隔）；请勿添加空格。 根据需要，可为不同的访问级别设置不同的值。> 
      >    例如,对于 URL `http://www.mydomain.com/pictures/city/strasbourg.png`，以下输入值分别允许以下请求： 
      >    <ul>
      >       <li>输入值 `/`：允许所有请求。</li>
      >       <li>输入值 `/pictures`：允许以下请求： <ul>
      >          <li>`http://www.mydomain.com/pictures.png`</li>
      >          <li>`http://www.mydomain.com/pictures/city/strasbourg.png`</li>
      >          <li>`http://www.mydomain.com/picturesnew/city/strasbourgh.png`</li>
      >       </ul></li>
      >       <li>输入值 `/pictures/`：仅允许包含 `/pictures/` 路径的请求。 例如 `http://www.mydomain.com/pictures/city/strasbourg.png`。</li>
      >       <li>输入值 `/pictures/city/strasbourg.png`：仅允许对此特定路径和资产的请求。</li>
      >    </ul>
      > </tr>
      > <tr>
      >    <td><b>ec_country_allow</b></td> 
      >    <td>仅允许来自一个或多个指定国家/地区的请求。 来自所有其他国家/地区的请求将被拒绝。 为每个国家/地区使用双字母 [ISO 3166 国家/地区代码](/previous-versions/azure/mt761717(v=azure.100))，并用逗号分隔；请勿添加空格。 例如，如果仅允许来自美国和法国的访问，请输入 `US,FR`。</td>
      > </tr>
      > <tr>
      >    <td><b>ec_country_deny</b></td> 
      >    <td>拒绝来自一个或多个指定国家/地区的请求。 允许来自所有其他国家/地区的请求。 其实现与 ec_country_allow 参数相同<b></b>。 如果 ec_country_allow 和 ec_country_deny 参数中都存在国家/地区代码，则 ec_country_allow 参数优先<b></b><b></b><b></b>。</td>
      > </tr>
      > <tr>
      >    <td><b>ec_ref_allow</b></td>
      >    <td>仅允许来自指定引用网站的请求。 引用网站标识链接到所请求资源的网页的 URL。 请勿在参数值中包括协议。> 
      >    允许以下类型的输入：
      >    <ul>
      >       <li>主机名或主机名与路径。</li>
      >       <li>多个引用网站。 若要添加多个引用网站，请用逗号分隔每个引用网站；请勿添加空格。 如果指定了引用网站值，但由于浏览器配置导致请求中未发送引用网站信息，则将默认拒绝这些请求。</li> 
      >       <li>含缺失或空白引用网站信息的请求。 默认情况下，ec_ref_allow 参数会阻止这些类型的请求<b></b>。 要允许这些请求，请输入文本“missing”，或输入空白值（通过使用尾随逗号）。</li> 
      >       <li>子域。 要允许子域，请输入一个星号 (\*)。 例如，要允许 `contoso.com` 的所有子域，请输入 `*.contoso.com`。</li>
      >    </ul> 
      >    例如，要允许访问来自 `www.contoso.com` 的请求、`contoso2.com` 下所有子域的请求，以及引用网站为空白或缺失的请求，请输入 `www.contoso.com,*.contoso.com,missing`。</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_ref_deny</b></td>
      >    <td>拒绝来自指定引用网站的请求。 实现与<b>ec_ref_allow</b>参数相同。 如果 ec_ref_allow 和 ec_ref_deny 参数中都存在引用网站，则 ec_ref_allow 参数优先<b></b><b></b><b></b>。</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_proto_allow</b></td> 
      >    <td>仅允许来自指定协议的请求。 有效值为 `http`、`https` 或 `http,https`。</td>
      > </tr>
      > <tr>
      >    <td><b>ec_proto_deny</b></td>
      >    <td>拒绝来自指定协议的请求。 其实现与 ec_proto_allow 参数相同<b></b>。 如果 ec_proto_allow 和 ec_proto_deny 参数中都存在协议，则 ec_proto_allow 参数优先<b></b><b></b><b></b>。</td>
      > </tr>
      > <tr>
      >    <td><b>ec_clientip</b></td>
      >    <td>限制对指定请求者的 IP 地址的访问。 支持 IPV4 和 IPV6。 可以指定单个请求 IP 地址或与特定子网关联的 IP 地址。 例如，`11.22.33.0/22` 允许从 IP 地址 11.22.32.1 到 11.22.35.254 的请求。</td>
      > </tr>
      > </table>

   5. 在输入加密参数值后，如果已同时创建了主密钥和备份密钥，请从“要加密的密钥”列表中选择要加密的密钥类型。****
    
   6. 从“加密版本”列表中选择一个加密版本：“V2”表示版本 2，“V3”表示版本 3（建议）。************ 

   7. 选择“加密”**** 生成令牌。

      生成令牌后，它会显示在“生成的令牌”框中。**** 若要使用该令牌，请将其作为查询字符串追加​​到文件 URL 路径的末尾。 例如 `http://www.domain.com/content.mov?a4fbc3710fd3449a7c99986b`。
        
   8. 根据需要使用解密工具测试令牌，以便可以查看令牌的参数。 将令牌值粘贴到“要解密的令牌”框中****。 从“要解密的密钥”**** 列表中选择加密密钥，然后选择“解密”****。

      解密令牌后，其参数会显示在“原始参数”框中。****

   9. 可以自定义请求被拒绝时返回的响应代码类型。 选择“启用”****，然后选择“响应代码”**** 列表中的响应代码。 “标头名称”**** 自动设置为“位置”****。 选择“保存”****，实现新的响应代码。 对于某些响应代码，还必须在“标头值”**** 框中输入错误页面的 URL。 默认情况下将选择 403 响应代码（禁止）****。 

3. 在“HTTP Large”下，选择“规则引擎”********。 使用此规则引擎来定义应用功能、启用令牌身份验证功能以及启用其他令牌身份验证相关功能的路径。 有关详细信息，请参阅[规则引擎引用](cdn-rules-engine-reference.md)。

   1. 选择现有规则或创建新规则，定义想要应用令牌身份验证的资产或路径。 
   2. 要对规则启用令牌身份验证，请从“功能”**** 列表中选择[“令牌身份验证”](cdn-verizon-premium-rules-engine-reference-features.md#token-auth)****，然后选择“启用”****。 选择“更新”更新规则，或选择“添加”创建规则********。
        
      ![CDN 规则引擎令牌身份验证启用示例](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. 在规则引擎中，还可启用与令牌身份验证相关的其他功能。 要启用以下任意功能，请从“功能”列表**** 中选择它，然后选择“启用”****。
    
   - [令牌身份验证拒绝代码](cdn-verizon-premium-rules-engine-reference-features.md#token-auth-denial-code)：确定拒绝请求时会返回给用户的响应类型****。 在此处设置的规则将替代在基于令牌的身份验证页上的“自定义拒绝处理”部分设置的响应代码****。

   - [令牌身份验证忽略 URL 大小写](cdn-verizon-premium-rules-engine-reference-features.md#token-auth-ignore-url-case)：确定用于验证令牌的 URL 是否区分大小写****。

   - [令牌身份验证参数](cdn-verizon-premium-rules-engine-reference-features.md#token-auth-parameter)：重命名在请求 URL 中显示的令牌身份验证查询字符串参数****。 
        
     ![CDN 规则引擎令牌身份验证设置示例](./media/cdn-token-auth/cdn-rules-engine2.png)

5. 可访问 [GitHub](https://github.com/VerizonDigital/ectoken) 中的源代码以自定义令牌。
   可用的语言包括：
    
   - C
   - C#
   - PHP
   - Perl
   - Java
   - Python 

## <a name="azure-cdn-features-and-provider-pricing"></a>Azure CDN 功能和提供程序定价

有关功能的信息，请参阅 [Azure CDN 产品功能](cdn-features.md)。 有关定价的信息，请参阅[内容分发网络定价](https://azure.microsoft.com/pricing/details/cdn/)。
