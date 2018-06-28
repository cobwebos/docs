---
title: 访问面板中未显示已分配的应用程序 | Microsoft Docs
description: 关于访问面板中未显示应用程序的疑难解答
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.reviwer: japere
ms.openlocfilehash: e67f64a40d99bdced79f37cd206a048ee60cfbfb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "36333429"
---
# <a name="an-assigned-application-is-not-appearing-on-the-access-panel"></a>访问面板中未显示已分配的应用程序

访问面板是一个基于 Web 的门户，在 Azure Active Directory (Azure AD) 中拥有工作或学校帐户的用户可以使用它来查看和启动 Azure AD 管理员已授权他们访问的基于云的应用程序。 这些应用程序代表用户在 Azure AD 门户中进行配置。 应用程序必须正确配置并分配到用户或用户所属的组，才能在访问面板中看到应用程序。

用户看到的应用类型分为以下几类：

-   Office 365 应用程序

-   使用基于联合的 SSO 配置的 Microsoft 应用程序与第三方应用程序

-   基于密码的 SSO 应用程序

-   使用现有 SSO 解决方案的应用程序

## <a name="general-issues-to-check-first"></a>首先要检查的常规问题

-   如果刚将一个应用程序添加到用户，请在几分钟后重新登录该用户的访问面板，以查看应用程序是否已添加。

-   如果刚从用户或用户所在组中删除一个许可证，则此操作可能要花较长时间，这取决于被更改的组的大小和复杂程度。 登录访问面板前请多等一会儿。

## <a name="problems-related-to-application-configuration"></a>与应用程序配置相关的问题

由于配置不正确，应用程序可能无法在用户的访问面板中显示。 以下是可以用来解决应用程序配置方面的问题的一些方法：

