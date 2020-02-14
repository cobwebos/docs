---
title: Azure AD 应用程序代理中的自定义域 | Microsoft 文档
description: 配置和管理 Azure AD 应用程序代理中的自定义域。
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
ms.openlocfilehash: 6f1656d730d55d4c5ab7fb963e49a8057ad88c9f
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77185542"
---
# <a name="configure-custom-domains-with-azure-ad-application-proxy"></a>Azure AD 应用程序代理配置自定义域

通过 Azure Active Directory 应用程序代理发布应用程序时，将为用户创建外部 URL。 此 URL 的默认域为 yourtenant.msappproxy.net。 例如，如果你在名为*Contoso*的租户中发布名为 "*支出*" 的应用，则外部 URL 为*https：\//expenses-contoso.msappproxy.net*。 如果要使用自己的域名而不是*msappproxy.net*，则可为应用程序配置自定义域。 

## <a name="benefits-of-custom-domains"></a>自定义域的优点

最好是为应用程序设置自定义域。 使用自定义域的一些原因包括：

- 应用之间的链接在企业网络外部工作。 如果你的应用程序具有指向应用程序代理外部目标的硬编码内部链接，而没有自定义域，则这些链接将中断。 当内部和外部 Url 相同时，请避免出现此问题。 如果无法使用自定义域，请参阅[重定向使用 Azure AD 应用程序代理发布的应用的硬编码链接](../application-proxy-link-translation.md)，了解解决此问题的其他方法。 
  
- 你的用户将有更简单的体验，因为他们可以从你的网络内部或外部使用相同的 URL 访问该应用程序。 它们不需要了解不同的内部和外部 Url，或跟踪其当前位置。 

- 您可以控制您的品牌，并创建所需的 Url。 自定义域有助于培养用户信心，因为用户会看到并使用熟悉的名称而不是*msappproxy.net*。

- 某些配置仅适用于自定义域。 例如，你需要使用安全断言标记语言（SAML）的应用的自定义域，例如当你使用 Active Directory 联合身份验证服务（AD FS）但无法使用 WS 联合身份验证时。 有关详细信息，请参阅[使用应用程序代理中的声明感知应用](application-proxy-configure-for-claims-aware-applications.md)。 

如果无法使内部和外部 Url 匹配，则使用自定义域并不重要，但仍可利用其他权益。 

## <a name="dns-configuration-options"></a>DNS 配置选项

根据你的要求，有几个选项可用于设置 DNS 配置：

### <a name="same-internal-and-external-url-different-internal-and-external-behavior"></a>相同的内部和外部 URL，不同的内部和外部行为 

如果你不希望通过应用程序代理定向内部用户，则可以设置一个*分裂的 DNS*。 拆分 DNS 基础结构将内部域名服务器和外部主机定向到外部域名服务器，以进行名称解析。 

![拆分式 DNS](./media/application-proxy-configure-custom-domain/split-brain-dns.png)

### <a name="different-internal-and-external-urls"></a>不同的内部和外部 Url 

如果内部和外部 Url 不同，则不需要配置裂脑行为，因为用户路由由 URL 确定。 在这种情况下，只更改外部 DNS，并将外部 URL 路由到应用程序代理终结点。 

为外部 URL 选择自定义域时，信息栏会显示需要添加到外部 DNS 提供程序的 CNAME 条目。 你始终可以通过转到应用的**应用程序代理**页来查看此信息。

## <a name="set-up-and-use-custom-domains"></a>设置并使用自定义域

若要将本地应用配置为使用自定义域，则需要已验证的 Azure Active Directory 自定义域、自定义域的 PFX 证书和要配置的本地应用。 

### <a name="create-and-verify-a-custom-domain"></a>创建和验证自定义域

若要创建和验证自定义域：

1. 在 Azure Active Directory 中，在左侧导航栏中选择 "**自定义域名**"，然后选择 "**添加自**定义域"。 
1. 输入自定义域名，然后选择 "**添加域**"。 
1. 在 "域" 页上，复制域的 TXT 记录信息。 
1. 根据你复制的 DNS 信息，前往你的域注册机构并为你的域创建一个新的 TXT 记录。
1. 注册域后，在 Azure Active Directory 中域的页面上，选择 "**验证**"。 **验证**域状态后，你可以在所有 Azure AD 配置（包括应用程序代理）中使用域。 

有关更多详细说明，请参阅[使用 Azure Active Directory 门户添加自定义域名](../fundamentals/add-custom-domain.md)。

### <a name="configure-an-app-to-use-a-custom-domain"></a>将应用配置为使用自定义域

使用自定义域通过应用程序代理发布应用程序：

1. 对于新应用，在 Azure Active Directory 中，在左侧导航栏中选择 "**企业应用程序**"。 选择“新建应用程序”。 在“本地应用程序”部分中，选择“添加本地应用程序”。 
   
   对于已在**企业应用程序**中的应用，请从列表中选择它，然后在左侧导航栏中选择 "**应用程序代理**"。 

