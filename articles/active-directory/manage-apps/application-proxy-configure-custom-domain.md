---
title: Azure AD 应用程序代理中的自定义域 | Microsoft 文档
description: 在 Azure AD 应用程序代理中配置和管理自定义域。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f35658a75adb4d4c6c279e45087e741b8117e65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481375"
---
# <a name="configure-custom-domains-with-azure-ad-application-proxy"></a>使用 Azure AD 应用程序代理配置自定义域

通过 Azure 活动目录应用程序代理发布应用程序时，会为用户创建外部 URL。 此 URL 的默认域为 yourtenant.msappproxy.net**。 例如，如果在*租户中发布*名为 *"支出"* 的应用，则外部 URL 为*https：/expenses-contoso.msappproxy.net\/*。 如果要使用自己的域名而不是*msappproxy.net*，则可以为应用程序配置自定义域。 

## <a name="benefits-of-custom-domains"></a>自定义域的优势

最好尽可能为应用设置自定义域。 使用自定义域的一些原因包括：

- 即使在企业网络之外，应用之间的链接也能正常工作。 如果没有自定义域，如果应用具有指向应用程序代理外部目标的硬编码内部链接，并且这些链接无法外部解析，则这些链接将中断。 当内部和外部 URL 相同时，可以避免此问题。 如果无法使用自定义域，请参阅[重定向使用 Azure AD 应用程序代理发布的应用的硬编码链接](../application-proxy-link-translation.md)，以了解解决此问题的其他方法。 
  
- 您的用户将具有更简单的体验，因为他们可以从网络内部或外部使用相同的 URL 访问应用。 他们不需要学习不同的内部和外部 URL，或跟踪其当前位置。 

- 您可以控制品牌并创建所需的 URL。 自定义域可以帮助建立用户的信心，因为用户看到并使用熟悉的名称，而不是*msappproxy.net*。

- 某些配置仅适用于自定义域。 例如，对于使用安全断言标记语言 （SAML） 的应用，例如使用活动目录联合服务 （AD FS） 但无法使用 WS-联合身份验证时），需要自定义域。 有关详细信息，请参阅[在应用程序代理 中使用声明感知应用](application-proxy-configure-for-claims-aware-applications.md)。 

如果无法使内部和外部 URL 匹配，则使用自定义域并不重要，但您仍可以利用其他优势。 

## <a name="dns-configuration-options"></a>DNS 配置选项

根据您的要求，有几个选项可用于设置 DNS 配置：

### <a name="same-internal-and-external-url-different-internal-and-external-behavior"></a>相同的内部和外部 URL，不同的内部和外部行为 

如果您不希望内部用户通过应用程序代理定向，则可以设置*一个分脑 DNS*。 拆分 DNS 基础结构将内部主机定向到内部域名服务器，将外部主机定向到外部域名服务器，以便进行名称解析。 

![拆分式 DNS](./media/application-proxy-configure-custom-domain/split-brain-dns.png)

### <a name="different-internal-and-external-urls"></a>不同的内部和外部 URL 

如果内部和外部 URL 不同，则无需配置拆分大脑行为，因为用户路由由 URL 决定。 在这种情况下，您只更改外部 DNS，并将外部 URL 路由到应用程序代理终结点。 

为外部 URL 选择自定义域时，信息栏将显示需要添加到外部 DNS 提供程序的 CNAME 条目。 您始终可以通过访问应用**的应用程序代理**页来查看此信息。

## <a name="set-up-and-use-custom-domains"></a>设置和使用自定义域

要将本地应用配置为使用自定义域，需要经过验证的 Azure 活动目录自定义域、自定义域的 PFX 证书和要配置的本地应用。 

### <a name="create-and-verify-a-custom-domain"></a>创建和验证自定义域

要创建和验证自定义域，请进行以下操作：

1. 在 Azure 活动目录中，在左侧导航中选择**自定义域名**，然后选择"**添加自定义域**"。 
1. 输入自定义域名并选择 **"添加域**"。 
1. 在域页上，复制域的 TXT 记录信息。 
1. 转到域注册商，并根据复制的 DNS 信息为您的域创建新的 TXT 记录。
1. 注册域后，在 Azure 活动目录中的域页面上选择 **"验证**"。 **域状态已验证**后，可以跨所有 Azure AD 配置（包括应用程序代理）使用域。 

有关更详细的说明，请参阅使用[Azure 活动目录门户添加自定义域名](../fundamentals/add-custom-domain.md)。

### <a name="configure-an-app-to-use-a-custom-domain"></a>将应用配置为使用自定义域

要使用自定义域通过应用程序代理发布应用，请执行以下操作：

1. 对于新应用，在 Azure 活动目录中，选择左侧导航中的**企业应用程序**。 选择 **"新应用程序**"。 在“本地应用程序”**** 部分中，选择“添加本地应用程序”****。 
   
   对于已在**企业应用程序中**的应用，请从列表中选择它，然后在左侧导航中选择**应用程序代理**。 

2. 在"应用程序代理设置"页上，如果要添加自己的本地应用程序，请输入**名称**。

3.  在 **"内部 Url"** 字段中，输入应用的内部 URL。
   
4. 在 **"外部 Url"** 字段中，下拉列表并选择要使用的自定义域。
   
5. 选择“添加”****。
   
   ![选择自定义域](./media/application-proxy-configure-custom-domain/application-proxy.png)
   
6. 如果域已具有证书，**则"证书**"字段将显示证书信息。 否则，选择 **"证书"** 字段。 
   
   ![单击以上传证书](./media/application-proxy-configure-custom-domain/certificate.png)
   
7. 在**SSL 证书**页上，浏览到并选择您的 PFX 证书文件。 输入证书的密码，然后选择 **"上载证书**"。 有关证书的详细信息，请参阅[自定义域的证书](#certificates-for-custom-domains)部分。
   
   ![上传证书](./media/application-proxy-configure-custom-domain/ssl-certificate.png)
   
   > [!TIP] 
   > 自定义域只需要上传一次证书。 之后，当您对其他应用使用自定义域时，将自动应用上载的证书。
   
8. 如果添加了证书，请在**应用程序代理**页上选择 **"保存**"。 
   
9. 在**应用程序代理**页上的信息栏中，请注意需要添加到 DNS 区域的 CNAME 条目。 
   
   ![添加 CNAME DNS 条目](./media/application-proxy-configure-custom-domain/dns-info.png)
   
10. 使用 Azure 门户添加 DNS 记录，将新的外部 URL 重定向到*msappproxy.net*域，请按照[管理 DNS 记录和记录集](../../dns/dns-operations-recordsets-portal.md)的说明进行操作。
   
11. 要检查 DNS 记录配置是否正确，请使用[nslookup](https://social.technet.microsoft.com/wiki/contents/articles/29184.nslookup-for-beginners.aspx)命令确认外部 URL 是可访问的，*并且msapproxy.net*域显示为别名。

应用程序现在设置为使用自定义域。 在测试或释放应用程序之前，请确保将用户分配给应用程序。 

要更改应用的域，请从应用**应用程序代理**页上**的"外部 URL"** 中的下拉列表中选择其他域。 如有必要，上传更新的域的证书，并更新 DNS 记录。 如果在 **"外部 URL"** 的下拉列表中看不到所需的自定义域，则可能无法验证该域。

有关应用程序代理的更多详细信息，请参阅[教程：在 Azure 活动目录中通过应用程序代理添加用于远程访问的本地应用程序](application-proxy-add-on-premises-application.md)。

## <a name="certificates-for-custom-domains"></a>自定义域的证书

证书为您的自定义域创建安全 TLS 连接。 

### <a name="certificate-formats"></a>证书格式

您必须使用 PFX 证书，以确保包含所有必需的中间证书。 证书必须包含私钥。

证书签名方法没有限制。 支持椭圆曲线加密 （ECC）、主题替代名称 （SAN） 和其他常见证书类型。 

只要通配符与外部 URL 匹配，就可以使用通配符证书。 您必须对[通配符应用程序使用通配符](application-proxy-wildcard.md)证书。 如果要使用证书同时访问子域，则必须在同一证书中添加子域通配符作为主题替代名称。 例如，除非将*\*.apps.adventure-works.com*添加为主题替代名称，否则*\*.adventure-works.com*的证书将*\*apps.adventure-works.com*不起作用。 

如果证书链安装在客户端设备上，则可以使用自己的公钥基础结构 （PKI） 颁发的证书。 Intune 可以将这些证书部署到托管设备。 对于非托管设备，必须手动安装这些证书。 

我们不建议使用私有根 CA，因为私有根 CA 也需要推送到客户端计算机，这会带来许多挑战。

### <a name="certificate-management"></a>证书管理

所有证书管理都通过各个应用程序页。 转到应用程序**的应用程序代理**页以访问**证书**字段。

可以为多个应用程序使用相同的证书。 如果上载的证书适用于其他应用程序，则将自动应用它。 添加或配置应用时，不会提示您再次上载它。 

当证书过期时，您会收到一条警告，告诉您要上载其他证书。 如果证书被吊销，则用户在访问应用时可能会看到安全警告。 要更新应用的证书，请导航到应用的应用程序**代理**页，选择 **"证书**"并上载新证书。 如果旧证书未被其他应用使用，则会自动删除它。 

## <a name="next-steps"></a>后续步骤
* 使用 Azure AD 身份验证对已发布应用[启用单一登录](application-proxy-configure-single-sign-on-with-kcd.md)。
* [启用对](../conditional-access/overview.md)已发布的应用的条件访问。

