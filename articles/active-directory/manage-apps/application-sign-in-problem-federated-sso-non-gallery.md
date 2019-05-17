---
title: 登录到配置为联合单一登录的非库应用程序时出现的问题 | Microsoft Docs
description: 关于使用 Azure AD 登录到配置为基于 SAML 的联合单一登录的应用程序时可能遇到的具体问题的指南
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 38decf98707231c21427f7a22dd4d12adb41852b
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2019
ms.locfileid: "65825442"
---
# <a name="problems-signing-in-to-a-non-gallery-application-configured-for-federated-single-sign-on"></a>登录到配置为联合单一登录的非库应用程序时出现的问题

若要解决下面的登录问题，我们建议你遵循这些建议以获得更好地诊断和自动执行的解决方法步骤：

- 安装[我的应用程序保护浏览器扩展](access-panel-extension-problem-installing.md)帮助 Azure Active Directory (Azure AD) 提供更好地诊断和解决方法时使用的测试体验在 Azure 门户中。
- 重新生成错误使用 Azure 门户中的应用程序配置页中的测试体验。 详细了解[基于调试 SAML 的单一登录应用程序](../develop/howto-v1-debug-saml-sso-issues.md)

## <a name="application-not-found-in-directory"></a>在目录中未找到应用程序

*错误 AADSTS70001:标识符的应用程序`https://contoso.com`目录中未找到*。

**可能的原因**

SAML 请求中从应用程序发送给 Azure AD 的颁发者属性与应用程序 Azure AD 中配置的标识符值不匹配。

**解决方法**

确保`Issuer`SAML 请求中的属性与 Azure AD 中配置的标识符值匹配。 如果您使用[测试体验](../develop/howto-v1-debug-saml-sso-issues.md)在 Azure 门户中使用我的应用保护浏览器扩展，您无需手动执行以下步骤。

1. 打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”或“共同管理员”身份登录。

2. 在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3. 在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4. 在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5. 单击“所有应用程序”，查看所有应用程序的列表。

   * 如果未看到想在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设为“所有应用程序”。

6. 选择要配置单一登录的应用程序。

7. 在应用程序加载后，在应用程序的左侧导航菜单中单击“单一登录”。

8. 当应用程序加载以后，请打开“基本 SAML 配置”。 验证标识符文本框中的值匹配错误中显示的标识符值的值。

## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>回复地址与为应用程序配置的回复地址不匹配。 

*错误 AADSTS50011:回复地址`https://contoso.com`与应用程序配置的回复地址不匹配* 

**可能的原因** 

SAML 请求中的 AssertionConsumerServiceURL 值与 Azure AD 中配置的“回复 URL”值或模式不匹配。 SAML 请求中的 AssertionConsumerServiceURL 值为错误中显示的 URL。 

**解决方法** 

确保`Issuer`SAML 请求中的属性与 Azure AD 中配置的标识符值匹配。 如果您使用[测试体验](../develop/howto-v1-debug-saml-sso-issues.md)在 Azure 门户中使用我的应用保护浏览器扩展，您无需手动执行以下步骤。
 
1. 打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”或“共同管理员”身份登录。 

2. 在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。 

3. 在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。 

4. 在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。 

5. 单击“所有应用程序”，查看所有应用程序的列表。 

   * 如果未看到想在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设为“所有应用程序”。
  
6. 选择要配置单一登录的应用程序

7. 在应用程序加载后，在应用程序的左侧导航菜单中单击“单一登录”。

8. 当应用程序加载以后，请打开“基本 SAML 配置”。 验证或更新以匹配的回复 URL 文本框中的值`AssertionConsumerServiceURL`SAML 请求中的值。    
    
你已在 Azure AD 中更新的回复 URL 值和匹配 SAML 请求中的应用程序发送的值后，您应能够登录到应用程序。

## <a name="user-not-assigned-a-role"></a>未为用户分配角色

