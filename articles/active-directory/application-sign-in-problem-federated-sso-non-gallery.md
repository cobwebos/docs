---
title: "登录到配置为联合单一登录的非库应用程序时出现的问题 | Microsoft Docs"
description: "关于使用 Azure AD 登录到配置为基于 SAML 的联合单一登录的应用程序时可能遇到的具体问题的指南"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 3afc7bca878caef424d3fa3c64aa17df0fda7de5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="problems-signing-in-to-a-non-gallery-application-configured-for-federated-single-sign-on"></a>登录到配置为联合单一登录的非库应用程序时出现的问题

若要解决问题，需要按照以下内容验证 Azure AD 中的应用程序配置：

-   已遵循 Azure AD 库应用程序的所有配置步骤。

-   在 AAD 中配置的标识符和回复 URL 与应用程序中的所需值匹配

-   已将用户分配给应用程序

## <a name="application-not-found-in-directory"></a>在目录中未找到应用程序

*错误 AADSTS70001: 在目录中未找到标识符为“https://contoso.com”的应用程序*。

**可能的原因**

SAML 请求中从应用程序发送给 Azure AD 的颁发者属性与应用程序 Azure AD 中配置的标识符值不匹配。

**解决方法**

确保 SAML 请求中的颁发者属性与在 Azure AD 中配置的标识符值匹配：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”或“共同管理员”身份登录。

2.  在左侧主导航菜单底部单击“更多服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  单击“所有应用程序”，查看所有应用程序的列表。

   * 如果未看到想在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设为“所有应用程序”。

6.  选择要配置单一登录的应用程序。

7.  在应用程序加载后，在应用程序的左侧导航菜单中单击“单一登录”。

8.  <span id="_Hlk477190042" class="anchor"></span>转到“域和 URL”部分。 验证标识符文本框中的值匹配错误中显示的标识符值的值。

在 Azure AD 中更新了标识符值并且该值与应用程序在 SAML 请求中发送的值匹配之后，应该能够登录到应用程序。

## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>回复地址与为应用程序配置的回复地址不匹配。 

*错误 AADSTS50011: 回复地址 "https://contoso.com" 与为应用程序配置的回复地址不匹配* 

**可能的原因** 

SAML 请求中的 AssertionConsumerServiceURL 值与 Azure AD 中配置的“回复 URL”值或模式不匹配。 SAML 请求中的 AssertionConsumerServiceURL 值为错误中显示的 URL。 

**解决方法** 

确保 SAML 请求中的 AssertionConsumerServiceURL 值与 Azure AD 中配置的“回复 URL”值匹配。 
 
