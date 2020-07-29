---
title: Azure AD 应用程序代理中的自定义域 | Microsoft 文档
description: 在 Azure AD 应用程序代理中配置和管理自定义域。
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/24/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 646a32509921709711b208c263ac6b077555eac5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84764904"
---
# <a name="configure-custom-domains-with-azure-ad-application-proxy"></a>使用 Azure AD 应用程序代理配置自定义域

通过 Azure Active Directory 应用程序代理发布应用程序时，可创建外部 URL 供用户使用。 此 URL 的默认域为 yourtenant.msappproxy.net。 例如，如果在名为 Contoso 的租户中发布了一个名为 Expenses 的应用，则外部 URL 为 https:\//expenses-contoso.msappproxy.net  。 如果想要使用自己的域名而不是 msappproxy.net，请为应用程序配置自定义域。 

## <a name="benefits-of-custom-domains"></a>自定义域的优势

如果可能，最好为应用设置自定义域。 使用自定义域的部分原因包括：

- 应用之间的链接在公司网络外部仍然有效。 没有自定义域的话，如果你的应用具有硬编码指向应用程序代理外部目标的内部链接，且这些链接不可在外部解析，那么这些链接将失效。 当内部和外部 URL 相同时，请避免出现此问题。 如果无法使用自定义域，请参阅[使用 Azure AD 应用程序代理重定向已发布应用的硬编码链接](../application-proxy-link-translation.md)，获取可解决此问题的其他方法。 
  
- 用户的体验将更简单，因为他们可在你的网络内部或外部使用相同的 URL 访问应用。 他们无需了解不同的内部和外部 URL，也无需跟踪其当前位置。 

- 你可控制你的品牌并创建所需的 URL。 自定义域可帮助培养用户的信心，因为用户会看到和使用熟悉的名称，而不是 msappproxy.net。

- 某些配置将仅适用于自定义域。 例如，需要将自定义域用于使用安全断言标记语言 (SAML) 的应用，例如当你使用 Active Directory 联合身份验证服务 (AD FS)，但没法使用 WS 联合身份验证时。 有关详细信息，请参阅[在应用程序代理中使用声明感知应用](application-proxy-configure-for-claims-aware-applications.md)。 

如果无法使内部和外部 URL 匹配，则使用自定义域并不重要，但你仍可利用其他优势。 

## <a name="dns-configuration-options"></a>DNS 配置选项

有几种方法来设置 DNS 配置，具体取决于你的需求：

### <a name="same-internal-and-external-url-different-internal-and-external-behavior"></a>内部和外部 URL 相同，但内部和外部行为不同 

如果不希望通过应用程序代理来定向内部用户，可设置“拆分式 DNS”。 拆分式 DNS 基础结构会将内部主机定向到内部域名称服务器，将外部主机定向到外部域名称服务器以进行名称解析。 

![拆分式 DNS](./media/application-proxy-configure-custom-domain/split-brain-dns.png)

### <a name="different-internal-and-external-urls"></a>内部和外部 URL 不同 

如果内部和外部 URL 不同，则无需配拆分式行为，因为用户路由是由 URL 决定的。 在这种情况下，你只需更改外部 DNS 并将外部 URL 路由到应用程序代理终结点。 

如果为外部 URL 选择了自定义域，信息栏将显示你需要添加到外部 DNS 提供程序的 CNAME 条目。 始终可转到应用的“应用程序代理”页面来查看此信息。

## <a name="set-up-and-use-custom-domains"></a>设置并使用自定义域

要将本地应用配置为使用自定义域，需具有已验证的 Azure Active Directory 自定义域、自定义域的 PFX 证书，以及要配置的本地应用。 

### <a name="create-and-verify-a-custom-domain"></a>创建和验证自定义域

若要创建和验证自定义域：

1. 在 Azure Active Directory 中，在左侧导航栏中选择“自定义域名”，然后选择“添加自定义域” 。 
1. 输入自定义域名，然后选择“添加域”。 
1. 在域页面上，复制域的 TXT 记录信息。 
1. 转到域注册机构，根据你复制的 DNS 信息为你的域新建一条 TXT 记录。
1. 注册域后，在 Azure Active Directory 的域页面上选择“验证”。 在域状态变为“已验证”之后，可在所有 Azure AD 配置（包括应用程序代理）中使用此域。 

有关更详细的说明，请参阅[使用 Azure Active Directory 门户添加自定义域名](../fundamentals/add-custom-domain.md)。

### <a name="configure-an-app-to-use-a-custom-domain"></a>将应用配置为使用自定义域

若要使用自定义域通过应用程序代理发布应用程序：

1. 对于新应用，在 Azure Active Directory 的左侧导航栏中选择“企业应用程序”。 选择“新建应用程序”。 在“本地应用程序”部分中，选择“添加本地应用程序”。 
   
   对于“企业应用程序”中已有的应用，从列表中选中它，然后在左侧导航栏中选择“应用程序代理” 。 

