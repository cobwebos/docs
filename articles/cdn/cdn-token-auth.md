---
title: "使用令牌身份验证保护 Azure CDN 资产 | Microsoft Docs"
description: "使用令牌身份验证保护对 Azure CDN 资产的访问。"
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: 
ms.assetid: 837018e3-03e6-4f9c-a23e-4b63d5707a64
ms.service: cdn
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/11/2016
ms.author: mezha
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 42b182c314795b1ebf69639ec7ac5583208dc7c1
ms.lasthandoff: 03/29/2017


---


# <a name="securing-azure-cdn-assets-with-token-authentication"></a>使用令牌身份验证保护 Azure CDN 资产

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

##<a name="overview"></a>概述

令牌身份验证是一种机制，允许你阻止 Azure CDN 向未经授权的客户端提供资产。  通常这样做是为了防止内容的“热链接”，即不同的网站（通常是信息板）未经允许就使用你的资产。  这可能会影响你的内容交付费用。 通过在 CDN 上启用此功能，请求将在交付内容之前通过 CDN 边缘 POP 进行身份验证。 

## <a name="how-it-works"></a>工作原理

令牌身份验证将验证请求是否由受信任的站点通过要求请求包含令牌值（该值包含关于请求者的编码信息）而生成。 内容只会在编码信息满足要求时提供给请求者，否则请求将被拒绝。 可以使用下面的一个或多个参数设置要求。

