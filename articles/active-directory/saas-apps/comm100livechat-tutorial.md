---
title: 教程：Azure Active Directory 与 Comm100 Live Chat 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Comm100 Live Chat 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0340d7f3-ab54-49ef-b77c-62a0efd5d49c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2018
ms.author: jeedes
ms.openlocfilehash: b85162c8392e8ecdb08a3ed04ff5b9de835808a1
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2018
ms.locfileid: "42144162"
---
# <a name="tutorial-azure-active-directory-integration-with-comm100-live-chat"></a>教程：Azure Active Directory 与 Comm100 Live Chat 的集成

本教程介绍如何将 Comm100 Live Chat 与 Azure Active Directory (Azure AD) 集成。

将 Comm100 Live Chat 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Comm100 Live Chat。
- 可让用户通过其 Azure AD 帐户自动登录到 Comm100 Live Chat（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Comm100 Live Chat 的集成，需备齐以下项目：

- Azure AD 订阅
- 已启用 Comm100 Live Chat 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Comm100 Live Chat
2. 配置和测试 Azure AD 单一登录

## <a name="adding-comm100-live-chat-from-the-gallery"></a>从库中添加 Comm100 Live Chat
要配置 Comm100 Live Chat 与 Azure AD 的集成，需要从库中将 Comm100 Live Chat 添加到托管 SaaS 应用列表。

**若要从库中添加 Comm100 Live Chat，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中，键入“Comm100 Live Chat”，在结果面板中选择“Comm100 Live Chat”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 Comm100 Live Chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，我们基于一个名为“Britta Simon”的测试用户使用 Comm100 Live Chat 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Comm100 Live Chat 用户。 换句话说，需要建立 Azure AD 用户与 Comm100 Live Chat 中相关用户之间的关联关系。

若要配置和测试 Comm100 Live Chat 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Comm100 Live Chat 测试用户](#create-a-comm100-live-chat-test-user)** - 在 Comm100 Live Chat 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分，我们将在 Azure 门户中启用 Azure AD 单一登录并在 Comm100 Live Chat 应用程序中配置单一登录。

**若要配置 Comm100 Live Chat 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的“Comm100 Live Chat”应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/comm100livechat-tutorial/tutorial_comm100livechat_samlbase.png)

3. 在“Comm100 Live Chat 域和 URL”部分执行以下步骤：

    ![Comm100 Live Chat 域和 URL 单一登录信息](./media/comm100livechat-tutorial/tutorial_comm100livechat_url.png)

    在“登录 URL”文本框中，使用以下模式键入 URL： `https://<SUBDOMAIN>.comm100.com/AdminManage/LoginSSO.aspx?siteId=<SITEID>`
    
    > [!NOTE] 
    > 登录 URL 值不是实际值。 需使用实际登录 URL 更新“登录 URL”值（本教程稍后将会介绍）。

4. Comm100 Live Chat 应用程序预期 SAML 断言包含特定属性。 请为此应用程序配置以下属性。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。 以下屏幕截图显示一个示例。

    ![配置单一登录](./media/comm100livechat-tutorial/tutorial_attribute_03.png)
    
5. 在“单一登录”对话框的“用户属性”部分中，按上图所示配置 SAML 令牌属性，并执行以下步骤：
    
    |  属性名称  | 属性值 |
    | --------------- | -------------------- |    
    |   电子邮件    | user.mail |

    a. 单击“添加属性”，打开“添加属性”对话框。

    ![配置单一登录](./media/comm100livechat-tutorial/tutorial_attribute_04.png)

    ![配置单一登录](./media/comm100livechat-tutorial/tutorial_attribute_05.png)
    
    b. 在“名称”文本框中，键入为该行显示的属性名称。
    
    c. 在“值”列表中，选择为该行显示的属性值。

    d. 将“命名空间”留空。
    
    e. 单击“确定” 。

6. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。

    ![证书下载链接](./media/comm100livechat-tutorial/tutorial_comm100livechat_certificate.png) 

7. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/comm100livechat-tutorial/tutorial_general_400.png)