2. 如果要添加自己的本地应用程序，则在应用程序代理设置页面上输入名称。

3.  在“内部 URL”字段中，为应用输入内部 URL。
   
4. 在“外部 URL”字段中，向下滚动查看列表，再选择你想要使用的自定义域。
   
5. 选择 **添加** 。
   
   ![选择自定义域](./media/application-proxy-configure-custom-domain/application-proxy.png)
   
6. 如果域已有证书，“证书”字段会显示证书信息。 否则，选择“证书”字段。 
   
   ![单击以上传证书](./media/application-proxy-configure-custom-domain/certificate.png)
   
7. 在“SSL 证书”页面上，浏览到 PFX 证书文件并将其选中。 输入证书的密码，然后选择“上传证书”。 有关证书的详细信息，请参阅[自定义域的证书](#certificates-for-custom-domains)部分。
   
   ![上传证书](./media/application-proxy-configure-custom-domain/ssl-certificate.png)
   
   > [!TIP] 
   > 自定义域只需上传一次证书。 之后，在你对其他应用使用自定义域时，将自动应用所上传的证书。
   
8. 如果已添加证书，则在“应用程序代理”页面上选择“保存” 。 
   
9. 在“应用程序代理”页面的信息栏中，记下你需要添加到 DNS 区域的 CNAME 条目。 
   
   ![添加 CNAME DNS 条目](./media/application-proxy-configure-custom-domain/dns-info.png)
   
10. 按照[使用 Azure 门户管理 DNS 记录和记录集](../../dns/dns-operations-recordsets-portal.md)中的说明，添加将新的外部 DNS 重定向到 msappproxy.net 域的 DNS 记录。
   
11. 要检查 DNS 记录是否配置正确，请使用 [nslookup](https://social.technet.microsoft.com/wiki/contents/articles/29184.nslookup-for-beginners.aspx) 命令来确认你的外部 URL 可供访问并且 msapproxy.net 域显示为别名。

你的应用程序现已设置为使用自定义域。 请确保先将用户分配到你的应用程序，然后再测试或发布它。 

要更改应用的域，请在应用的“应用程序代理”页面的“外部 URL”中，从下拉列表中选择不同的域 。 如果需要，请为更新后的域上传证书，并更新 DNS 记录。 如果未在“外部 URL”的下拉列表中看到所需的自定义域，则可能不对该域进行验证。

有关应用程序代理的更详细说明，请参阅[教程：在 Azure Active Directory 中添加一个本地应用程序以通过应用程序代理远程访问](application-proxy-add-on-premises-application.md)。

## <a name="certificates-for-custom-domains"></a>自定义域的证书

证书会为自定义域创建安全 TLS 连接。 

### <a name="certificate-formats"></a>证书格式

必须使用 PFX 证书，以确保包含所有必需的中间证书。 该证书必须包含私钥。

证书签名方法没有限制。 椭圆曲线加密 (ECC)、使用者可选名称 (SAN) 和其他常见证书类型都受支持。 

只要通配符与外部 URL 匹配，就可使用通配符证书。 必须对[通配符应用程序](application-proxy-wildcard.md)使用通配符证书。 如果还希望使用证书来访问子域，则必须在同一证书中将子域通配符添加为使用者可选名称。 例如，\*.adventure-works.com 的证书将不可用于 \*.apps.adventure-works.com，除非你将 \*.apps.adventure-works.com 添加为使用者可选名称  。 

如果客户端设备上安装了证书链，则可使用你自己的公钥基础结构 (PKI) 颁发的证书。 Intune 可将这些证书部署到托管设备。 对于非托管设备，必须手动安装这些证书。 

不建议使用专用根 CA，因为还需要将专用根 CA 推送到客户端计算机，这可能会带来许多挑战。

### <a name="certificate-management"></a>证书管理

所有证书管理都是通过各个应用程序页面进行的。 请转到应用程序“应用程序代理”页面，访问“证书”字段 。

为应用程序上载证书后，该证书也会自动应用到使用相同证书配置的**新**应用程序。 需要为租户中的现有应用重新上传证书。

证书过期时，你将收到警告，上面通知你上传其他证书。 如果撤销了证书，用户在访问该应用时可能会看到安全警告。 若要更新应用的证书，请导航到应用的“应用程序代理”页面，选择“证书”，然后上传新证书 。 如果旧证书未由其他应用使用，则会自动删除该证书。 

## <a name="next-steps"></a>后续步骤

* 使用 Azure AD 身份验证对已发布应用[启用单一登录](application-proxy-configure-single-sign-on-with-kcd.md)。
* 针对已发布的云应用的[条件访问](../conditional-access/concept-conditional-access-cloud-apps.md)。
