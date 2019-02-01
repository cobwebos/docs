---
title: 教程：Azure Active Directory 与 DocuSign 的集成 | Microsoft 文档
description: 了解如何在 Azure Active Directory 和 DocuSign 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: jeedes
ms.openlocfilehash: 8838cb2479078a8b6b4f031128501f09284b6bdd
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55159260"
---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>教程：Azure Active Directory 与 DocuSign 的集成

本教程介绍如何将 DocuSign 与 Azure Active Directory (Azure AD) 集成。

将 DocuSign 与 Azure AD 集成具有优势：

- 可在 Azure AD 中控制谁有权访问 DocuSign。
- 可使用户通过其 Azure AD 帐户自动登录到 DocuSign（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 DocuSign 的集成，需要以下项：

- Azure AD 订阅
- 已启用 DocuSign 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述

在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 DocuSign
2. 配置和测试 Azure AD 单一登录

## <a name="adding-docusign-from-the-gallery"></a>从库中添加 DocuSign

若要配置 DocuSign 与 Azure AD 的集成，需要从库中将 DocuSign 添加到托管 SaaS 应用列表。

**若要从库中添加 DocuSign，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中，键入“DocuSign”，在结果面板中选择“DocuSign”，然后单击“添加”按钮添加应用程序。

    ![结果列表中的 DocuSign](./media/docusign-tutorial/tutorial_docusign_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将使用名为“Britta Simon”的测试用户配置和测试 DocuSign 的 Azure AD 单一登录。

为使单一登录能正常工作，Azure AD 需要知道与 Azure AD 用户相对应的 DocuSign 用户。 换句话说，需要在 Azure AD 用户与 DocuSign 中的相关用户之间建立链接关系。

若要配置和测试 DocuSign 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 DocuSign 测试用户](#creating-a-docusign-test-user)** - 在 DocuSign 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，会在 Azure 门户中启用 Azure AD 单一登录，并在 DocuSign 应用程序中配置单一登录。

**若要配置 DocuSign 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的“DocuSign”应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“选择单一登录方法”对话框中，单击“SAML”模式对应的“选择”，以启用单一登录。

    ![配置单一登录](common/tutorial_general_301.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框。

    ![配置单一登录](common/editconfigure.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    ![DocuSign 域和 URL 单一登录信息](./media/docusign-tutorial/tutorial_docusign_url.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL： `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. 在“标识符”文本框中，使用以下模式键入 URL：`https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    > [!NOTE]
    > 这些不是实际值。 本教程的“查看 SAML 2.0 终结点”部分稍后将介绍如何使用实际的登录 URL 和标识符来更新该值。

5. 在“SAML 签名证书”页的“SAML 签名证书”部分中，单击“下载”以下载“证书(Base64)”并将证书文件保存在计算机上。

    ![证书下载链接](./media/docusign-tutorial/tutorial_docusign_certificate.png) 

6. 在“设置 DocuSign”部分中，根据要求复制相应的 URL。

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

    ![DocuSign 配置](common/configuresection.png)

7. 在另一个 Web 浏览器窗口中，以管理员身份登录到 DocuSign 管理门户。

8. 在页面右上角单击个人资料徽标，然后单击“转到管理员”。
  
    ![配置单一登录][51]

9. 在域解决方案页单击“域”

    ![配置单一登录][50]

10. 在“域”部分下，单击“声明域”。

    ![配置单一登录][52]

11. 在“声明域”对话框中，在“域名”文本框中键入公司域，并单击“声明”。 确保对域进行验证并且其状态为活动。

    ![配置单一登录][53]

12. 在域解决方案页单击“标识提供者”。
  
    ![配置单一登录][54]

13. 在“标识提供者”部分下，单击“添加标识提供者”。 

    ![配置单一登录][55]

14. 在“标识提供者设置”页上，执行以下步骤：

    ![配置单一登录][56]

    a. 在“名称”文本框中，为配置键入一个唯一的名称。 请不要使用空格。

    b. 在“标识提供者颁发者”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值。

    c. 在“标识提供者登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值。

    d. 在“标识提供者注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值。

    e. 选择“对身份验证请求签名”。

    f. 对于“身份验证请求发送方式”，选择“POST”。

    g. 对于“注销请求发送方式”，选择“GET”。

    h. 在“自定义属性映射”部分中，单击“添加新映射”。

    ![配置单一登录][62]

    i. 选择要通过 Azure AD 声明映射的字段。 在此示例中，**emailaddress** 声明使用 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** 的值映射。 这是 Azure AD 中用于电子邮件声明的默认声明名称，然后单击“保存”。

    ![配置单一登录][57]

    > [!NOTE]
    > 请使用合适的“用户标识符”将用户从 Azure AD 映射到 Docusign 用户映射。 根据组织设置选择正确的字段并输入合适的值。

    j. 在“标识提供者证书”部分中，单击“添加证书”，然后上传已从 Azure AD 门户下载的证书并单击“保存”。

    ![配置单一登录][58]

    k. 在“标识提供者”部分中，单击“操作”，然后单击“终结点”。

    ![配置单一登录][59]

    l. 在“DocuSign 管理门户”上的“查看 SAML 2.0 终结点”部分中，执行以下步骤：

    ![配置单一登录][60]

    * 复制“服务提供程序颁发者 URL”，然后将其粘贴到 Azure 门户的“DocuSign 域和 URL”部分中的“标识符”文本框中。

    * 复制“服务提供程序登录 URL”，然后将其粘贴到 Azure 门户的“DocuSign 域和 URL”部分中的“登录 URL”文本框中。

    * 单击“关闭”。

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![创建 Azure AD 用户][100]

2. 选择屏幕顶部的“新建用户”。

    ![创建 Azure AD 测试用户](common/create_aaduser_01.png) 

3. 在“用户属性”中，按照以下步骤操作。

    ![创建 Azure AD 测试用户](common/create_aaduser_02.png)

    a. 在“名称”字段中，输入 BrittaSimon。
  
    b. 在“用户名”字段中键入 brittasimon@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选择“属性”，再选择“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 选择“创建”。

### <a name="creating-a-docusign-test-user"></a>创建 DocuSign 测试用户

本部分的目的是在 DocuSign 中创建名为“Britta Simon”的用户。 DocuSign 支持在默认情况下启用的实时预配。 此部分不存在任何操作项。 如果尚不存在用户，则在尝试访问 DocuSign 期间创建新用户。
>[!Note]
>如果需要手动创建用户，请联系  [DocuSign 支持团队](https://support.docusign.com/)。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 DocuSign 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序”，然后选择“所有应用程序”。

    ![分配用户][201]

2. 在应用程序列表中，选择“DocuSign”。

    ![配置单一登录](./media/docusign-tutorial/tutorial_docusign_app.png)

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

6. 在“添加分配”对话框中，选择“分配”按钮。

### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击 DocuSign 磁贴时，应该会自动登录到 DocuSign 应用程序。
有关访问面板的详细信息，请参阅[访问面板简介](../user-help/active-directory-saas-access-panel-introduction.md)。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
[50]: ./media/docusign-tutorial/tutorial_docusign_18.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[62]: ./media/docusign-tutorial/tutorial_docusign_30.png