-   [如何配置 Azure AD 库应用程序的联合单一登录](#how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application)

-   [如何配置非库应用程序的联合单一登录](#how-to-configure-federated-single-sign-on-for-a-non-gallery-application)

-   [如何配置 Azure AD 库应用程序的密码单一登录应用程序](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

-   [如何配置非库应用程序的密码单一登录应用程序](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

### <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>如何配置 Azure AD 库应用程序的联合单一登录

通过企业单一登录功能启用的 Azure AD 库中的所有应用程序都具有可用的分步教程。 可以访问[有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)，了解分步指南的详细信息。

若要从 Azure AD 库配置应用程序，需要：

-   [从 Azure AD 库添加应用程序](#add-an-application-from-the-azure-ad-gallery)

-   [在 Azure AD 中配置应用程序的元数据值（登录 URL、标识符、回复 URL）](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [选择用户标识符，并添加要发送给应用程序的用户属性](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [检索 Azure AD 元数据和证书](#download-the-azure-ad-metadata-or-certificate)

-   [在应用程序中配置 Azure AD 元数据值（登录 URL、颁发者、注销 URL 和证书）](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>从 Azure AD 库添加应用程序

若要从 Azure AD 库添加应用程序，请按照以下步骤操作：

1.  打开 [Azure 门户](https://portal.azure.com)，并以“全局管理员”或“共同管理员”身份登录

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  在“企业应用程序”窗格的右上角，单击“添加”按钮。

6.  在“从库中添加”部分的“输入名称”文本框中，键入应用程序的名称。

7.  选择要配置为单一登录的应用程序。

8.  在添加应用程序前，可以在“名称”文本框中更改名称。

9.  单击“添加”按钮，添加该应用程序。

稍等片刻，便可看到应用程序的配置窗格。

#### <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>为 Azure AD 库中的应用程序配置单一登录

若要为应用程序配置单一登录，请按照以下步骤操作：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”或“共同管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  单击“所有应用程序”，查看所有应用程序的列表。

  * 如果未看到想在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设为“所有应用程序”。

6.  选择要配置单一登录的应用程序。

7.  在应用程序加载后，在应用程序的左侧导航菜单中单击“单一登录”。

8.  从“模式”下拉列表中选择“基于 SAML 的登录”。

9.  在“域和 URL”中输入所需的值。 应从应用程序供应商处获取这些值。

   1. 要将应用程序配置为 SP 启动的 SSO，“登录 URL”是必需值。 对于某些应用程序，“标识符”也是必需值。

   2. 要将应用程序配置为 IdP 启动的 SSO，“回复 URL”是必需值。 对于某些应用程序，“标识符”也是必需值。

10. **可选：** 如果要查看非必需值，请单击“显示高级 URL 设置”。

11. 在“用户属性”的“用户标识符”下拉列表中，为用户选择唯一标识符。

12. **可选：** 单击“查看和编辑其他所有用户属性”，以编辑在用户登录时要发送至应用程序（位于 SAML 令牌中）的属性。

   若要添加属性：

   1. 单击“添加属性”。 输入“名称”，并从下拉列表中选择“值”。

   2. 单击“保存”。 然后就能在表中看到新属性了。

13. 单击“配置 &lt;应用程序名称&gt;”以访问关于了解如何在应用程序中配置单一登录的文档。 此外，你还拥有为应用程序设置 SSO 所必需的元数据 URL 和证书。

14. 单击“保存”以保存配置。

15. 将用户分配到应用程序。

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>选择用户标识符，并添加要发送给应用程序的用户属性

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

   >[!NOTE] 
   >Azure AD 会根据所选值或 SAML AuthRequest 中应用程序要求的格式，为 NameID 属性（用户标识符）选择格式。 有关详细信息，请参阅 NameIDPolicy 部分下的[单一登录 SAML 协议](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest)文章。
   >
   >

9.  若要添加用户属性，请单击“查看和编辑所有其他用户属性”，以编辑在用户登录时要发送至应用程序（位于 SAML 令牌中）的属性。

   若要添加属性：

   1. 单击“添加属性”。 输入“名称”，并从下拉列表中选择“值”。

   2. 单击“保存”。 然后将在表中看到新属性。

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>下载 Azure AD 元数据或证书

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

### <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>如何配置非库应用程序的联合单一登录

若要配置非库应用程序，需要拥有 Azure AD Premium 和支持 SAML 2.0 的应用程序。 有关 Azure AD 版本的详细信息，请访问 [Azure AD 定价](https://azure.microsoft.com/pricing/details/active-directory/)。

-   [在 Azure AD 中配置应用程序的元数据值（登录 URL、标识符、答复 URL）](#configuring-single-sign-on)

-   [选择用户标识符，并添加要发送给应用程序的用户属性](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [检索 Azure AD 元数据和证书](#download-the-azure-ad-metadata-or-certificate)

-   [在应用程序中配置 Azure AD 元数据值（登录 URL、颁发者、注销 URL 和证书）](#configuring-single-sign-on)

#### <a name="configure-the-applications-metadata-values-in-azure-ad-sign-on-url-identifier-reply-url"></a>在 Azure AD 中配置应用程序的元数据值（登录 URL、标识符、答复 URL）

若要为不在 Azure AD 库中的应用程序配置单一登录，请按照以下步骤操作：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”或“共同管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  在“企业应用程序”窗格的右上角，单击“添加”按钮。

6.  在“添加自己的应用”部分中，单击“非库应用程序”。

7.  在“名称”文本框中输入该应用程序的名称。

8.  单击“添加”按钮，添加该应用程序。

9.  在应用程序加载后，在应用程序的左侧导航菜单中单击“单一登录”。

10. 在“模式”下拉列表中选择“基于 SAML 的登录”。

11. 在“域和 URL”中输入所需的值。 应从应用程序供应商处获取这些值。

   1. 要将应用程序配置为 IdP 启动的 SSO，请输入“回复 URL”和“标识符”。

   2.  **可选：** 要将应用程序配置为 SP 启动的 SSO，“登录 URL”是必需值。

12. 在“用户属性”的“用户标识符”下拉列表中，为用户选择唯一标识符。

13. **可选：** 单击“查看和编辑其他所有用户属性”，以编辑在用户登录时要发送至应用程序（位于 SAML 令牌中）的属性。

   若要添加属性：

   1. 单击“添加属性”。 输入“名称”，并从下拉列表中选择“值”。

   2. 单击“保存”。 然后就能在表中看到新属性了。

14. 单击“配置 &lt;应用程序名称&gt;”以访问关于了解如何在应用程序中配置单一登录的文档。 此外，你还拥有应用程序所必需的 Azure AD URL 和证书。

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>选择用户标识符，并添加要发送给应用程序的用户属性

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

   >[!NOTE] 
   >Azure AD 会根据所选值或 SAML AuthRequest 中应用程序要求的格式，为 NameID 属性（用户标识符）选择格式。 有关详细信息，请参阅 NameIDPolicy 部分下的[单一登录 SAML 协议](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest)文章。
   >
   >

9.  若要添加用户属性，请单击“查看和编辑所有其他用户属性”，以编辑在用户登录时要发送至应用程序（位于 SAML 令牌中）的属性。

   若要添加属性：

   1. 单击“添加属性”。 输入“名称”，并从下拉列表中选择“值”。

   2. 单击“保存”。 然后就能在表中看到新属性了。

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>下载 Azure AD 元数据或证书

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

### <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>如何配置 Azure AD 库应用程序的密码单一登录

若要从 Azure AD 库配置应用程序，需要：

-   [从 Azure AD 库添加应用程序](#add-an-application-from-the-azure-ad-gallery)

-   [将应用程序配置为密码单一登录](#configure-the-application-for-password-single-sign-on)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>从 Azure AD 库添加应用程序

若要从 Azure AD 库添加应用程序，请按照以下步骤操作：

1.  打开 [Azure 门户](https://portal.azure.com)，并以“全局管理员”或“共同管理员”身份登录

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  在“企业应用程序”窗格的右上角，单击“添加”按钮。

6.  在“从库中添加”部分的“输入名称”文本框中，键入应用程序的名称。

7.  选择要配置为单一登录的应用程序。

8.  在添加应用程序前，可以在“名称”文本框中更改名称。

9.  单击“添加”按钮，添加该应用程序。

稍等片刻，便可看到应用程序的配置窗格。

#### <a name="configure-the-application-for-password-single-sign-on"></a>将应用程序配置为密码单一登录

若要为应用程序配置单一登录，请按照以下步骤操作：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”或“共同管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  单击“所有应用程序”，查看所有应用程序的列表。

   * 如果未看到想在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设为“所有应用程序”。

6.  选择要配置单一登录的应用程序。

7.  在应用程序加载后，在应用程序的左侧导航菜单中单击“单一登录”。

8.  选择“基于密码的登录”模式。

9.  [将用户分配到应用程序](#how-to-assign-a-user-to-an-application-directly)。

10. 此外，还可以通过下列步骤代表用户提供凭据：选择用户对应的行，单击“更新凭据”，并代表用户输入用户名和密码。 否则，会在启动时提示用户输入凭据。

### <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>如何配置非库应用程序的密码单一登录

若要从 Azure AD 库配置应用程序，需要：

-   [添加非库应用程序](#add-a-non-gallery-application)

-   [将应用程序配置为密码单一登录](#configure-the-application-for-password-single-sign-on)

#### <a name="add-a-non-gallery-application"></a>添加非库应用程序

若要从 Azure AD 库添加应用程序，请按照以下步骤操作：

1.  打开 [Azure 门户](https://portal.azure.com)，并以“全局管理员”或“共同管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  在“企业应用程序”窗格的右上角，单击“添加”按钮。

6.  单击“非库应用程序”。

7.  在“名称”文本框中输入应用程序的名称。 选择“添加”。

稍等片刻，便可看到应用程序的配置窗格。

#### <a name="configure-the-application-for-password-single-sign-on"></a>将应用程序配置为密码单一登录

若要为应用程序配置单一登录，请按照以下步骤操作：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”或“共同管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  单击“所有应用程序”，查看所有应用程序的列表。

    1.  如果未看到想在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设为“所有应用程序”。

6.  选择要配置单一登录的应用程序。

7.  在应用程序加载后，在应用程序的左侧导航菜单中单击“单一登录”。

8.  选择“基于密码的登录”模式。

9.  输入“登录 URL”。 这就是用户在其中输入用户名和密码进行登录时的 URL。 确保登录字段在 URL 中可见。

10. [将用户分配到应用程序](#how-to-assign-a-user-to-an-application-directly)。

11. 此外，还可以通过下列步骤代表用户提供凭据：选择用户对应的行，单击“更新凭据”，并代表用户输入用户名和密码。 否则，会在启动时提示用户输入凭据。

## <a name="problems-related-to-assigning-applications-to-users"></a>有关向用户分配应用程序的问题

由于未向应用程序分配用户，因此用户在其访问面板中可能看不到应用程序。 以下是一些检查方式：

-   [检查是否向应用程序分配了用户](#check-if-a-user-is-assigned-to-the-application)

-   [如何直接向应用程序分配用户](#how-to-assign-a-user-to-an-application-directly)

-   [检查是否向用户分配了与应用程序相关的许可证](#check-if-a-user-is-under-a-license-related-to-the-application)

-   [如何向用户分配许可证](#how-to-assign-a-user-a-license)

### <a name="check-if-a-user-is-assigned-to-the-application"></a>检查是否向用户分配了应用程序

若要检查是否向用户分配了应用程序，请遵循以下步骤：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  单击“所有应用程序”，查看所有应用程序的列表。

6.  **搜索**有关应用程序的名称。

7.  单击“用户和组”。

8.  检查用户是否已分配给应用程序。

   * 如果没有遵循“如何直接将用户分配到应用程序”中的步骤，请按此步骤来操作。

### <a name="how-to-assign-a-user-to-an-application-directly"></a>如何直接将用户分配到应用程序

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

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>检查用户是否分配了与应用程序相关的许可证

若要检查用户的已分配许可证，请按照以下步骤操作：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在导航菜单中，单击“用户和组”。

5.  单击“所有用户”。

6.  **搜索**感兴趣的用户，并**单击对应的行**进行选择。

7.  单击“许可证”查看当前已分配给用户的许可证。

  * 如果已向用户分配了 Office 许可证，则第一方 Office 应用程序会显示在用户的访问面板中。

### <a name="how-to-assign-a-user-a-license"></a>如何向用户分配许可证 

要将许可证分配给用户，请按照以下步骤操作：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在导航菜单中，单击“用户和组”。

5.  单击“所有用户”。

6.  **搜索**感兴趣的用户，并**单击对应的行**进行选择。

7.  单击“许可证”查看当前已分配给用户的许可证。

8.  单击“分配”按钮。

9.  从可用产品列表中选择**一个或多个产品**。

10. **可选**单击“分配选项”项精确分配产品。 完成此操作后，单击“确定”。

11. 单击“分配”按钮，将这些许可证分配给此用户。

## <a name="problems-related-to-assigning-applications-to-groups"></a>有关向组分配应用程序的问题

由于用户所在组已分配有应用程序，因此他们可以在访问面板中看到应用程序。 以下是一些检查方式：

-   [检查用户的组成员身份](#check-a-users-group-memberships)

-   [如何直接向组分配应用程序](#how-to-assign-an-application-to-a-group-directly)

-   [检查用户是否是已分配许可证的组的成员](#check-if-a-user-is-part-of-group-assigned-to-a-license)

-   [如何向组分配许可证](#how-to-assign-a-license-to-a-group)

### <a name="check-a-users-group-memberships"></a>检查用户的组成员身份

若要检查组的成员身份，请执行以下步骤：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在导航菜单中，单击“用户和组”。

5.  单击“所有用户”。

6.  **搜索**感兴趣的用户，并**单击对应的行**进行选择。

7.  单击“组”。

8.  检查用户是否是已分配应用程序的组的成员。

  * 要将用户从组中删除，请**单击组对应的行**，并选择删除。

### <a name="how-to-assign-an-application-to-a-group-directly"></a>如何直接向组分配应用程序

要直接将一个或多个组分配到应用程序，请遵循以下步骤：

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

10. 在“按名称或电子邮件地址搜索”搜索框中，键入要分配的组的**完整组名**。

11. 将鼠标悬停在列表中的**组**上以显示**复选框**。 单击组头像或徽标旁边的复选框以将用户添加到“已选择”列表。

12. **可选：** 如果想要**添加多个组**，请在“按名称或电子邮件地址搜索”搜索框中，键入其他**完整组名**，然后单击复选框以将此组添加到“已选择”列表。

13. 选择完所有组后，单击“选择”按钮将所有已选择的组添加到要分配给应用程序的用户和组的列表中。

14. **可选：** 单击“添加分配”窗格中的“选择角色”选择器，选择一个角色来分配给所选组。

15. 单击“分配”按钮，将应用程序分配给所选组。

很快所选用户便能在访问面板中启动这些应用程序。

### <a name="check-if-a-user-is-part-of-group-assigned-to-a-license"></a>检查用户是否是要向其分配许可证的组的成员

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在导航菜单中，单击“用户和组”。

5.  单击“所有用户”。

6.  **搜索**感兴趣的用户，并**单击对应的行**进行选择。

7.  单击“组”。

8.  单击特定组的行。

9.  单击“许可证”查看已分配给组的许可证有哪些。

   * 如果已向组分配 Office 许可证，则某个第一方 Office 应用程序可能会显示在用户的访问面板中。

### <a name="how-to-assign-a-license-to-a-group"></a>如何向组分配许可证

要将许可证分配给组，请按照以下步骤操作：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在导航菜单中，单击“用户和组”。

5.  单击“所有组”。

6.  **搜索**感兴趣的组，并**单击对应的行**进行选择。

7.  单击“许可证”查看当前已分配给组的许可证。

8.  单击“分配”按钮。

9.  从可用产品列表中选择**一个或多个产品**。

10. **可选**单击“分配选项”项精确分配产品。 完成此操作后，单击“确定”。

11. 单击“分配”按钮，将这些许可证分配给此组。 这可能需要较长时间，具体取决于组的大小和复杂程度。

>[!NOTE]
>若要加快速度，请考虑暂时直接向用户分配许可证。 
>
>

## <a name="next-steps"></a>后续步骤
[向 Azure Active Directory 添加新用户](active-directory-users-create-azure-portal.md)

