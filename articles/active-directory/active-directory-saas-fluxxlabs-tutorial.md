---
title: 教程：Azure Active Directory 与 Fluxx Labs 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Fluxx Labs 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d8fac770-bb57-4e1f-b50b-9ffeae239d07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2018
ms.author: jeedes
ms.openlocfilehash: 0bba820c14c5eddc6db99923e3fb1de58c110f4c
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
---
# <a name="tutorial-azure-active-directory-integration-with-fluxx-labs"></a>教程：Azure Active Directory 与 Fluxx Labs 集成

本教程介绍如何将 Fluxx Labs 与 Azure Active Directory (Azure AD) 集成。

将 Fluxx Labs 与 Azure AD 集成可提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Fluxx Labs。
- 可以让用户使用其 Azure AD 帐户自动登录到 Fluxx Labs（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Fluxx Labs 的集成，需要做好以下各项准备：

- Azure AD 订阅
- 启用了 Fluxx Labs 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Fluxx Labs
2. 配置和测试 Azure AD 单一登录

## <a name="adding-fluxx-labs-from-the-gallery"></a>从库中添加 Fluxx Labs
要配置 Fluxx Labs 与 Azure AD 的集成，需要从库中将 Fluxx Labs 添加到托管 SaaS 应用列表。

**若要从库添加 Fluxx Labs，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中，键入“Fluxx Labs”，在结果面板中选择“Fluxx Labs”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 Fluxx Labs](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_fluxxlabs_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于一个名为“Britta Simon”的测试用户使用 Fluxx Labs 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Fluxx Labs 用户。 换句话说，需要建立 Azure AD 用户与 Fluxx Labs 中相关用户之间的链接关系。

可通过将 Azure AD 中“用户名”的值指定为 Fluxx Labs 中“用户名”的值来建立此链接关系。

若要配置并测试 Fluxx Labs 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Fluxx Labs 测试用户](#create-a-fluxx-labs-test-user)** - 在 Fluxx Labs 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分，我们将在 Azure 门户中启用 Azure AD 单一登录并在 Fluxx Labs 应用程序中配置单一登录。

**若要配置 Fluxx Labs 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中，在 **Fluxx Labs** 应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_fluxxlabs_samlbase.png)

3. 在“Fluxx Labs 域和 URL”部分执行以下步骤：

    ![Fluxx Labs 域和 URL 单一登录信息](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_fluxxlabs_url.png)

    a. 在“标识符”文本框中，使用以下模式键入 URL：

    | 环境 | URL 模式|
    |-------------|------------|
    | 生产 | `https://<subdomain>.fluxx.io` |
    | 预生产 | `https://<subdomain>.preprod.fluxxlabs.com`|
        
    b. 在“回复 URL”文本框中，使用以下模式键入 URL：

    | 环境 | URL 模式|
    |-------------|------------|
    | 生产 | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | 预生产 | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|
        
    > [!NOTE] 
    > 这些不是实际值。 请使用实际标识符和回复 URL 更新这些值。 若要获取这些值，请联系 [Fluxx Labs 支持团队](mailto:travis@fluxxlabs.com)。

4. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。

    ![证书下载链接](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_fluxxlabs_certificate.png) 

5. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_400.png)

6. 在“Fluxx Labs 配置”部分，单击“配置 Fluxx Labs”打开“配置登录”窗口。 从“快速参考”部分中复制“SAML 实体 ID 和 SAML 单一登录服务 URL”。

    ![Fluxx Labs 配置](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_fluxxlabs_configure.png)

7. 在其他 Web 浏览器窗口中，以管理员身份登录 Fluxx Labs 公司站点。

8. 在“设置”部分下，选择“管理员”。

    ![Fluxx Labs 配置](./media/active-directory-saas-fluxxlabs-tutorial/config1.png)

9. 在管理面板中，选择“插件” > “集成”，然后选择“SAML SSO (已禁用)”

    ![Fluxx Labs 配置](./media/active-directory-saas-fluxxlabs-tutorial/config2.png)
    
10. 在属性部分执行以下步骤：
    
    ![Fluxx Labs 配置](./media/active-directory-saas-fluxxlabs-tutorial/config3.png)

    a. 选中“SAML SSO”复选框。

    b. 在“请求路径”文本框中，键入 **/auth/saml**。

    c. 在“回调路径”文本框中，键入 **/auth/saml/callback**。

    d. 在“断言使用者服务 URL (单一登录 URL)”文本框中，粘贴从 Azure 门户复制的“SAML 单一登录服务 URL”值。

    e. 在“受众 (SP 实体 ID)”文本框中，粘贴从 Azure 门户复制的“SAML 实体 ID”值。

    f. 在记事本中打开 base-64 编码的证书，将其内容复制到剪贴板，然后将其粘贴到“标识提供者证书”文本框。

    g. 在“名称标识符格式”文本框中，输入值 `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`。

    h. 单击“ **保存**”。

    > [!NOTE]
    > 保存内容后，字段会出于安全原因显示空白，但值已保存到配置中。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/active-directory-saas-fluxxlabs-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/active-directory-saas-fluxxlabs-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/active-directory-saas-fluxxlabs-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/active-directory-saas-fluxxlabs-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。
  
### <a name="create-a-fluxx-labs-test-user"></a>创建 Fluxx Labs 测试用户

若要使 Azure AD 用户能够登录 Fluxx Labs，必须将这些用户预配到 Fluxx Labs 中。 对于 Fluxx Labs，预配是一项手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 Fluxx Labs 公司站点。

2. 单击下面显示的**图标**。

    ![Fluxx Labs 配置](./media/active-directory-saas-fluxxlabs-tutorial/config6.png)

3. 在仪表板中，单击下面显示的图标打开“新建人员”卡。

    ![Fluxx Labs 配置](./media/active-directory-saas-fluxxlabs-tutorial/config4.png)

4. 在“新建人员”部分中，执行以下步骤：
    
    ![Fluxx Labs 配置](./media/active-directory-saas-fluxxlabs-tutorial/config5.png)

    a. Fluxx Labs 使用电子邮件作为 SSO 登录名的唯一标识符。 在“SSO UID”字段中填写用户的电子邮件地址，该值与他们进行 SSO 登录时使用的电子邮件地址匹配。

    b. 单击“ **保存**”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，我们通过授予 Britta Simon 访问 Fluxx Labs 的权限，以支持其使用 Azure 单一登录。

![分配用户角色][200] 

**若要将 Britta Simon 分配到 Fluxx Labs，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Fluxx Labs”。

    ![应用程序列表中的 Fluxx Labs 链接](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_fluxxlabs_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Fluxx Labs 磁贴时，应会自动登录到 Fluxx Labs 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_203.png
