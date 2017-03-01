---
title: "在 Azure CDN 自定义域上启用 HTTPS | Microsoft 文档"
description: "了解如何在自定义域的 Azure CDN 终结点上启用 HTTPS。"
services: cdn
documentationcenter: 
author: camsoper
manager: erikre
editor: 
ms.assetid: 10337468-7015-4598-9586-0b66591d939b
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: casoper
translationtype: Human Translation
ms.sourcegitcommit: aea6f0fedb447e953b9db40342e5091bd35486af
ms.openlocfilehash: 9b80ae8c247480e69025b86fac322166a4d5cb4c


---
# <a name="enable-https-on-an-azure-cdn-custom-domain"></a>在 Azure CDN 自定义域上启用 HTTPS

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

利用对 Azure CDN 自定义域的 HTTPS 支持，可以通过 SSL 使用自己的域名传送安全内容以提高在传输过程中数据的安全性。 通过一键式启用、完整的证书管理可简化为自定义域启用 HTTPS 的端到端工作流，所有这些都不会增加成本。

在传输过程中确保所有 Web 应用程序敏感数据的隐私和数据完整性至关重要。 使用 HTTPS 协议可确保通过 Internet 发送敏感数据时对其进行加密。 它提供信任、身份验证并保护 Web 应用程序免受攻击。 目前，Azure CDN 在 CDN 终结点上支持 HTTPS。 例如，如果从 Azure CDN（例如 https://contoso.azureedge.net）创建 CDN 终结点，默认情况下，HTTPS 已启用。 现在，使用自定义域 HTTPS，可以为自定义域（例如 https://www.contoso.com）启用安全交付。 

HTTPS 功能的一些关键属性包括：

- 无需额外付费：证书获取或续订不收取费用，对于 HTTPS 流量不另外收费。 只需为从 CDN 出口的 GB 数付费。

- 简单启用：可从 [Azure 门户](https://portal.azure.com)进行一键式预配。 还可以使用 REST API 或其他开发人员工具启用该功能。

- 完整的证书管理：为你处理所有证书获取和管理。 在证书过期之前自动预配并续订证书。 这将完全消除由于证书过期而导致服务中断的风险。

>[!NOTE] 
>在启用 HTTPS 支持前，必须已建立 [Azure CDN 自定义域](./cdn-map-content-to-custom-domain.md)。

## <a name="step-1-enabling-the-feature"></a>步骤 1：启用功能 

1. 在 [Azure 门户](https://portal.azure.com)中，浏览到 Verizon 标准或高级 CDN 配置文件。

2. 在终结点的列表中，单击包含自定义域的终结点。

3. 单击要启用 HTTPS 的自定义域。

    ![“终结点”边栏选项卡](./media/cdn-custom-ssl/cdn-custom-domain.png)

4. 单击“启用”以启用 HTTPS 并保存更改。

    ![“自定义 HTTPS”对话框](./media/cdn-custom-ssl/cdn-enable-custom-ssl.png)


## <a name="step-2-domain-validation"></a>步骤 2：域验证

>[!IMPORTANT] 
>必须先完成域验证，然后才能在自定义域上激活 HTTPS。 你有 6 个工作日来批准域。 如果 6 个工作日内未批准，请求将被取消。  

在自定义域上启用 HTTPS 之后，我们的 HTTPS 证书提供商 DigiCert 将根据 WHOIS 注册者信息，通过电子邮件（默认情况下）或电话通过联系域的注册者来验证域的所有权。 如果 WHOIS 注册者信息是私有的，DigiCert 会将验证电子邮件发送到 **admin@*<your-domain-name.com>***。

收到电子邮件后，你有两个验证选项：

1. 可批准通过同一根域（例如 consoto.com）的同一帐户下的所有将来订单。 如果你计划在将来为同一根域添加其他自定义域，这是建议的方法。
 
2. 可以只批准该请求中使用的特定主机名。 后续请求将需要其他批准。

    示例电子邮件：
    
    ![“自定义 HTTPS”对话框](./media/cdn-custom-ssl/domain-validation-email-example.png)

批准后，DigiCert 会将你的自定义域名添加到 SAN 证书。 该证书将在一年内有效，并将在过期前自动续订。

## <a name="step-3-wait-for-the-propagation-then-start-using-your-feature"></a>步骤 3：等待传播，然后开始使用你的功能

验证域名后，将需要长达 6-8 小时才能使自定义域 HTTPS 功能激活。 在该过程完成后，Azure 门户中的“自定义 HTTPS”状态将设置为“已启用”。 现在，自定义域的 HTTPS 已可供你使用。

## <a name="frequently-asked-questions"></a>常见问题

1. *谁是证书提供商？使用哪种类型的证书？*

    我们使用 DigiCert 提供的使用者备用名称 (SAN) 证书。 SAN 证书可以使用一个证书保护多个完全限定域名。

2. *能否使用我的专用证书？*
    
    目前不能，但它在规划之中。

3. *如果我未收到 DigiCert 发来的域验证电子邮件，怎么办？*

    如果你在 24 小时内未收到电子邮件，请与 Microsoft 联系。

4. *使用 SAN 证书是否没有使用专用证书安全？*
    
    SAN 证书遵循与专用证书相同的加密和安全标准。 所有颁发的 SSL 证书都使用 SHA-256 来增强服务器安全性。

5. *能否将自定义域 HTTPS 用于 Akamai 的 Azure CDN？*

    目前，只有 Verizon 的 Azure CDN 提供此功能。 我们正在努力在未来几个月内对 Akamai 的 Azure CDN 支持此功能。


## <a name="next-steps"></a>后续步骤

- 了解如何[在Azure CDN 终结点上设置自定义域](./cdn-map-content-to-custom-domain.md)





<!--HONumber=Feb17_HO1-->


