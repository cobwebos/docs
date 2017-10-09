---
title: "教程：Azure Active Directory 与 Pingboard 集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 Pingboard 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 57278d02a40aa92f07d61684e3c4d74aa0ac1b5b
ms.openlocfilehash: 67534da42ac27989a8b08cec4d6f9f9c31774264
ms.contentlocale: zh-cn
ms.lasthandoff: 09/28/2017

---
# <a name="tutorial-azure-active-directory-integration-with-pingboard"></a>教程：Azure Active Directory 与 Pingboard 集成

在本教程中，了解如何将 Pingboard 与 Azure Active Directory (Azure AD) 集成。

将 Pingboard 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Pingboard
- 可以让用户使用其 Azure AD 帐户自动登录到 Pingboard（单一登录）
- 可以在一个中心位置（即 Azure 门户）中管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Pingboard 的集成，需要具有以下项：

- Azure AD 订阅
- 启用了 Pingboard 单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Pingboard
2. 配置和测试 Azure AD 单一登录

## <a name="adding-pingboard-from-the-gallery"></a>从库中添加 Pingboard
要配置 Pingboard 与 Azure AD 的集成，需要从库中将 Pingboard 添加到托管 SaaS 应用列表。

**若要从库中添加 Pingboard，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![企业应用程序][2]
    
3. 单击对话框顶部的“添加”按钮。

    ![“新建应用程序”按钮][3]

4. 在搜索框中，键入“Pingboard”，在结果面板中选择“Pingboard”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 Pingboard](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

本部分基于名为“Britta Simon”的测试用户配置和测试 Pingboard 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Pingboard 用户。 换句话说，需要在 Azure AD 用户与 Pingboard 中相关用户之间建立链接关系。

通过将 Azure AD 中“用户名”的值分配为 Pingboard 中“用户名”的值来建立此链接关系。

若要配置和测试 Pingboard 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. [创建 Pingboard 测试用户](#create-a-pingboard-test-user) - 在 Pingboard 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

本部分在 Azure 门户中启用 Azure AD 单一登录，并在 PingBoard 应用程序中配置单一登录。

**若要配置 Pingboard 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户的“Pingboard”应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2.  在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_samlbase.png)

3. 在“Pingboard 域和 URL”部分中，如果要在“IDP”发起的模式下配置应用程序，请执行以下步骤：

    ![Pingboard 域和 URL 单一登录信息 IDP](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_url.png)

    a. 在“标识符”文本框中，键入 `http://app.pingboard.com/sp` 值

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<entity-id>.pingboard.com/auth/saml/consume`

4. 如果要在“SP”发起的模式下配置应用程序，请选中“显示高级 URL 设置”：

    ![Pingboard 域和 URL 单一登录信息 SP](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_sp_initiated01.png)

     在“登录 URL”文本框中，使用以下模式键入 URL：`https://<sub-domain>.pingboard.com/sign_in`

    > [!NOTE] 
    > 请注意，这些值不是实际值。 请使用实际的“回复 URL”和“注销 URL”更新这些值。 请联系 [Pingboard 客户端支持团队](https://support.pingboard.com/)获取这些值。

5. 在“SAML 签名证书”部分中，单击“元数据 XML”，并在计算机上保存 XML 文件。

    ![Pingboard 元数据 xml](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_certificate.png) 

6. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/active-directory-saas-pingboard-tutorial/tutorial_general_400.png)

7. 若要在 Pingboard 端配置 SSO，请打开新的浏览器窗口，并登录到 Pingboard 帐户。 必须是 Pingboard 管理员才能设置单一登录。

8. 从顶部菜单中选择“应用”>“集成”

    ![配置单一登录](./media/active-directory-saas-pingboard-tutorial/Pingboard_integration.png)

9.  在“集成”页上，找到“Azure Active Directory”磁贴，并单击它。

    ![Pingboard 单一登录集成](./media/active-directory-saas-pingboard-tutorial/Pingboard_aad.png)

10. 在后面的模式中，单击“配置”

    ![Pingboard 配置按钮](./media/active-directory-saas-pingboard-tutorial/Pingboard_configure.png)

11. 在以下页上，会出现“Azure SSO 集成已启用”。 在记事本中打开下载的元数据 XML 文件，并将其内容粘贴到 **IDP 元数据**。

    ![Pingboard SSO 配置界面](./media/active-directory-saas-pingboard-tutorial/Pingboard_sso_configure.png)

12. 验证文件后，如果所有内容都正确，则会立即启用单一登录。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![“Azure Active Directory”按钮](./media/active-directory-saas-pingboard-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![“用户和组”以及“所有用户”链接](./media/active-directory-saas-pingboard-tutorial/create_aaduser_02.png) 

3. 在对话框顶部，单击“添加”以打开“用户”对话框。
 
    ![“添加”按钮](./media/active-directory-saas-pingboard-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![“用户”对话框](./media/active-directory-saas-pingboard-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d.单击“下一步”。 单击“创建” 。
 
### <a name="create-a-pingboard-test-user"></a>创建 Pingboard 测试用户

要使 Azure AD 用户能够登录 Pingboard，必须将这些用户预配到 Pingboard 中。 对于 Pingboard，需要手动预配。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 Pingboard 公司站点。

2. 单击“目录”页上的“添加员工”按钮。

    ![添加员工](./media/active-directory-saas-pingboard-tutorial/create_testuser_add.png)

3. 在“添加员工”对话框页上，执行以下步骤：

    ![邀请人员](./media/active-directory-saas-pingboard-tutorial/create_testuser_name.png)

    a. 在“全名”文本框中，键入用户的全名，如 Britta Simon。

    b. 在“电子邮件”文本框中，键入用户的电子邮件地址，如 brittasimon@contoso.com。

    c. 在“职务”文本框中，键入 Britta Simon 的职务。

    d.单击“下一步”。 在“位置”下拉列表中，选择 Britta Simon 的位置。
    
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，并单击“确定”。 单击“添加”。   

4. 确认界面随即出现，以便确认添加用户。
    
    ![确认](./media/active-directory-saas-pingboard-tutorial/create_testuser_confirm.png)
        
    > [!NOTE]
    > Azure Active Directory 帐户持有者将收到一封电子邮件，其中包含用于在激活帐户前确认帐户的链接。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

本部分通过授予 Britta Simon 访问 Pingboard 的权限，允许其使用 Azure 单一登录。

![分配用户][200] 

**要将 Britta Simon 分配到 Pingboard，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Pingboard”。

    ![应用程序列表中的 Pingboard 链接](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。

单击访问面板中的“Pingboard”磁贴时，用户应自动登录到 Pingboard 应用程序。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_203.png

