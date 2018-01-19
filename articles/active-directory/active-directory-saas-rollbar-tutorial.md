---
title: "教程：Azure Active Directory 与 Rollbar 的集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 Rollbar 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 57537e54-9388-4272-a610-805ce45a451f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/04/2017
ms.author: jeedes
ms.openlocfilehash: bb8a81327163513ab721d2ad72da19173b59bc1f
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2018
---
# <a name="tutorial-azure-active-directory-integration-with-rollbar"></a>教程：Azure Active Directory 与 Rollbar 的集成

本教程介绍了如何将 Rollbar 与 Azure Active Directory (Azure AD) 进行集成。

将 Rollbar 与 Azure AD 集成可提供以下优势：

- 可以在 Azure AD 中控制谁有权访问 Rollbar。
- 可以让用户使用其 Azure AD 帐户自动登录到 Rollbar（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>系统必备

若要配置 Azure AD 与 Rollbar 的集成，需要具有以下各项：

- Azure AD 订阅
- 启用了单一登录的 Rollbar 订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Rollbar
2. 配置和测试 Azure AD 单一登录

## <a name="adding-rollbar-from-the-gallery"></a>从库中添加 Rollbar
若要配置 Rollbar 与 Azure AD 的集成，需要从库中将 Rollbar 添加到托管 SaaS 应用列表。

**若要从库中添加 Rollbar，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中，键入“Rollbar”，在结果面板中选择“Rollbar”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 Rollbar](./media/active-directory-saas-rollbar-tutorial/tutorial_rollbar_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为“Britta Simon”的测试用户配置和测试 Rollbar 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Rollbar 用户。 换句话说，需要在 Azure AD 用户与 Rollbar 中相关用户之间建立链接关系。

可通过将 Azure AD 中“用户名”的值指定为 Rollbar 中“用户名”的值来建立此链接关系。

若要配置和测试 Rollbar 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Rollbar 测试用户](#create-a-rollbar-test-user)** - 在 Rollbar 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Rollbar 应用程序中配置单一登录。

**若要配置 Rollbar 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中，在 **Rollbar** 应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/active-directory-saas-rollbar-tutorial/tutorial_rollbar_samlbase.png)

3. 在“Rollbar 域和 URL”部分中，如果要在 **IDP** 发起的模式下配置应用程序，请执行以下步骤：

    ![Rollbar 域和 URL 单一登录信息](./media/active-directory-saas-rollbar-tutorial/tutorial_rollbar_url.png)

    a. 在“标识符”文本框中，键入 URL：`https://saml.rollbar.com`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://rollbar.com/<accountname>/saml/sso/azure/`

4. 如果要在 SP 发起的模式下配置应用程序，请选中“显示高级 URL 设置”，并执行以下步骤：

    ![Rollbar 域和 URL 单一登录信息](./media/active-directory-saas-rollbar-tutorial/tutorial_rollbar_url1.png)

    在“登录 URL”文本框中，使用以下模式键入 URL：`https://rollbar.com/<accountname>/saml/login/azure/`
     
    > [!NOTE] 
    > 这些不是实际值。 请使用实际的“回复 URL”和“注销 URL”更新这些值。 请联系 [Rollbar 客户端支持团队](mailto:support@rollbar.com)获取这些值。 

5. 在“SAML 签名证书”部分中，单击“元数据 XML”，并在计算机上保存元数据文件。

    ![证书下载链接](./media/active-directory-saas-rollbar-tutorial/tutorial_rollbar_certificate.png) 

6. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/active-directory-saas-rollbar-tutorial/tutorial_general_400.png)
    
7. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Rollbar 公司站点。

8. 单击右上角的“配置文件设置”，然后单击“帐户名称设置”。
    
    ![配置](./media/active-directory-saas-rollbar-tutorial/general.png)

9. 单击“安全性”下的“标识提供者”。

    ![配置](./media/active-directory-saas-rollbar-tutorial/configure1.png)

10. 在“SAML 标识提供者”部分中，执行以下步骤：
    
    ![配置](./media/active-directory-saas-rollbar-tutorial/configure2.png)

    a. 从“SAML 标识提供者”下拉列表中，选择“AZURE”。

    b. 在记事本中打开元数据文件，将其内容复制到剪贴板，然后将其粘贴到“SAML 元数据”文本框中。

    c. 单击“ **保存**”。

11. 单击保存按钮后，屏幕将如下所示。 在此部分中执行以下步骤：
    
    ![配置](./media/active-directory-saas-rollbar-tutorial/configure3.png)

    a. 选中“要求通过 SAML 标识提供者进行登录”复选框。

    b. 单击“ **保存**”。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/active-directory-saas-rollbar-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/active-directory-saas-rollbar-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/active-directory-saas-rollbar-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/active-directory-saas-rollbar-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d.单击“下一步”。 单击“创建”。
 
### <a name="create-a-rollbar-test-user"></a>创建 Rollbar 测试用户

为了使 Azure AD 用户能够登录到 Rollbar，必须将其预配到 Rollbar 中。 对于 Rollbar，需要手动执行预配。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 Rollbar 公司站点。

2. 单击右上角的“配置文件设置”，然后单击“帐户名称设置”。

    ![用户](./media/active-directory-saas-rollbar-tutorial/general.png)

3. 单击“用户”。
    
    ![添加员工](./media/active-directory-saas-rollbar-tutorial/user1.png)

4. 单击“邀请团队成员”。

    ![邀请人员](./media/active-directory-saas-rollbar-tutorial/user2.png)

5. 在文本框中，输入用户名（例如 **brittasimon@contoso.com**），然后单击“添加/邀请”。

    ![邀请人员](./media/active-directory-saas-rollbar-tutorial/user3.png)

6. 用户收到邀请，在接受邀请后，会在系统中创建该用户。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Rollbar 的权限，允许其使用 Azure 单一登录。

![分配用户角色][200] 

**若要将 Britta Simon 分配到 Rollbar，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Rollbar”。

    ![应用程序列表中的 Rollbar 链接](./media/active-directory-saas-rollbar-tutorial/tutorial_rollbar_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

当在访问面板中单击 Rollbar 磁贴时，应当会自动登录到 Rollbar 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-rollbar-tutorial/tutorial_general_203.png