1.  打开[“Azure 门户”](https://portal.azure.com/)，以“全局管理员”或“共同管理员”身份登录。 

2.  在左侧主导航菜单底部单击“更多服务”，打开“Azure Active Directory 扩展”。 

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。 

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。 

5.  单击“所有应用程序”，查看所有应用程序的列表。 

  * 如果未看到想在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设为“所有应用程序”。
  
6.  选择要配置单一登录的应用程序

7.  在应用程序加载后，在应用程序的左侧导航菜单中单击“单一登录”。

8.  转到“域和 URL”部分。 验证或更新“回复 URL”文本框中的值，以匹配 SAML 请求中的 AssertionConsumerServiceURL 值。

  * 如果不显示“回复 URL”文本框，请选中“显示高级 URL 设置”复选框。 

在 Azure AD 中更新了“回复 URL”值并且该值与应用程序在 SAML 请求中发送的值匹配之后，应该能够登录到应用程序。

## <a name="user-not-assigned-a-role"></a>未为用户分配角色

*错误 AADSTS50105: 没有为已登录的用户“brian@contoso.com”分配应用程序角色*

**可能的原因**

未在 Azure AD 中授予用户访问应用程序的权限。

**解决方法**

要直接将一个或多个用户分配到应用程序，请按照以下步骤操作：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。

2.  在左侧主导航菜单底部单击“更多服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  单击“所有应用程序”，查看所有应用程序的列表。

  * 如果未看到想在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设为“所有应用程序”。

6.  从列表中选择要向其分配用户的应用程序。

7.  在应用程序加载后，在应用程序的左侧导航菜单中单击“用户和组”。

8.  单击“用户和组”列表顶部的“添加”按钮，打开“添加分配”边栏选项卡。

9.  在“添加分配”边栏选项卡中，单击“用户和组”选择器。

10. 在“按名称或电子邮件地址搜索”搜索框中，键入要分配的用户的**全名**或**电子邮件地址**。

11. 将鼠标悬停在列表中的“用户”上方以显示“复选框”。 单击用户个人资料头像或徽标旁边的复选框，将用户添加到“已选择”列表。

12. **可选：**如果想要**添加多个用户**，请在“按名称或电子邮件地址搜索”搜索框中，键入其他**全名**或**电子邮件地址**，然后单击复选框以将此用户添加到“已选择”列表。

13. 在完成用户的选择后，单击“选择”按钮将他们添加到要分配给应用程序的用户和组列表。

14. **可选：**单击“添加分配”边栏选项卡中的“选择角色”选择器可选择要分配给所选用户的角色。

15. 单击“分配”按钮，将应用程序分配给选定用户。

稍等片刻，所选用户就能使用解决方案描述部分所述的方法来启动这些应用程序了。

## <a name="not-a-valid-saml-request"></a>不是有效的 SAML 请求

*错误 AADSTS75005: 请求不是有效的 Saml2 协议消息。*

**可能的原因**

Azure AD 不支持应用程序针对单一登录所发送的 SAML 请求。 常见问题如下：

-   SAML 请求中缺少必填字段

-   SAML 请求编码方法

**解决方法**

1.  捕获 SAML 请求。 遵循有关[如何在 Azure AD 中调试对应用程序进行基于 SAML 的单一登录](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)教程，了解如何捕获 SAML 请求。

2.  联系应用程序供应商并共享以下信息：

    -   SAML 请求

    -   [Azure AD 单一登录 SAML 协议要求](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

它们应验证是否支持单一登录的 Azure AD SAML 实现。

## <a name="no-resource-in-requiredresourceaccess-list"></a>requiredResourceAccess 列表中没有资源

*错误 AADSTS65005: 客户端应用程序已请求访问资源 "00000002-0000-0000-c000-000000000000"。该请求失败，因为客户端未在其 requiredResourceAccess 列表中指定此资源*。

**可能的原因**

应用程序对象已损坏。

**解决方法**

若要解决此问题，请从目录中删除应用程序。 然后，添加并重新配置应用程序，按照以下步骤操作：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”或“共同管理员”身份登录。

2.  在左侧主导航菜单底部单击“更多服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  单击“所有应用程序”，查看所有应用程序的列表。

  * 如果未看到想在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设为“所有应用程序”。

6.  选择要配置单一登录的应用程序。

7.  单击应用程序“概述”边栏选项卡左上角的“删除”。

8.  刷新 Azure AD，并从 Azure AD 库添加应用程序。 然后，重新配置应用程序。

重新配置应用程序后，应该能登录到该应用程序。

## <a name="certificate-or-key-not-configured"></a>证书或密钥未配置

错误 AADSTS50003：没有配置签名密钥。

**可能的原因**

应用程序对象已损坏，Azure AD 无法识别为应用程序配置的证书。

**解决方法**

若要删除并创建新证书，请按照以下步骤操作：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”或“共同管理员”身份登录。

2.  在左侧主导航菜单底部单击“更多服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  单击“所有应用程序”，查看所有应用程序的列表。

  * 如果未看到想在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设为“所有应用程序”。

6.  选择要配置单一登录的应用程序。

7.  在应用程序加载后，在应用程序的左侧导航菜单中单击“单一登录”。

8.  在“SAML 签名证书”部分下，单击“创建新证书”。

9.  选择到期日期。 然后，单击“保存”。

10. 选中“激活新证书”以覆盖活动证书。 然后，单击边栏选项卡顶部的“保存”并选择接受以激活滚动更新证书。

11. 在“SAML 签名证书”部分下，单击“删除”删除“未使用”的证书。

## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>自定义发送到应用程序的 SAML 声明时出现问题

若要了解如何自定义发送到应用程序的 SAML 属性声明，请参阅 [Azure Active Directory 中的声明映射](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)获取详细信息。

## <a name="next-steps"></a>后续步骤
[Azure AD 单一登录 SAML 协议要求](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)