*错误 AADSTS50105:已登录用户`brian\@contoso.com`未分配给应用程序角色*

**可能的原因**

未在 Azure AD 中授予用户访问应用程序的权限。

**解决方法**

若要直接将一个或多个用户分配到应用程序，请执行以下步骤。 如果您使用[测试体验](../develop/howto-v1-debug-saml-sso-issues.md)在 Azure 门户中使用我的应用保护浏览器扩展，您无需手动执行以下步骤。

1. 打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。

2. 在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3. 在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4. 在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5. 单击“所有应用程序”，查看所有应用程序的列表。

   * 如果未看到要在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设置为“所有应用程序”。

6. 从列表中选择要向其分配用户的应用程序。

7. 在应用程序加载后，在应用程序的左侧导航菜单中单击“用户和组”。

8. 单击“用户和组”列表顶部的“添加”按钮，以打开“添加分配”窗格。

9. 在“添加分配”窗格中，单击“用户和组”选择器。

10. 在“按名称或电子邮件地址搜索”搜索框中，键入要分配的用户的**全名**或**电子邮件地址**。

11. 将鼠标悬停在列表中的“用户”上方以显示“复选框”。 单击用户个人资料头像或徽标旁边的复选框，将用户添加到“已选择”列表。

12. **可选：** 如果要“添加多个用户”，请在“按名称或电子邮件地址搜索”搜索框中键入其他“全名”或“电子邮件地址”，然后单击复选框以将此用户添加到“已选择”列表。

13. 在完成用户的选择后，单击“选择”按钮将他们添加到要分配给应用程序的用户和组列表。

14. **可选：** 单击“添加分配”窗格中的“选择角色”选择器，选择一个角色来分配给所选用户。

15. 单击“分配”按钮，将应用程序分配给选定用户。

在一段很短的时间后，所选用户能够使用解决方案描述部分中所述的方法启动这些应用程序。

## <a name="not-a-valid-saml-request"></a>不是有效的 SAML 请求

*错误 AADSTS75005:请求不是有效的 Saml2 协议消息。*

**可能的原因**

Azure AD 不支持应用程序针对单一登录所发送的 SAML 请求。 常见问题如下：

-   SAML 请求中缺少必填字段

-   SAML 请求编码方法

**解决方法**

1.  捕获 SAML 请求。 遵循有关[如何在 Azure AD 中调试对应用程序进行基于 SAML 的单一登录](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)教程，了解如何捕获 SAML 请求。

