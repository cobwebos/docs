---
title: "Azure AD 应用程序代理中的自定义域 | Microsoft 文档"
description: "管理 Azure AD 应用程序代理中的自定义域，使得无论用户在哪里访问应用，应用的 URL 都相同。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 2fe9f895-f641-4362-8b27-7a5d08f8600f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: billmath
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: c15808a97d4efa5f9453f33d9ac70ebde5288201
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2017
---
# <a name="working-with-custom-domains-in-azure-ad-application-proxy"></a>使用 Azure AD 应用程序代理中的自定义域

通过 Azure Active Directory 应用程序代理发布应用程序时，会创建可让用户在远程工作时访问的外部 URL。 此 URL 的默认域为 yourtenant.msappproxy.net。 例如，如果发布了一个名为 Expenses 的应用且租户名为 Contoso，则外部 URL 为 https://expenses-contoso.msappproxy.net。 如果想要使用自己的域名，请为应用程序配置自定义域。 

我们建议尽可能地为应用程序设置自定义域。 自定义域的部分优点包括：

- 不管用户是在网络内部还是外部工作，都可以使用相同的 URL 访问应用程序。
- 如果所有应用程序具有相同的内部和外部 URL，则一个应用程序中指向另一个应用程序的链接可继续在企业网络外部工作。 
- 可以控制品牌并创建所需的 URL。 


## <a name="configure-a-custom-domain"></a>配置自定义域

### <a name="prerequisites"></a>先决条件

配置自定义域之前，请确保根据以下要求做好准备： 
- 一个[已添加到 Azure Active Directory 的经验证的域](active-directory-domains-add-azure-portal.md)。
- 一个用于域的、采用 PFX 文件格式的自定义证书。 
- 一个[通过应用程序代理发布](application-proxy-publish-azure-portal.md)的本地应用。

### <a name="configure-your-custom-domain"></a>配置自定义域

做好这三项准备后，请遵循以下步骤设置自定义域：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 导航到“Azure Active Directory” > “企业应用程序” > “所有应用程序”，选择要管理的应用。
3. 选择“应用程序代理”。 
4. 在“外部 URL”字段中，使用下拉列表选择自己的自定义域。 如果列表中未显示该域，则表示它尚未验证。 
5. 选择“保存”
5. 已禁用的“证书”字段将会启用。 选择此字段。 

   ![单击以上传证书](./media/active-directory-application-proxy-custom-domains/certificate.png)

   如果已经为此域上传了证书，则“证书”字段将显示证书信息。 

6. 上传 PFX 证书，并输入证书的密码。 
7. 选择“保存”以保存更改。 
8. 添加用于将新外部 URL 重定向到 msappproxy.net 域的 [DNS 记录](../dns/dns-operations-recordsets-portal.md)。 

>[!TIP] 
>只需为每个自定义域上传一个证书。 上传证书后，可以在发布新应用时选择自定义域，而无需执行除 DNS 记录以外的其他配置。 

## <a name="manage-certificates"></a>管理证书

### <a name="certificate-format"></a>证书格式
证书签名方法没有限制。 椭圆曲线加密 (ECC)、使用者可选名称 (SAN) 和其他常见证书类型全部都受到支持。 

只要通配符与所需的外部 URL 匹配，就可使用通配符证书。 

### <a name="changing-the-domain"></a>更改域
所有已验证的域显示在应用程序的“外部 URL”下拉列表中。 若要更改域，只需更新应用程序的该字段。 如果所需的域不在列表中，请[将它添加为已验证的域](active-directory-domains-add-azure-portal.md)。 如果选择一个尚无关联证书的域，请遵循步骤 5 - 7 添加证书。 然后，请务必更新从新的外部 URL 重定向的 DNS 记录。 

### <a name="certificate-management"></a>证书管理
可对多个应用程序使用相同的证书，但这些应用程序共享一台外部主机时除外。 

证书过期时，系统会发出警告，告知需要通过门户上传另一个证书。 如果撤销了证书，用户在访问应用程序时可能会看到安全警告。 我们不会对证书执行撤销检查。  若要更新给定应用程序的证书，请导航到该应用程序，并遵循步骤 5-7 在发布的应用程序上配置自定义域，以上传新证书。 如果旧证书未由其他应用程序使用，则会自动删除该证书。 

目前，所有证书管理都是通过单个应用程序页面进行的，因此需要在相关应用程序的上下文中管理证书。 

## <a name="next-steps"></a>后续步骤
* 使用 Azure AD 身份验证对已发布应用[启用单一登录](active-directory-application-proxy-sso-using-kcd.md)。
* 对已发布应用[启用条件访问](active-directory-application-proxy-conditional-access.md)。
* [将自定义域名添加到 Azure AD](active-directory-domains-add-azure-portal.md)


