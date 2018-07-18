---
title: 教程：Azure Active Directory 与 Ziflow 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Ziflow 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 84e60fa4-36fb-49c4-a642-95538c78f926
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/09/2018
ms.author: jeedes
ms.openlocfilehash: 5a50c578bd2b8a0479365f1543b9db741080f19f
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36228952"
---
# <a name="tutorial-azure-active-directory-integration-with-ziflow"></a>教程：Azure Active Directory 与 Ziflow 集成

在本教程中，了解如何将 Ziflow 与 Azure Active Directory (Azure AD) 集成。

将 Ziflow 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Ziflow。
- 可以让用户使用其 Azure AD 帐户自动登录到 Ziflow（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Ziflow 的集成，需要准备好以下各项：

- Azure AD 订阅
- 启用了 Ziflow 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Ziflow
2. 配置和测试 Azure AD 单一登录

## <a name="adding-ziflow-from-the-gallery"></a>从库中添加 Ziflow
若要配置 Ziflow 与 Azure AD 的集成，需要从库中将 Ziflow 添加到托管 SaaS 应用列表。

**若要从库中添加 Ziflow，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中，键入“Ziflow”，在结果面板中选择“Ziflow”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 Ziflow](./media/ziflow-tutorial/tutorial_ziflow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于一个名为“Britta Simon”的测试用户使用 Ziflow 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Ziflow 用户。 换句话说，需要建立 Azure AD 用户与 Ziflow 中相关用户之间的链接关系。

若要配置和测试 Ziflow 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Ziflow 测试用户](#create-a-ziflow-test-user)** - 在 Ziflow 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分，我们将在 Azure 门户中启用 Azure AD 单一登录并在 Ziflow 应用程序中配置单一登录。

**若要配置 Ziflow 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中，在 **Ziflow** 应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/ziflow-tutorial/tutorial_ziflow_samlbase.png)

3. 在“Ziflow 域和 URL”部分中，执行以下步骤：

    ![Ziflow 域和 URL 单一登录信息](./media/ziflow-tutorial/tutorial_ziflow_url.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<subdomain>.ziflow.io/#/login-sso/<Unique ID>`

    b. 在“标识符”文本框中，使用以下模式键入 URL：`urn:auth0:ziflow-production:<Unique ID>`

    > [!NOTE] 
    > 上面的值不是实际值。 本教程稍后将介绍如何使用实际值来更新“标识符”和“登录 URL”中的唯一 ID 值。 请联系 [Ziflow 支持团队](mailto:support@ziflow.com)获取登录 URL 中的子域值。
    
4. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。

    ![证书下载链接](./media/ziflow-tutorial/tutorial_ziflow_certificate.png) 

5. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/ziflow-tutorial/tutorial_general_400.png)

6. 在“Ziflow 配置”部分，单击“配置 Ziflow”打开“配置登录”窗口。 从“快速参考”部分中复制“注销 URL 和 SAML 单一登录服务 URL”。

    ![Ziflow 配置](./media/ziflow-tutorial/tutorial_ziflow_configure.png) 

7. 在另一个 Web 浏览器窗口中，以安全管理员身份登录到 Ziflow。


8. 单击右上角的头像，然后单击“管理帐户”。

    ![Ziflow 配置管理](./media/ziflow-tutorial/tutorial_ziflow_manage.png)

9. 在左上角单击“单一登录”。

    ![Ziflow 配置登录](./media/ziflow-tutorial/tutorial_ziflow_signon.png)

10. 在“单一登录”页上，执行以下步骤：

    ![Ziflow 配置单一登录](./media/ziflow-tutorial/tutorial_ziflow_page.png)

    a. 选择“SAML2.0”作为“类型”。

    b. 在“登录 URL”文本框中，粘贴从 Azure 门户复制的“SAML 单一登录服务 URL”值。

    c. 将从 Azure 门户下载的 base-64 编码证书上传到“X509 签名证书”中。

    d. 在“注销页 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值。

    e. 在“标识提供者的配置设置”部分，复制突出显示的唯一 ID 值，并将其追加到 Azure 门户上“Ziflow 域和 URL”部分中的“标识符和登录 URL”。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/ziflow-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/ziflow-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/ziflow-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/ziflow-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。
  
### <a name="create-a-ziflow-test-user"></a>创建 Ziflow 测试用户

为使 Azure AD 用户能够登录 Ziflow，必须对其进行预配才能使其登录 Ziflow。 在“Ziflow”中，预配属手动任务。

若要预配用户帐户，请执行以下步骤：

1. 以安全管理员身份登录 Ziflow。

2. 导航到顶部的“人员”。

    ![Ziflow 配置人员](./media/ziflow-tutorial/tutorial_ziflow_people.png)

3. 单击“添加”，然后单击“添加用户”。

    ![Ziflow 配置添加用户](./media/ziflow-tutorial/tutorial_ziflow_add.png)

4. 在“添加用户”弹出窗口中，执行以下步骤：

    ![Ziflow 配置添加用户](./media/ziflow-tutorial/tutorial_ziflow_adduser.png)

    a. 在“电子邮件”文本框中，输入用户的电子邮件，如 brittasimon@contoso.com。

    b. 在“名字”文本框中，输入用户的名字，例如 Britta。

    c. 在“姓氏”文本框中，输入用户的姓氏，例如 Simon。

    d. 选择 Ziflow 角色。

    e. 单击“添加 1 个用户”。

    > [!NOTE]
    > Azure Active Directory 帐户持有者将收到一封电子邮件，其中包含用于在激活帐户前确认帐户的链接。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过向 Britta Simon 授予 Ziflow 的访问权限支持其使用 Azure 单一登录。

![分配用户角色][200] 

**若要将 Britta Simon 分配到 Ziflow，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Ziflow”。

    ![应用程序列表中的 Ziflow 链接](./media/ziflow-tutorial/tutorial_ziflow_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

当在访问面板中单击 Ziflow 磁贴时，应会自动登录到 Ziflow 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ziflow-tutorial/tutorial_general_01.png
[2]: ./media/ziflow-tutorial/tutorial_general_02.png
[3]: ./media/ziflow-tutorial/tutorial_general_03.png
[4]: ./media/ziflow-tutorial/tutorial_general_04.png

[100]: ./media/ziflow-tutorial/tutorial_general_100.png

[200]: ./media/ziflow-tutorial/tutorial_general_200.png
[201]: ./media/ziflow-tutorial/tutorial_general_201.png
[202]: ./media/ziflow-tutorial/tutorial_general_202.png
[203]: ./media/ziflow-tutorial/tutorial_general_203.png