2. 如果要添加自己的本地应用程序，请在 "应用程序代理设置" 页上输入**名称**。

3.  在 "**内部 url** " 字段中，输入应用的内部 url。
   
4. 在 "**外部 Url** " 字段中，下拉列表并选择要使用的自定义域。
   
5. 选择“添加”。
   
   ![选择自定义域](./media/application-proxy-configure-custom-domain/application-proxy.png)
   
6. 如果域已有证书，"**证书**" 字段将显示证书信息。 否则，请选择 "**证书**" 字段。 
   
   ![单击以上传证书](./media/application-proxy-configure-custom-domain/certificate.png)
   
7. 在 " **SSL 证书**" 页上，浏览到 PFX 证书文件并将其选中。 输入证书的密码，然后选择 "**上传证书**"。 有关证书的详细信息，请参阅 "[自定义域的证书](#certificates-for-custom-domains)" 部分。
   
   ![上载证书](./media/application-proxy-configure-custom-domain/ssl-certificate.png)
   
   > [!TIP] 
   > 自定义域只需上载一次证书。 之后，在将自定义域用于其他应用时，将自动应用上传的证书。
   
8. 如果添加了证书，请在 "**应用程序代理**" 页上选择 "**保存**"。 
   
9. 在 "**应用程序代理**" 页上的信息栏中，记下需要添加到 DNS 区域的 CNAME 条目。 
   
   ![添加 CNAME DNS 条目](./media/application-proxy-configure-custom-domain/dns-info.png)
   
10. 按照[使用 Azure 门户管理 dns 记录和记录集](../../dns/dns-operations-recordsets-portal.md)中的说明，添加将新的外部 URL 重定向到*MSAPPPROXY.NET*域的 dns 记录。
   
11. 若要检查是否正确配置了 DNS 记录，请使用[nslookup](https://social.technet.microsoft.com/wiki/contents/articles/29184.nslookup-for-beginners.aspx)命令确认外部 URL 是可访问的，并且*msapproxy.net*域显示为别名。

现在，应用程序已设置为使用自定义域。 请确保先将用户分配到应用程序，然后再进行测试或发布。 

若要更改应用的域，请从应用的**应用程序代理**页上的 "**外部 URL** " 下拉列表中选择一个不同的域。 如果需要，请为更新的域上载证书，并更新 DNS 记录。 如果在 "**外部 URL**" 的下拉列表中看不到所需的自定义域，则可能无法对其进行验证。

有关应用程序代理的详细说明，请参阅[教程：在 Azure Active Directory 中通过应用程序代理添加用于远程访问的本地应用程序](application-proxy-add-on-premises-application.md)。

## <a name="certificates-for-custom-domains"></a>自定义域的证书

证书为自定义域创建安全 SSL 连接。 

### <a name="certificate-formats"></a>证书格式

必须使用 PFX 证书，以确保包含所有必需的中间证书。 证书必须包含私钥。

证书签名方法没有限制。 支持椭圆曲线加密（ECC）、使用者备用名称（SAN）和其他常见证书类型。 

只要通配符与外部 URL 匹配，就可以使用通配符证书。 必须为[通配符应用程序](application-proxy-wildcard.md)使用通配符证书。 如果要使用证书还访问子域，则必须在同一证书中将子域通配符作为使用者备用名称添加。 例如，除非你 *\*添加 apps.adventure-works.com*作为使用者备用名称，否则 *\** 的证书将不能 *\*用于 apps.adventure-works.com。* 

如果证书链安装在客户端设备上，则可以使用自己的公钥基础结构（PKI）颁发的证书。 Intune 可以将这些证书部署到托管设备。 对于非托管设备，必须手动安装这些证书。

最好不要使用专用根 CA。 还需要将专用根 CA 推送到客户端计算机，这会带来许多挑战。 

### <a name="certificate-management"></a>证书管理

所有证书管理都是通过各个应用程序页面进行的。 请访问应用程序的**应用程序代理**页，访问 "**证书**" 字段。

你可以为多个应用程序使用相同的证书。 如果上传的证书与其他应用程序一起使用，则将自动应用该证书。 添加或配置应用时，系统不会提示您重新上传。 

当证书过期时，会收到一条警告，告诉您上载其他证书。 如果证书被吊销，用户在访问应用程序时可能会看到安全警告。 若要更新应用的证书，请导航到应用的 "**应用程序代理**" 页，选择 "**证书**"，并上传新证书。 如果旧证书未被其他应用使用，则会自动删除该证书。 

## <a name="next-steps"></a>后续步骤
* 使用 Azure AD 身份验证对已发布应用[启用单一登录](application-proxy-configure-single-sign-on-with-kcd.md)。
* [启用](../conditional-access/overview.md)已发布应用的条件性访问。

