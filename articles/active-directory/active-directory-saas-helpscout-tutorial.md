---
title: "教程：Azure Active Directory 与 Help Scout 集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 Help Scout 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0aad9910-0bc1-4394-9f73-267cf39973ab
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2017
ms.author: jeedes
ms.openlocfilehash: fe8775bd39173b2adf1f82d32f5e851ef1c19931
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>教程：Azure Active Directory 与 Help Scout 集成

本教程介绍如何将 Help Scout 与 Azure Active Directory (Azure AD) 集成。

将 Help Scout 与 Azure AD 集成具有以下优势：

- 可以在 Azure AD 中控制谁有权访问 Help Scout。
- 可以让用户使用其 Azure AD 帐户自动登录到 Help Scout（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

要配置 Azure AD 与 Help Scout 的集成，需要以下项：

- Azure AD 订阅
- 启用了 Help Scout 单一登录的订阅

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Help Scout
2. 配置并测试 Azure AD 单一登录

## <a name="adding-help-scout-from-the-gallery"></a>从库中添加 Help Scout
要配置 Help Scout 与 Azure AD 的集成，需要从库中将 Help Scout 添加到托管 SaaS 应用列表。

**要从库中添加 Help Scout，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新建应用程序”按钮][3]

4. 在搜索框中，键入“Help Scout”，在结果面板中选择“Help Scout”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 Help Scout](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为“Britta Simon”的测试用户配置并测试 Help Scout 的 Azure AD 单一登录。

要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Help Scout 用户。 换句话说，需要建立 Azure AD 用户与 Help Scout 中相关用户之间的链接关系。

Help Scout 使用电子邮件地址作为登录名，因此，若要建立链接关系，请使用与 Azure AD 中的**用户名**相同的**电子邮件地址**。

要配置和测试 Help Scout 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Help Scout 测试用户](#create-a-help-scout-test-user)** - 在 Help Scout 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Help Scout 应用程序中配置单一登录。

**要配置 Help Scout 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的 Help Scout 应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_samlbase.png)

3. 在“Help Scout 域和 URL”部分中，如果要在“IDP”发起的模式下配置应用程序，请执行以下步骤：

    ![Help Scout 域和 URL 单一登录信息](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_url.png)

    a. “标识符”是 Help Scout 的“受众 URI (服务提供程序实体 ID)”，以 `urn:` 开头

    b. “回复 URL”是 Help Scout 的“发回 URL (断言使用者服务 URL)”，以 `https://` 开头 

    > [!NOTE] 
    > 这些 URL 中的值仅用于演示。 需要使用实际回复 URL 和标识符更新这些值。 可从“身份验证”部分下面的“单一登录”选项卡获取这些值，本教程稍后将会介绍。

4. 如果要在“SP”发起的模式下配置应用程序，请选中“显示高级 URL 设置”，并执行以下步骤：

    ![Help Scout 域和 URL 单一登录信息](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_url1.png)

    在“登录 URL”文本框中，键入 URL `https://secure.helpscout.net/members/login/`
     
5. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。

    ![证书下载链接](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_certificate.png) 

6. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/active-directory-saas-helpscout-tutorial/tutorial_general_400.png)


7. 在“Help Scout 配置”部分中，单击“配置 Help Scout”打开“配置登录”窗口。 从“快速参考”部分中复制“SAML 单一登录服务 URL”。

    ![配置单一登录](./media/active-directory-saas-helpscout-tutorial/config.png) 

8. 在另一个 Web 浏览器窗口中，以管理员身份登录 Help Scout 公司站点。

9. 登录后，在顶部菜单中单击“管理”，并从下拉菜单中选择“公司”。

    ![配置单一登录](./media/active-directory-saas-helpscout-tutorial/settings1.png) 
 
10. 在左侧菜单中选择“身份验证”。 

    ![配置单一登录](./media/active-directory-saas-helpscout-tutorial/settings2.png) 

11. 随后会转到“SAML 设置”部分，请执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-helpscout-tutorial/settings3.png) 
 
    a. 复制“发回 URL (断言使用者服务 URL)”值，并将其粘贴到 Azure 门户中 Help Scout 的“域和 URL”部分下面的“回复 URL”框中。
    
    b. 复制“受众 URI (服务提供程序实体 ID)”值，并将其粘贴到 Azure 门户中 Help Scout 的“域和 URL”部分下面的“标识符”框中。

12. 将“启用 SAML”切换为打开状态，并执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-helpscout-tutorial/settings4.png) 
 
    a. 将从 Azure 门户复制的“单一登录服务 URL”的值粘贴到“单一登录 URL”文本框中。
    
    b. 单击“上传证书”，上传已从 Azure 门户下载的**证书 (Base64)**。

    c. 在“电子邮件域”文本框中输入组织的电子邮件域，例如 `contoso.com`。 可使用逗号分隔多个域。 每当 Help Scout 用户或管理员在 [Help Scout 登录页](https://secure.helpscout.net/members/login/)上输入该特定域时，都会路由到“标识提供者”，以使用其凭据进行身份验证。

    d.单击“下一步”。 最后，可以希望用户只能通过此方法登录到 Help Scout，可将“强制 SAML 登录”切换为打开状态。 如果希望用户仍旧使用其 Help Scout 凭据登录，可将此选项切换为关闭状态。 即使启用此选项，帐户所有者也始终能够使用其帐户密码登录到 Help Scout。

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，并单击“确定”。 单击“保存” 。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/active-directory-saas-helpscout-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/active-directory-saas-helpscout-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/active-directory-saas-helpscout-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/active-directory-saas-helpscout-tutorial/create_aaduser_04.png)

    a.在“横幅徽标”下面，选择“删除上传的徽标”。 在“姓名”框中，键入“BrittaSimon”。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建” 。
 
### <a name="create-a-help-scout-test-user"></a>创建 Help Scout 测试用户

本部分的目的是在 Help Scout 中创建名为 Britta Simon 的用户。 Help Scout 支持实时预配，该功能默认处于启用状态。

此部分不存在任何操作项。 尝试访问 Help Scout 时，如果 Help Scout 中尚不存在用户，则系统会创建一个新用户。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，将通过向 Britta Simon 授予 Help Scout 的访问权限使之能够使用 Azure 单一登录。

![分配用户角色][200] 

**要将 Britta Simon 分配到 Help Scout，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Help Scout”。

    ![应用程序列表中的 Help Scout 链接](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击 Help Scout 磁贴时，应自动登录到 Help Scout 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_203.png