2.  联系应用程序供应商并共享以下信息：

    -   SAML 请求

    -   [Azure AD 单一登录 SAML 协议要求](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

应用程序供应商应验证它们的单一登录支持的 Azure AD SAML 实现。

## <a name="misconfigured-application"></a>配置错误的应用程序

*错误 AADSTS650056:配置错误应用程序。这可能由以下原因之一造成：客户端不具有列出为 AAD Graph 中在客户端的应用程序注册中请求的权限的任何权限。或者，管理员尚未同意租户中。或者，检查以确保它匹配配置的客户端应用程序标识符在请求中的应用程序标识符。请联系管理员以修复配置或代表租户同意。*

**可能的原因**

`Issuer`属性发送的 SAML 请求中向 Azure AD 应用程序与 Azure AD 中应用程序配置的标识符值不匹配。

**解决方法**

确保`Issuer`SAML 请求中的属性与 Azure AD 中配置的标识符值匹配。 如果您使用[测试体验](../develop/howto-v1-debug-saml-sso-issues.md)在 Azure 门户中使用我的应用保护浏览器扩展，您无需手动执行以下步骤：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”或“共同管理员”身份登录。

1.  打开**Azure Active Directory 扩展**通过选择**的所有服务**左侧主导航菜单顶部。

1.  类型 **"Azure Active Directory"** 在筛选器搜索框中，选择**Azure Active Directory**项。

1.  选择**企业应用程序**在 Azure Active Directory 的左侧导航菜单。

1.  选择“所有应用程序”，查看所有应用程序的列表。

    如果未看到要在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设置为“所有应用程序”。

1.  选择要配置为单一登录的应用程序。

1.  当应用程序加载以后，请打开“基本 SAML 配置”。 验证标识符文本框中的值匹配错误中显示的标识符值的值。

## <a name="certificate-or-key-not-configured"></a>证书或密钥未配置

错误 AADSTS50003: 没有配置签名密钥。

**可能的原因**

应用程序对象已损坏，Azure AD 无法识别为应用程序配置的证书。

**解决方法**

若要删除并创建新证书，请按照以下步骤操作：

1. 打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”或“共同管理员”身份登录。

2. 在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3. 在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4. 在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5. 单击“所有应用程序”，查看所有应用程序的列表。

   * 如果未看到想在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设为“所有应用程序”。

6. 选择要配置单一登录的应用程序。

7. 在应用程序加载后，在应用程序的左侧导航菜单中单击“单一登录”。

8. 在“SAML 签名证书”部分下，单击“创建新证书”。

9. 选择到期日期。 然后，单击“保存”。

10. 选中“激活新证书”以覆盖活动证书。 然后，单击窗格顶部的“保存”并选择接受以激活滚动更新证书。

11. 在“SAML 签名证书”部分下，单击“删除”删除“未使用”的证书。

## <a name="saml-request-not-present-in-the-request"></a>请求中不存在 SAML 请求

*错误 AADSTS750054:必须在针对 SAML 重定向绑定的 HTTP 请求中将SAMLRequest 或 SAMLResponse 提供为查询字符串参数。*

**可能的原因**

Azure AD 无法识别 HTTP 请求中的 URL 参数中的 SAML 请求。 如果应用程序未使用 HTTP 重定向绑定将 SAML 请求发送到 Azure AD 时，可以发生这种情况。

**解决方法**

应用程序需要 SAML 请求编码为使用 HTTP 的 location 标头发送重定向绑定。 有关如何实现它的详细信息，请阅读 [SAML 协议规范文档](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf)中的“HTTP 重定向绑定”部分。

## <a name="azure-ad-is-sending-the-token-to-an-incorrect-endpoint"></a>Azure AD 将令牌发送到不正确的终结点

**可能的原因**

单一登录，期间如果登录请求不包含显式的回复 URL (断言使用者服务 URL)，然后 Azure AD 将选择已配置的任何依赖于 Url 为该应用程序。 即使应用程序具有配置显式回复 URL，用户可能会重定向 https://127.0.0.1:444。 

当应用程序被添加为非库应用时，Azure Active Directory 将此回复 URL 创建为默认值。 此行为已更改，Azure Active Directory 默认情况下不再添加此 URL。 

**解决方法**

删除应用程序配置的未使用的答复 Url。

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”或“共同管理员”身份登录。

2.  打开**Azure Active Directory 扩展**通过选择**的所有服务**左侧主导航菜单顶部。

3.  类型 **"Azure Active Directory"** 在筛选器搜索框中，选择**Azure Active Directory**项。

4.  选择**企业应用程序**在 Azure Active Directory 的左侧导航菜单。

5.  选择“所有应用程序”，查看所有应用程序的列表。

    如果未看到要在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设置为“所有应用程序”。

6.  选择要配置为单一登录的应用程序。

7.  当应用程序加载以后，请打开“基本 SAML 配置”。 在中**回复 URL (断言使用者服务 URL)**，由系统创建删除未使用或默认答复 Url。 例如，`https://127.0.0.1:444/applications/default.aspx`。



## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>自定义发送到应用程序的 SAML 声明时出现问题

若要了解如何自定义发送到应用程序的 SAML 属性声明，请参阅 [Azure Active Directory 中的声明映射](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)获取详细信息。

## <a name="next-steps"></a>后续步骤
[Azure AD 单一登录 SAML 协议要求](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)
