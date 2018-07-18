---
title: 教程：Azure Active Directory 与 Teamphoria 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Teamphoria 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d569c705-6f0f-4ec1-b485-ba82526b5d32
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2018
ms.author: jeedes
ms.openlocfilehash: 0ff053b88a718c152d31b593a7759b034347f3ef
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2018
ms.locfileid: "36293659"
---
# <a name="tutorial-azure-active-directory-integration-with-teamphoria"></a>教程：Azure Active Directory 与 Teamphoria 集成

本教程介绍了如何将 Teamphoria 与 Azure Active Directory (Azure AD) 集成。

将 Teamphoria 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Teamphoria
- 可以让用户使用其 Azure AD 帐户自动登录到 Teamphoria（单一登录）
- 可在一个中心位置（即 Azure 门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Teamphoria 的集成，需要以下项：

- Azure AD 订阅
- 已启用 Teamphoria 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。
本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Teamphoria
2. 配置和测试 Azure AD 单一登录

## <a name="adding-teamphoria-from-the-gallery"></a>从库中添加 Teamphoria
要配置 Teamphoria 与 Azure AD 的集成，需要从库中将 Teamphoria 添加到托管的 SaaS 应用列表。

**若要从库中添加 Teamphoria，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 单击对话框顶部的“添加”按钮。

    ![应用程序][3]

4. 在搜索框中，键入“Teamphoria”。

    ![创建 Azure AD 测试用户](./media/teamphoria-tutorial/tutorial_teamphoria_search.png)

5. 在结果窗格中，选择“Teamphoria”，并单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/teamphoria-tutorial/tutorial_teamphoria_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，将基于名为“Britta Simon”的测试用户配置和测试 Teamphoria 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Teamphoria 用户。 换句话说，需要建立 Azure AD 用户与 Teamphoria 中相关用户之间的链接关系。

若要配置和测试 Teamphoria 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Teamphoria 测试用户](#creating-a-teamphoria-test-user)** - 在 Teamphoria 中创建 Britta Simon 的对应用户，并将其链接到她的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，会在 Azure 门户中启用 Azure AD 单一登录并在 Teamphoria 应用程序中配置单一登录。

**若要配置 Teamphoria 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户的“Teamphoria”应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。

    ![配置单一登录](./media/teamphoria-tutorial/tutorial_teamphoria_samlbase.png)

3. 在“Teamphoria 域和 URL”部分中，执行以下步骤：

    ![配置单一登录](./media/teamphoria-tutorial/tutorial_teamphoria_url.png)

    在“登录 URL”文本框中，使用以下模式键入 URL：`https://<sub-domain>.teamphoria.com/login`   

    > [!NOTE] 
    > 登录 URL 值不是真实值。 需使用实际登录 URL 更新此值。 联系 [Teamphoria 客户端支持团队](https://www.teamphoria.com/)以获取登录 URL。

4. 在“SAML 签名证书”部分中，单击“证书(Base64)”，并在计算机上保存证书。

    ![配置单一登录](./media/teamphoria-tutorial/tutorial_teamphoria_certificate.png)

5. 单击“保存”按钮。

    ![配置单一登录](./media/teamphoria-tutorial/tutorial_general_400.png)

6. 在“Teamphoria 配置”部分中，单击“配置 Teamphoria”打开“配置登录”窗口。 从“快速参考”部分中复制“SAML 单一登录服务 URL”。

    ![配置单一登录](./media/teamphoria-tutorial/tutorial_teamphoria_configure.png)

7. 若要在“Teamphoria”端配置单一登录，请以管理员身份登录 Teamphoria 应用程序。

8. 转到左侧工具栏中的“管理员设置”选项，并在“配置”选项卡下单击“单一登录”，以打开 SSO 配置窗口。

    ![配置单一登录](./media/teamphoria-tutorial/admin_sso_configure.png)

9. 单击右上角的“添加新的标识提供程序”选项，以打开用于添加 SSO 设置的窗体。

    ![配置单一登录](./media/teamphoria-tutorial/add_new_identity_provider.png)

10. 在字段中输入详细信息，如下所述 -

    ![配置单一登录](./media/teamphoria-tutorial/Teamphoria_sso_save.png)

    a. 显示名称：在管理页上输入插件的显示名称。

    b. 按钮名称：会在用于通过 SSO 登录的登录页上显示的选项卡的名称。

    c. 证书：在记事本中打开之前从 Azure 门户下载的证书，复制相同的内容并将其粘贴到此处的框中。

    d. 入口点：粘贴之前从 Azure 门户中复制的“SAML 单一登录服务 URL”。

    e. 将选项切换为“打开”，然后单击“保存”。

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为“Britta Simon”的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/teamphoria-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![创建 Azure AD 测试用户](./media/teamphoria-tutorial/create_aaduser_02.png) 

3. 在对话框顶部单击“添加”，打开“用户”对话框。
 
    ![创建 Azure AD 测试用户](./media/teamphoria-tutorial/create_aaduser_03.png)

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/teamphoria-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b. 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d. 单击“创建”。

### <a name="creating-a-teamphoria-test-user"></a>创建 Teamphoria 测试用户

要使 Azure AD 用户能够登录 Teamphoria，必须将这些用户预配到 Teamphoria 中。 对于 Teamphoria，需要手动执行预配。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录 Teamphoria 公司站点。

2. 单击左侧工具栏上的“管理员”设置，并在“管理”选项卡下单击“用户”，以打开用户的管理页。

    ![添加员工](./media/teamphoria-tutorial/admin_manage_users.png)

3. 单击“手动邀请”选项。

    ![邀请人员](./media/teamphoria-tutorial/admin_manage_add_users.png)

4. 在此页上，执行以下操作。
    
    ![邀请人员](./media/teamphoria-tutorial/manual_user_invite.png)

    a. 在“电子邮件地址”文本框中，键入 Britta Simon 的“电子邮件地址”。

    b. 在“名字”文本框中，键入“Britta”。

    c. 在“姓氏”文本框中，键入“Simon”。

    d. 单击“邀请 1 名用户”。 用户需要接受邀请，才能在系统中创建用户。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Teamphoria 的权限，允许其使用 Azure 单一登录。

![分配用户][200]

**要将 Britta Simon 分配到 Teamphoria，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，然后单击“所有应用程序”。

    ![分配用户][201]

2. 在应用程序列表中，选择“Teamphoria”。

    ![配置单一登录](./media/teamphoria-tutorial/tutorial_teamphoria_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。

### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../active-directory-saas-access-panel-introduction.md)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/teamphoria-tutorial/tutorial_general_01.png
[2]: ./media/teamphoria-tutorial/tutorial_general_02.png
[3]: ./media/teamphoria-tutorial/tutorial_general_03.png
[4]: ./media/teamphoria-tutorial/tutorial_general_04.png

[100]: ./media/teamphoria-tutorial/tutorial_general_100.png

[200]: ./media/teamphoria-tutorial/tutorial_general_200.png
[201]: ./media/teamphoria-tutorial/tutorial_general_201.png
[202]: ./media/teamphoria-tutorial/tutorial_general_202.png
[203]: ./media/teamphoria-tutorial/tutorial_general_203.png
