---
title: 如何配置非库应用程序的联合单一登录 | Microsoft Docs
description: 如何为想要与 Azure AD 集成的自定义非库应用程序配置联合单一登录
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 65df07b6fca62476953ad98dc9e17d9a82c97a53
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2018
---
# <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>如何配置非库应用程序的联合单一登录

若要为非库应用程序配置单一登录*而无需编写代码*，需要拥有订阅或 Azure AD Premium，并且该应用程序必须支持 SAML 2.0。 有关 Azure AD 版本的详细信息，请访问 [Azure AD 定价](https://azure.microsoft.com/pricing/details/active-directory/)。

## <a name="overview-of-steps-required"></a>所需步骤概述
下面从较高的级别概述了使用 SAML 2.0 配置非库（例如：自定义）应用程序联合单一登录所需的步骤。

-   [在 Azure AD 中配置应用程序的元数据值（登录 URL、标识符、答复 URL）](#_Configuring_single_sign-on)

-   [选择用户标识符，并添加要发送给应用程序的用户属性](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [检索 Azure AD 元数据和证书](#download-the-azure-ad-metadata-or-certificate)

-   [在应用程序中配置 Azure AD 元数据值（登录 URL、颁发者、注销 URL 和证书）](#_Configuring_single_sign-on)

-   [将用户分配到应用程序](#_Assign_users_to_the_application)

## <a name="configuring-single-sign-on-to-non-gallery-applications"></a>配置非库应用程序的单一登录

若要为不在 Azure AD 库中的应用程序配置单一登录，请按照以下步骤操作：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”或“共同管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  在“企业应用程序”窗格的右上角，单击“添加”按钮。

6.  在“添加自己的应用”部分中，单击“非库应用程序”

7.  在“名称”文本框中输入应用程序的名称。

8.  单击“添加”按钮，添加该应用程序。

9.  在应用程序加载后，在应用程序的左侧导航菜单中单击“单一登录”。

10. 在“模式”下拉列表中选择“基于 SAML 的登录”。

11. 在“域和 URL”中输入所需的值。 应从应用程序供应商处获取这些值。

   1. 要将应用程序配置为 IdP 启动的 SSO，请输入“回复 URL”和“标识符”。

   2. **可选：** 要将应用程序配置为 SP 启动的 SSO，则“登录 URL”是必需值。

12. 在“用户属性”的“用户标识符”下拉列表中，为用户选择唯一标识符。

13. **可选：** 单击“查看和编辑其他所有用户属性”，以编辑在用户登录时要发送至应用程序（位于 SAML 令牌中）的属性。

   若要添加属性：

   1. 单击“添加属性”。 输入“名称”，并从下拉列表中选择“值”。

   2. 单击“保存”。 然后就能在表中看到新属性了。

14. 单击“配置 &lt;应用程序名称&gt;”以访问关于了解如何在应用程序中配置单一登录的文档。 此外，还具有应用程序要求的 Azure AD URL 和证书。

15. [将用户分配到应用程序。](#assign-users-to-the-application)

## <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>选择用户标识符，并添加要发送给应用程序的用户属性

若要选择用户标识符或添加用户属性，请按照以下步骤操作：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”或“共同管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  单击“所有应用程序”，查看所有应用程序的列表。

  * 如果未看到要在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设置为“所有应用程序”。

6.  选择已配置单一登录的应用程序。

7.  在应用程序加载后，在应用程序的左侧导航菜单中单击“单一登录”。

8.  在“用户属性”部分的“用户标识符”下拉列表中，为用户选择唯一标识符。 所选项需与应用程序中的期望值匹配，以便对用户进行身份验证。

 >[!注意} Azure AD 根据所选值或应用程序要求的格式（位于 SAML AuthRequest 中）为 NameID 属性（用户标识符）选择格式。 有关详细信息，请参阅 NameIDPolicy 部分下的[单一登录 SAML 协议](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest)文章。
 >
 >

9.  若要添加用户属性，请单击“查看和编辑所有其他用户属性”，以编辑在用户登录时要发送至应用程序（位于 SAML 令牌中）的属性。

   若要添加属性：

   1. 单击“添加属性”。 输入“名称”，并从下拉列表中选择“值”。

   2. 单击“保存”。 然后就能在表中看到新属性了。

## <a name="download-the-azure-ad-metadata-or-certificate"></a>下载 Azure AD 元数据或证书

若要从 Azure AD 下载应用程序元数据或证书，请按照以下步骤操作：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”或“共同管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  单击“所有应用程序”，查看所有应用程序的列表。

   * 如果未看到要在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设置为“所有应用程序”。

6.  选择已配置单一登录的应用程序。

7.  在应用程序加载后，在应用程序的左侧导航菜单中单击“单一登录”。

8.  转到“SAML 签名证书”部分，并单击“下载”列值。 根据应用程序对于配置单一登录的要求，将看到下载元数据 XML 或下载证书选项。

Azure AD 不提供用于获取元数据的 URL。 元数据只能作为 XML 文件进行检索。

## <a name="assign-users-to-the-application"></a>将用户分配到应用程序

要直接将一个或多个用户分配到应用程序，请按照以下步骤操作：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  单击“所有应用程序”，查看所有应用程序的列表。

  * 如果未看到要在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设置为“所有应用程序”。

6.  从列表中选择要向其分配用户的应用程序。

7.  在应用程序加载后，在应用程序的左侧导航菜单中单击“用户和组”。

8.  单击“用户和组”列表顶部的“添加”按钮，以打开“添加分配”窗格。

9.  在“添加分配”窗格中，单击“用户和组”选择器。

10. 在“按名称或电子邮件地址搜索”搜索框中，键入要分配的用户的**全名**或**电子邮件地址**。

11. 将鼠标悬停在列表中的“用户”上方以显示“复选框”。 单击用户个人资料头像或徽标旁边的复选框，将用户添加到“已选择”列表。

12. **可选：** 如果想要**添加多个用户**，请在“按名称或电子邮件地址搜索”搜索框中，键入其他**全名**或**电子邮件地址**，然后单击复选框以将此用户添加到“已选择”列表。

13. 在完成用户的选择后，单击“选择”按钮将他们添加到要分配给应用程序的用户和组列表。

14. **可选：** 单击“添加分配”窗格中的“选择角色”选择器，选择一个角色来分配给所选用户。

15. 单击“分配”按钮，将应用程序分配给选定用户。

稍等片刻，所选用户就能使用解决方案描述部分所述的方法来启动这些应用程序了。

## <a name="customizing-the-saml-claims-sent-to-an-application"></a>自定义发送到应用程序的 SAML 声明

若要了解如何自定义发送到应用程序的 SAML 属性声明，请参阅 [Azure Active Directory 中的声明映射](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)获取详细信息。

## <a name="next-steps"></a>后续步骤
[使用应用程序代理为应用提供单一登录](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