- 国家/地区：允许或拒绝源自指定国家/地区的请求。  [有效的国家/地区代码列表。](https://msdn.microsoft.com/library/mt761717.aspx) 
- URL：仅允许指定的资产或路径请求。  
- 主机：允许或拒绝在请求标头中使用指定主机的请求。
- 引用网站：允许或拒绝指定的引用网站请求。
- IP 地址：仅允许源自特定 IP 地址或 IP 子网的请求。
- 协议：根据用于请求内容的协议，允许或阻止请求。
- 过期时间：分配日期和时间段，以确保链接仅在有限的时间段内保持有效。

请参见各参数的详细配置示例。

## <a name="reference-architecture"></a>参考体系结构

请参阅下面的在 CDN 上设置令牌身份验证以使用 Web 应用的参考体系结构。

![CDN 配置文件边栏选项卡管理按钮](./media/cdn-token-auth/cdn-token-auth-workflow2.png)

## <a name="token-validation-logic-on-cdn-endpoint"></a>CDN 终结点上的令牌验证逻辑
    
此图表描述了在 CDN 终结点上配置令牌身份验证时，Azure CDN 如何验证客户端请求。

![CDN 配置文件边栏选项卡管理按钮](./media/cdn-token-auth/cdn-token-auth-validation-logic.png)

## <a name="setting-up-token-authentication"></a>设置令牌身份验证

1. 在 [Azure门户](https://portal.azure.com)中，浏览到 CDN 配置文件，然后单击“管理”按钮启动补充门户。

    ![CDN 配置文件边栏选项卡管理按钮](./media/cdn-rules-engine/cdn-manage-btn.png)

2. 将鼠标悬停在“HTTP Large”上，然后在浮出控件中单击“令牌身份验证”。 将在此选项卡中设置加密密钥和加密参数。

    1. 为“主密钥”输入唯一的加密密钥。  为“备份密钥”输入另一个唯一的加密密钥。

        ![CDN 令牌身份验证设置密钥](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
    2. 使用加密工具设置加密参数（根据过期时间、国家/地区、引用网站、协议、客户端 IP 允许或拒绝请求。 可以使用任意组合。）

        ![CDN 配置文件边栏选项卡管理按钮](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

        - ec-expire：在指定的时间段之后分配令牌的过期时间。 过期时间之后提交的请求将被拒绝。 此参数使用 Unix 时间戳（基于自 1970/1/1 00:00:00 GMT 标准历元后的秒数。 可以使用在线工具提供标准时间与 Unix 时间之间的转换。）例如，如果要将令牌设置为在 2016/12/31 12:00:00 GMT 时过期，则使用 Unix 时间：1483185600，如下所示：
    
        ![CDN 配置文件边栏选项卡管理按钮](./media/cdn-token-auth/cdn-token-auth-expire2.png)
    
        - ec-url-allow：允许你为特定资产或路径定制令牌。 它限制对 URL 以特定相对路径开头的请求的访问。 可以输入多个路径（每个路径之间用逗号分隔）。 URL 区分大小写。 根据需要，可为不同的访问级别设置不同的值。 以下是几种方案：
        
            如果你有一个 URL：http://www.mydomain.com/pictures/city/strasbourg.png。 查看相应的输入值 "" 及其访问级别

            1. 输入值“/”：允许所有请求
            2. 输入值“/pictures”：允许以下所有请求
            
                - http://www.mydomain.com/pictures.png
                - http://www.mydomain.com/pictures/city/strasbourg.png
                - http://www.mydomain.com/picturesnew/city/strasbourgh.png
            3. 输入值“/pictures/”：仅允许 /pictures/ 的请求
            4. 输入值“/pictures/city/strasbourg.png”：仅允许此资产的请求
    
        ![CDN 配置文件边栏选项卡管理按钮](./media/cdn-token-auth/cdn-token-auth-url-allow4.png)
    
        - ec-country-allow：仅允许从一个或多个指定国家/地区发出的请求。 源自所有其他国家/地区的请求将被拒绝。 使用国家/地区代码来设置参数（每个国家/地区之间用逗号分隔）。 例如，如果要允许来自美国和法国的访问，请在下面的列中输入 US、FR。  
        
        ![CDN 配置文件边栏选项卡管理按钮](./media/cdn-token-auth/cdn-token-auth-country-allow.png)

        - ec-country-deny：拒绝从一个或多个指定国家/地区发出的请求。 将允许源自所有其他国家/地区的请求。 使用国家/地区代码来设置参数（每个国家/地区之间用逗号分隔）。 例如，如果要拒绝来自美国和法国的访问，请在列中输入 US、FR。
    
        - ec-ref-allow：仅允许来自指定引用网站的请求。 引用网站标识链接到所请求资源的网页的 URL。 引用网站参数值不应包括协议。 可在该主机名上输入主机名和/或特定路径。 可以在单个参数中添加多个引用网站（每个应用网站之间用逗号分隔）。 如果指定了引用网站值，但由于某些浏览器配置导致请求中未发送引用网站信息，则将默认拒绝这些请求。 可以在参数中指定“缺少”或空白值，以允许这些缺少引用信息的请求。 也可以使用“*.consoto.com”来允许 consoto.com 的所有子域。  例如，如果要允许来自 www.consoto.com 和 consoto2.com 下的所有子域的访问请求，以及包含空白值或缺少引用信息的请求，请在下面输入值：
        
        ![CDN 配置文件边栏选项卡管理按钮](./media/cdn-token-auth/cdn-token-auth-referrer-allow2.png)
    
        - ec-ref-deny：拒绝来自指定引用网站的请求。 请参阅“ec-ref-allow”参数中的详细信息和示例。
         
        - ec-proto-allow：仅允许来自指定协议的请求。 例如，http 或 https。
        
        ![CDN 配置文件边栏选项卡管理按钮](./media/cdn-token-auth/cdn-token-auth-url-allow4.png)
            
        - ec-proto-deny：拒绝来自指定协议的请求。 例如，http 或 https。
    
        - ec-clientip：限制对指定请求者的 IP 地址的访问。 支持 IPV4 和 IPV6。 可以指定单个请求 IP 地址或 IP 子网。
            
        ![CDN 配置文件边栏选项卡管理按钮](./media/cdn-token-auth/cdn-token-auth-clientip.png)
        
    3. 可以使用解密工具测试令牌。

    4. 可以自定义拒绝请求时将返回给用户的响应类型。 默认情况下使用的是 403。

3. 现在请单击“HTTP Large”下的“规则引擎”选项卡。 将使用此选项卡来定义应用功能、启用令牌身份验证功能以及启用其他令牌身份验证相关功能的路径。

    - 使用“IF”列定义要应用令牌身份验证的资产或路径。 
    - 单击以从功能下拉列表中添加“令牌身份验证”，以启用令牌身份验证。
        
    ![CDN 配置文件边栏选项卡管理按钮](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. 在“规则引擎”选项卡中，有一些可以启用的额外功能。
    
    - 令牌身份验证拒绝代码：确定拒绝请求时将返回给用户的响应类型。 此处设置的规则将替代“令牌身份验证”选项卡中的拒绝代码设置。
    - 令牌身份验证忽略：确定用于验证令牌的 URL 是否区分大小写。
    - 令牌身份验证参数：重命名在请求 URL 中显示的令牌身份验证查询字符串参数。 
        
    ![CDN 配置文件边栏选项卡管理按钮](./media/cdn-token-auth/cdn-rules-engine2.png)

5. 可以自定义令牌，该令牌是一种应用程序，它可以为基于令牌的身份验证功能生成令牌。 可在 [GitHub](https://github.com/VerizonDigital/ectoken) 中的此处访问源代码。
可用的语言包括：
    
    - C
    - C#
    - PHP
    - Perl
    - Java
    - Python    


## <a name="azure-cdn-features-and-provider-pricing"></a>Azure CDN 功能和提供程序定价

请参阅 [CDN 概述](cdn-overview.md)主题。

