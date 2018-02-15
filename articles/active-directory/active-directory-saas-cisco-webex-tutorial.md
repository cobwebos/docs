---
title: "教程：Azure Active Directory 与 Cisco Webex 的集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 Cisco Webex 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory7
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 42632dcf8997ec5e987ac8a6615aae24e903399a
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2017
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>教程：Azure Active Directory 与 Cisco Webex 的集成

本教程介绍如何将 Cisco Webex 与 Azure Active Directory (Azure AD) 集成。

将 Cisco Webex 与 Azure AD 集成可提供以下优势：

- 可以在 Azure AD 中控制谁有权访问 Cisco Webex。
- 可以让用户使用他们的 Azure AD 帐户自动登录到 Cisco Webex。
- 可在一个中心位置（即 Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅[什么是使用 Azure Active Directory 的应用程序访问和单一登录？](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Cisco Webex 的集成，需要具有以下项：

- Azure AD 订阅
- 启用了 Cisco Webex 单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

若要测试本教程中的步骤，请遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Cisco Webex
2. 配置和测试 Azure AD 单一登录

## <a name="add-cisco-webex-from-the-gallery"></a>从库中添加 Cisco Webex
若要配置 Cisco Webex 与 Azure AD 的集成，需要从库中将 Cisco Webex 添加到托管 SaaS 应用的列表。

**若要从库中添加 Cisco Webex，请执行以下步骤：**

1. 在 [Azure 门户](https://portal.azure.com)的左窗格中，选择“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 转到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请选择对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中，键入“Cisco Webex”。 

5. 从结果窗格中选择“Cisco Webex”。 然后选择“添加”按钮以添加该应用程序。

    ![结果列表中的 Cisco Webex](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为“Britta Simon”的测试用户配置和测试 Cisco Webex 的 Azure AD 单一登录。

为使单一登录能正常工作，Azure AD 需要能够确定在 Azure AD 中与 Cisco Webex 用户相对应的用户。 换句话说，需要建立 Azure AD 用户与 Cisco Webex 中相关用户之间的关联。

在 Cisco Webex 中，将“用户名”的值指定为与 Azure AD 中的“用户名”相同的值。 现在，已关联这两个用户。 

若要配置和测试 Cisco Webex 的 Azure AD 单一登录，请完成以下构建基块：

1. [配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)，使用户能够使用此功能。
2. [创建 Azure AD 测试用户](#create-an-azure-ad-test-user)，使用 Britta Simon 测试 Azure AD 单一登录。
3. [创建 Cisco Webex 测试用户](#create-a-cisco-webex-test-user)以在 Cisco Webex 中创建 Britta Simon 的对应用户，该用户与 Azure AD 中表示 Britta Simon 的用户相关联。
4. [分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)，使 Britta Simon 能够使用 Azure AD 单一登录。
5. [测试单一登录](#test-single-sign-on)，验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Cisco Webex 应用程序中配置单一登录。

**若要配置 Cisco Webex 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的“Cisco Webex”应用程序集成页上，选择“单一登录”。

    ![配置单一登录链接][4]

2. 若要启用单一登录，请在“单一登录”对话框中的“模式”下拉列表中，选择“基于 SAML 的登录”。
 
    ![“单一登录”对话框](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_samlbase.png)

3. 在“Cisco Webex 域和 URL”部分中执行以下步骤：

    ![Cisco Webex 域和 URL 单一登录信息](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_url.png)

    a. 在“登录 URL”框中，使用以下模式键入 URL：`https://<subdomain>.webex.com`

    b. 在“标识符”框中键入 URL `http://www.webex.com`。

    c. 在“回复 URL”框中，使用以下模式键入 URL：`https://company.webex.com/dispatcher/SAML2AuthService?siteurl=company`
     
    > [!NOTE] 
    > 这些不是实际值。 使用实际的回复 URL 和登录 URL 更新这些值。 请联系 [Cisco Webex 客户端支持团队](https://www.webex.co.in/support/support-overview.html)获取这些值。 

5. 在“SAML 签名证书”部分中，选择“元数据 XML”，并将元数据文件保存在计算机上。

    ![证书下载链接](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_certificate.png) 

6. 选择“保存”。

    ![配置单一登录“保存”按钮](./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_400.png)
    
6. 在“Cisco Webex 配置”部分中，选择“配置 Cisco Webex”以打开“配置登录”窗口。 从“快速参考”部分中复制“注销 URL”、“SAML 实体 ID”和“SAML 单一登录服务 URL”。

    ![配置单一登录](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_configure.png) 

7. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Cisco Webex 公司网站。

8. 在顶部菜单中，选择“网站管理”。

    ![网站管理](./media/active-directory-saas-cisco-webex-tutorial/ic777621.png "Site Administration")

9. 在“管理网站”部分中，选择“SSO 配置”。
   
    ![SSO 配置](./media/active-directory-saas-cisco-webex-tutorial/ic777622.png "SSO Configuration")

10. 在“联合 Web SSO 配置”部分中，执行以下步骤：
   
    ![联合 SSO 配置](./media/active-directory-saas-cisco-webex-tutorial/ic777623.png "Federated SSO Configuration")  

    a. 在“联合身份验证协议”列表中，选择“SAML 2.0”。

    b. 对于“SSO 配置文件”，请选择“SP 发起”。

    c. 在记事本中打开下载的证书，然后复制内容。

    d.单击“下一步”。 选择“导入 SAML 元数据”，然后粘贴已复制的证书内容。

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，并单击“确定”。 在“SAML 的颁发者(IdP ID)”框中，粘贴从 Azure 门户复制的“SAML 实体 ID”值。

    f. 在“客户 SSO 服务登录 URL”框中，粘贴从 Azure 门户复制的“SAML 单一登录服务 URL”。

    g. 从“NameID 格式”列表中，选择“电子邮件地址”。

    h. 在“AuthnContextClassRef”框中，键入“urn:oasis:names:tc:SAML:2.0:ac:classes:Password”。

    i. 在“客户 SSO 服务注销 URL”框中，粘贴从 Azure 门户复制的“注销 URL”。
   
    j. 选择“更新”。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了。 从“Active Directory” > “企业应用程序”部分添加该应用后，选择“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在 [Azure AD 嵌入式文档](https://go.microsoft.com/fwlink/?linkid=845985)中阅读有关嵌入式文档功能的详细信息。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，选择“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/active-directory-saas-cisco-webex-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，选择“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/active-directory-saas-cisco-webex-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，请在“所有用户”对话框顶部选择“添加”。

    ![“添加”按钮](./media/active-directory-saas-cisco-webex-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/active-directory-saas-cisco-webex-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d.单击“下一步”。 选择“创建”。
 
### <a name="create-a-cisco-webex-test-user"></a>创建 Cisco Webex 测试用户

为了使 Azure AD 用户能够登录到 Cisco Webex，必须在 Cisco Webex 中预配这些用户。 对于 Cisco Webex，需要手动执行预配。

**若要预配用户帐户，请执行以下步骤：**

1. 登录到你的“Cisco Webex”租户。

2. 转到“管理用户” > “添加用户”。
   
    ![添加用户](./media/active-directory-saas-cisco-webex-tutorial/ic777625.png "添加用户")

3. 在“添加用户”部分中执行以下步骤：
   
    ![添加用户](./media/active-directory-saas-cisco-webex-tutorial/ic777626.png "添加用户")   

    a. 对于“帐户类型”，请选择“主机”。

    b. 在“名字”框中，键入用户的名字（在此示例中为“Britta”）。

    c. 在“姓氏”框中，键入用户的姓氏（在此示例中为“Simon”）。

    d.单击“下一步”。 在“用户名”框中，键入用户的电子邮件（在此示例中为 Brittasimon@contoso.com）。

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，并单击“确定”。 在“电子邮件”框中，键入用户的电子邮件地址（在此示例中为 Brittasimon@contoso.com）。

    f. 在“密码”框中，键入用户的密码。

    g. 在“确认密码”框中，重新输入用户的密码。

    h. 选择“添加”。

>[!NOTE]
>可以使用 Cisco Webex 提供的任何其他 Cisco Webex 用户帐户创建工具或 API 来预配 Azure AD 用户帐户。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，将授予用户 Britta Simon 访问 Cisco Webex 的权限，使其能够使用 Azure 单一登录。

![分配用户角色][200] 

**若要将 Britta Simon 分配到 Cisco Webex，请执行以下步骤：**

1. 在 Azure 门户中，打开应用程序视图。 接着转到目录视图，然后转到“企业应用程序”。  

2. 选择“所有应用程序”。

    ![分配用户][201] 

3. 在应用程序列表中，选择“Cisco Webex”。

    ![应用程序列表中的 Cisco Webex 链接](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_app.png)  

3. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接][202]

4. 选择“添加”按钮。 然后，在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框中，选择“用户”列表中的“Britta Simon”。

6. 在“用户和组”对话框中，单击“选择”按钮。

7. 在“添加分配”对话框中选择“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中选择 Cisco Webex 磁贴时，应会自动登录到 Cisco Webex 应用程序。

有关访问面板的详细信息，请参阅[访问面板简介](active-directory-saas-access-panel-introduction.md)。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_203.png

