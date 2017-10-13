---
title: "教程：Azure Active Directory 与 Form.com 集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 与 Form.com 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: f1bc0112-315c-4e6f-8c69-7c6873007bcf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: jeedes
ms.openlocfilehash: fe3fa637e570ebdb01cfdebc6ba3e000c05b6a8c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-formcom"></a>教程：Azure Active Directory 与 Form.com 集成

在本教程中，了解如何将 Form.com 与 Azure Active Directory (Azure AD) 集成。

将 Form.com 与 Azure AD 集成提供以下优势：

- 可以在 Azure AD 中控制谁有权访问 Form.com。
- 可以让用户使用其 Azure AD 帐户自动登录到 Form.com（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Form.com 的集成，需要以下项：

- Azure AD 订阅
- 已启用 Form.com 单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Form.com
2. 配置并测试 Azure AD 单一登录

## <a name="adding-formcom-from-the-gallery"></a>从库中添加 Form.com
若要配置 Form.com 与 Azure AD 的集成，需要从库中将 Form.com 添加到托管 SaaS 应用列表。

**若要从库中添加 Form.com，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新建应用程序”按钮][3]

4. 在搜索框中，键入“Form.com”，在结果面板中选择“Form.com”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 Form.com](./media/active-directory-saas-formcom-tutorial/tutorial_form.com_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于一个名为“Britta Simon”的测试用户使用 Form.com 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Form.com 用户。 换句话说，需要在 Azure AD 用户与 Form.com 中的相关用户之间建立链接关系。

通过将 Azure AD 中“用户名”的值指定为 Form.com 中“用户名”的值来建立此链接关系。

若要配置和测试 Form.com 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Form.com 测试用户](#create-a-formcom-test-user)** - 在 Form.com 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Form.com 应用程序中配置单一登录。

**若要配置 Form.com 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的 **Form.com** 应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/active-directory-saas-formcom-tutorial/tutorial_form.com_samlbase.png)

3. 在“Form.com 域和 URL”部分中，执行以下步骤：

    ![Form.com 域和 URL 单一登录信息](./media/active-directory-saas-formcom-tutorial/tutorial_form.com_url.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<subdomain>.wa-form.com`

    b. 在“标识符”文本框中，使用以下模式键入 URL：`https://<subdomain>.form.com`

    c. 在“回复 URL”文本框中，使用以下模式键入 URL：
    | |
    |--|
    | `https://<subdomain>.wa-form.com/Member/UserAccount/SAML2.action` |
    | `https://<subdomain>.form.com/Member/UserAccount/SAML2.action` |
    
    > [!NOTE] 
    > 这些不是实际值。 请使用实际登录 URL、回复 URL 和标识符更新这些值。 请联系 [Form.com 客户端支持团队](https://form.com/about/company/contact-us/)获取这些值。 
 
4. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。

    ![证书下载链接](./media/active-directory-saas-formcom-tutorial/tutorial_form.com_certificate.png) 

5. 若要生成**元数据 URL**，请执行以下步骤：

    a. 单击“应用注册”。
    
    ![配置 appreg](./media/active-directory-saas-formcom-tutorial/tutorial_form.com_appregistrations.png)
   
    b. 单击“终结点”以打开“终结点”对话框。  
    
    ![配置 Endpointcon](./media/active-directory-saas-formcom-tutorial/tutorial_form.com_endpointicon.png)

    c. 单击复制按钮以复制**联合元数据文档** URL 并将其粘贴到记事本。
    
    ![配置终结点](./media/active-directory-saas-formcom-tutorial/tutorial_form.com_endpoint.png)
     
    d.单击“下一步”。 现在，转到 **Form.com** 的属性页，使用“复制”按钮复制**应用程序 ID** 并将其粘贴到记事本中。
 
    ![配置 appid](./media/active-directory-saas-formcom-tutorial/tutorial_form.com_appid.png)

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，并单击“确定”。 使用以下模式生成**元数据 URL**：`<FEDERATION METADATA DOCUMENT url>?appid=<application id>`

6. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/active-directory-saas-formcom-tutorial/tutorial_general_400.png)

7. 在“Form.com 配置”部分中，单击“配置 Form.com”打开“配置登录”窗口。 从“快速参考”部分中复制“SAML 单一登录服务 URL”

    ![Form.com 配置](./media/active-directory-saas-formcom-tutorial/tutorial_form.com_configure.png) 

8. 若要在“Form.com”端配置单一登录，需要将下载的“证书 (Base64)”、“元数据 URL”和“SAML 单一登录服务 URL”发送给 [Form.com 支持团队](https://form.com/about/company/contact-us/)。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/active-directory-saas-formcom-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/active-directory-saas-formcom-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/active-directory-saas-formcom-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/active-directory-saas-formcom-tutorial/create_aaduser_04.png)

    a.在“横幅徽标”下面，选择“删除上传的徽标”。 在“姓名”框中，键入“BrittaSimon”。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建” 。
 
### <a name="create-a-formcom-test-user"></a>创建 Form.com 测试用户

本部分的目的是在 Form.com 中创建名为 Britta Simon 的用户。请与 [Form.com 支持团队](https://form.com/about/company/contact-us/)协作，将用户添加到 Form.com 帐户中。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Form.com 的权限，允许她使用 Azure 单一登录。

![分配用户角色][200] 

**若要将 Britta Simon 分配到 Form.com，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Form.com”。

    ![应用程序列表中的 Form.com 链接](./media/active-directory-saas-formcom-tutorial/tutorial_form.com_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Form.com 磁贴时，应自动登录到 Form.com 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-formcom-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-formcom-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-formcom-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-formcom-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-formcom-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-formcom-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-formcom-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-formcom-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-formcom-tutorial/tutorial_general_203.png