8. 在“Comm100 Live Chat 配置”部分，单击“配置 Comm100 Live Chat”打开“配置单一登录”窗口。 从“快速参考”部分中复制“注销 URL 和 SAML 单一登录服务 URL”。

    ![Comm100 Live Chat 配置](./media/comm100livechat-tutorial/tutorial_comm100livechat_configure.png)

9. 在另一个 Web 浏览器窗口中，以安全管理员身份登录到 Comm100 Live Chat。

10. 单击页面右上角的“我的帐户”。

    ![Comm100 Live Chat - 我的帐户](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

11. 在菜单左侧单击“安全性”，然后单击“代理单一登录”。

    ![Comm100 Live Chat - 安全性](./media/comm100livechat-tutorial/tutorial_comm100livechat_security.png)

12. 在“代理单一登录”页上，执行以下步骤：

    ![Comm100 Live Chat - 安全性](./media/comm100livechat-tutorial/tutorial_comm100livechat_singlesignon.png)

    a. 复制第一个突出显示的链接，并将其粘贴到 Azure 门户上“Comm100 Live Chat 域和 URL”部分中的“单一登录 URL”文本框内。

    b. 在“SAML SSO URL”文本框中，粘贴从 Azure 门户复制的“SAML 单一登录服务 URL”值。

    c. 在“远程注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值。

    d. 单击“选择文件”，将从 Azure 门户下载的 base-64 编码证书上传到“证书”中。

    e. 单击“保存更改”

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/comm100livechat-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/comm100livechat-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/comm100livechat-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/comm100livechat-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。
 
### <a name="create-a-comm100-live-chat-test-user"></a>创建 Comm100 Live Chat 测试用户

为了使 Azure AD 用户能够登录 Comm100 Live Chat，必须将其预配到 Comm100 Live Chat 中。 在 Comm100 Live Chat 中，预配是一项手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 以安全管理员身份登录到 Comm100 Live Chat。

2. 单击页面右上角的“我的帐户”。

    ![Comm100 Live Chat - 我的帐户](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

3. 从菜单左侧单击“代理”，然后单击“新建代理”。

    ![Comm100 Live Chat - 代理](./media/comm100livechat-tutorial/tutorial_comm100livechat_agent.png)

4. 在“新建代理”页上执行以下步骤：

    ![Comm100 Live Chat - 新建代理](./media/comm100livechat-tutorial/tutorial_comm100livechat_newagent.png)

    a. a. 在“电子邮件”文本框中，输入用户的电子邮件，例如 **Brittasimon@contoso.com**。

    b. 在“名字”文本框中，输入用户的名字，如 Britta。

    c. 在“姓氏”文本框中，输入用户的姓氏，例如 **simon**。

    d. 在“显示名称”文本框中，输入用户的显示名称，例如 **Britta Simon**。

    e. 在“密码”文本框中键入密码。

    f. 单击“ **保存**”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，我们通过授予 Britta Simon 访问 Comm100 Live Chat 的权限，使其能够使用 Azure 单一登录。

![分配用户角色][200] 

**若要将 Britta Simon 分配到 Comm100 Live Chat，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Comm100 Live Chat”。

    ![应用程序列表中的 Comm100 Live Chat 链接](./media/comm100livechat-tutorial/tutorial_comm100livechat_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“Comm100 Live Chat”磁贴时，应会自动登录到 Comm100 Live Chat 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/comm100livechat-tutorial/tutorial_general_01.png
[2]: ./media/comm100livechat-tutorial/tutorial_general_02.png
[3]: ./media/comm100livechat-tutorial/tutorial_general_03.png
[4]: ./media/comm100livechat-tutorial/tutorial_general_04.png

[100]: ./media/comm100livechat-tutorial/tutorial_general_100.png

[200]: ./media/comm100livechat-tutorial/tutorial_general_200.png
[201]: ./media/comm100livechat-tutorial/tutorial_general_201.png
[202]: ./media/comm100livechat-tutorial/tutorial_general_202.png
[203]: ./media/comm100livechat-tutorial/tutorial_general_203.png

