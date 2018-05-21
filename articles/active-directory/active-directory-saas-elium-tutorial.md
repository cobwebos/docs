---
title: 教程：Azure Active Directory 与 Elium 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Elium 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: fae344b3-5bd9-40e2-9a1d-448dcd58155f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: jeedes
ms.openlocfilehash: b64f4153908668117ff07b20701c834ce66d3c46
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-elium"></a>教程：Azure Active Directory 与 Elium 集成

本教程介绍如何将 Elium 与 Azure Active Directory (Azure AD) 集成。

将 Elium 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Elium。
- 可以让用户使用其 Azure AD 帐户自动登录到 Elium（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Elium 的集成，需要以下项目：

- Azure AD 订阅
- 已启用 Elium 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Elium
2. 配置和测试 Azure AD 单一登录

## <a name="adding-elium-from-the-gallery"></a>从库中添加 Elium
若要配置 Elium 与 Azure AD 的集成，需要从库中将 Elium 添加到托管 SaaS 应用列表。

**若要从库中添加 Elium，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中，键入“Elium”，在结果面板中选择“Elium”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 Elium](./media/active-directory-saas-elium-tutorial/tutorial_elium_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于一个名为“Britta Simon”的测试用户使用 Elium 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Elium 用户。 换句话说，需要建立 Azure AD 用户与 Elium 中相关用户之间的链接关系。

若要配置和测试 Elium 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Elium 测试用户](#create-an-elium-test-user)** - 在 Elium 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Elium 应用程序中配置单一登录。

**若要配置 Elium 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的 Elium 应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/active-directory-saas-elium-tutorial/tutorial_elium_samlbase.png)

3. 在“Elium 域和 URL”部分，如果要在 IDP 发起的模式下配置应用程序，请执行以下步骤：

    ![Elium 域和 URL 单一登录信息](./media/active-directory-saas-elium-tutorial/tutorial_elium_url.png)

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://<platform-domain>.elium.com/login/saml2/metadata`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<platform-domain>.elium.com/login/saml2/acs`

4. 如果要在 SP 发起的模式下配置应用程序，请选中“显示高级 URL 设置”，并执行以下步骤：

    ![Elium 域和 URL 单一登录信息](./media/active-directory-saas-elium-tutorial/tutorial_elium_url1.png)

    在“登录 URL”文本框中，使用以下模式键入 URL：` https://<platform-domain>.elium.com/login/saml2/login`
     
    > [!NOTE] 
    > 这些不是实际值。 从可以在 `https://<platform-domain>.elium.com/login/saml2/metadata` 中下载的 **SP 元数据文件**获取这些值。本教程稍后将对此做出说明。

5. Elium 应用程序需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。

    ![配置单一登录](./media/active-directory-saas-elium-tutorial/tutorial_attribute.png)

6. 在“单一登录”对话框的“用户属性”部分中，按上图所示配置 SAML 令牌属性，再执行以下步骤：
           
    | 属性名称 | 属性值 |   
    | ---------------| ----------------|
    | 电子邮件   |user.mail |
    | first_name| user.givenname |
    | last_name| user.surname|
    | job_title| user.jobtitle|
    | company| user.companyname|
    
    > [!NOTE]
    > 这是默认声明。 **只需电子邮件声明**。 对于 JIT 预配，也只需电子邮件声明。 其他自定义声明根据客户平台的不同而异。

    a. 单击“添加属性”，打开“添加属性”对话框。

    ![配置单一登录](./media/active-directory-saas-elium-tutorial/tutorial_attribute_04.png)

    b. 在“名称”文本框中，键入为该行显示的属性名称。

    ![配置单一登录](./media/active-directory-saas-elium-tutorial/tutorial_attribute_05.png)

    c. 在“值”列表中，选择为该行显示的属性值。

    d. 将命名空间保留空白。
    
    e. 单击“确定” 。 

5. 在“SAML 签名证书”部分中，单击“元数据 XML”，并在计算机上保存元数据文件。

    ![证书下载链接](./media/active-directory-saas-elium-tutorial/tutorial_elium_certificate.png) 

6. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/active-directory-saas-elium-tutorial/tutorial_general_400.png)
    
7. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Elium 公司站点。

8. 在右上角单击“用户配置文件”，然后选择“管理”。

    ![配置单一登录](./media/active-directory-saas-elium-tutorial/user1.png)

9. 选择“安全性”选项卡。

    ![配置单一登录](./media/active-directory-saas-elium-tutorial/user2.png)

10. 向下滚动到“单一登录(SSO)”部分中，并执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-elium-tutorial/user3.png)

    a. 复制“验证 SAML2 身份验证是否适用于你的帐户”值，并将其粘贴到 Azure 门户上“Elium 域和 URL”部分中的“登录 URL”文本框。

    > [!NOTE]
    > 配置 SSO 后，始终可以通过以下 URL 访问默认的远程登录页：`https://<platform_domain>/login/regular/login` 

    b. 选中“启用 SAML2 联合身份验证”复选框。

    c. 选中“JIT 预配”复选框。

    d. 单击“下载”按钮打开“SP 元数据”。

    e. 在“SP 元数据”文件中搜索“entityID”，复制“entityID”值，并将其粘贴到 Azure 门户上“Elium 域和 URL”部分中的“标识符”文本框。 

    ![配置单一登录](./media/active-directory-saas-elium-tutorial/user4.png)

    f. 在“SP 元数据”文件中搜索“AssertionConsumerService”，复制“Location”值，并将其粘贴到 Azure 门户上“Elium 域和 URL”部分中的“回复 URL”文本框。

    ![配置单一登录](./media/active-directory-saas-elium-tutorial/user5.png)

    g. 在写字板中打开从 Azure 门户下载的元数据文件内容，复制内容并将其粘贴到“IdP 元数据”文本框中。

    h. 单击“ **保存**”。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/active-directory-saas-elium-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/active-directory-saas-elium-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/active-directory-saas-elium-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/active-directory-saas-elium-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。
 
### <a name="create-an-elium-test-user"></a>创建 Elium 测试用户

本部分的目的是在 Elium 中创建名为“Britta Simon”的用户。 Elium 支持在默认情况下启用的实时预配。 此部分不存在任何操作项。 如果新用户尚不存在，可在尝试访问 Elium 期间创建该用户。
>[!Note]
>如果需要手动创建用户，请联系 [Elium 支持团队](mailto:support@elium.com)。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，我们通过向 Britta Simon 授予 Elium 的访问权限，使其能够使用 Azure 单一登录。

![分配用户角色][200] 

**若要将 Britta Simon 分配到 Elium，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Elium”。

    ![应用程序列表中的 Elium 链接](./media/active-directory-saas-elium-tutorial/tutorial_elium_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击 Elium 磁贴时，应会自动登录到 Elium 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-elium-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-elium-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-elium-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-elium-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-elium-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-elium-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-elium-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-elium-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-elium-tutorial/tutorial_general_203.png

