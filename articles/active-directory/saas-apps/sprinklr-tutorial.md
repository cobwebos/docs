---
title: 教程：Azure Active Directory 与 Sprinklr 集成 | Microsoft 文档
description: 了解如何在 Azure Active Directory 与 Sprinklr 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: b33938a1-25a5-484c-8e75-7dc6de2d534d
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2017
ms.author: jeedes
ms.openlocfilehash: 68437cd1fe7d6aa82ff36a139a70b208a4b0abde
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36217275"
---
# <a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>教程：Azure Active Directory 与 Sprinklr 集成

在本教程中，了解如何将 Sprinklr 与 Azure Active Directory (Azure AD) 集成。

将 Sprinklr 与 Azure AD 集成可提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Sprinklr
- 可以让用户使用其 Azure AD 帐户自动登录到 Sprinklr（单一登录）
- 可以在一个中心位置（即 Azure 门户）管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Sprinklr 的集成，需要以下项：

- Azure AD 订阅
- 已启用 Sprinklr 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库添加 Sprinklr
2. 配置和测试 Azure AD 单一登录

## <a name="adding-sprinklr-from-the-gallery"></a>从库添加 Sprinklr
若要配置 Sprinklr 与 Azure AD 的集成，需要从库中将 Sprinklr 添加到托管 SaaS 应用列表。

**若要从库添加 Sprinklr，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

4. 在搜索框中，键入 **Sprinklr**。

    ![创建 Azure AD 测试用户](./media/sprinklr-tutorial/tutorial_sprinklr_search.png)

5. 在结果窗格中选择“Sprinklr”，然后单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/sprinklr-tutorial/tutorial_sprinklr_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，基于一个名为“Britta Simon”的测试用户使用 Sprinklr 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Sprinklr 用户。 换句话说，需要建立 Azure AD 用户与 Sprinklr 中相关用户之间的链接关系。

可通过将 Azure AD 中“用户名”的值指定为 Sprinklr 中“用户名”的值来建立此链接关系。

若要配置和测试 Sprinklr 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Sprinklr 测试用户](#creating-a-sprinklr-test-user)** - 在 Sprinklr 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分，将在 Azure 门户中启用 Azure AD 单一登录并在 Sprinklr 应用程序中配置单一登录。

**若要配置 Sprinklr 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的“Sprinklr”应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/sprinklr-tutorial/tutorial_sprinklr_samlbase.png)

3. 在“Sprinklr 域和 URL”部分中执行以下步骤：

    ![配置单一登录](./media/sprinklr-tutorial/tutorial_sprinklr_url.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<subdomain>.sprinklr.com`

    b. 在“标识符”文本框中，使用以下模式键入 URL：`https://<subdomain>.sprinklr.com`

    > [!NOTE] 
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新该值。 请联系 [Sprinklr 客户端支持团队](https://www.sprinklr.com/contact-us/)获取这些值。 
 
4. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。

    ![配置单一登录](./media/sprinklr-tutorial/tutorial_sprinklr_certificate.png) 

5. 单击“保存”按钮。

    ![配置单一登录](./media/sprinklr-tutorial/tutorial_general_400.png)

6. 在“Sprinklr 配置”部分中，单击“配置 Sprinklr”打开“配置登录”窗口。 从“快速参考”部分中复制“注销 URL”、“SAML 实体 ID”和“SAML 单一登录服务 URL”。

7. 在另一个 Web 浏览器窗口中，以管理员身份登录 Sprinklr 公司站点。

8. 转到“管理”\>“设置”。
   
    ![管理](./media/sprinklr-tutorial/ic782907.png "Administration")

9. 从左侧窗格转到“管理合作伙伴”\>“单一登录”。
   
    ![管理合作伙伴](./media/sprinklr-tutorial/ic782908.png "Manage Partner")

10. 单击“+添加单一登录”。
   
    ![单一登录](./media/sprinklr-tutorial/ic782909.png "Single Sign-Ons")

11. 在“单一登录”页上，执行以下步骤：
   
    ![单一登录](./media/sprinklr-tutorial/ic782910.png "Single Sign-Ons")

    a. 在“名称”文本框中，键入配置名称（例如：*WAADSSOTest*）。

    b. 选择“启用”。

    c. 选择“使用新 SSO 证书”。
             
    e. 在记事本中打开 base-64 编码的证书，将其内容复制到剪贴板，然后将其粘贴到“标识提供者证书”文本框。

    f. 将从 Azure 门户复制的“SAML 实体 ID”值粘贴到“实体 ID”文本框中。

    g. 将从 Azure 门户复制的“SAML 单一登录服务 URL”值粘贴到“标识提供者登录 URL”文本框中。

    h. 将从 Azure 门户复制的“注销 URL”值粘贴到“标识提供者注销 URL”文本框中。
     
    i. 对于“SAML 用户 ID 类型”，请选择“断言包含用户的 sprinklr.com 用户名”。

    j. 对于“SAML 用户 ID 位置”，请选择“用户 ID 位于 Subject 语句的 NameIdentifier 元素中”。

    k. 单击“ **保存**”。
       
    ![SAML](./media/sprinklr-tutorial/ic782911.png "SAML")

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/sprinklr-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/sprinklr-tutorial/create_aaduser_02.png) 

3. 若要打开“用户”对话框，请在对话框顶部单击“添加”。
 
    ![创建 Azure AD 测试用户](./media/sprinklr-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/sprinklr-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b. 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d. 单击“创建”。
 
### <a name="creating-a-sprinklr-test-user"></a>创建 Sprinklr 测试用户

1. 以管理员身份登录 Sprinklr 公司站点。

2. 转到“管理”\>“设置”。
   
    ![管理](./media/sprinklr-tutorial/ic782907.png "Administration")

3. 从左侧窗格转到“管理客户端”\>“用户”。
   
    ![设置](./media/sprinklr-tutorial/ic782914.png "设置")

4. 单击“添加用户”。
   
    ![设置](./media/sprinklr-tutorial/ic782915.png "设置")

5. 在“编辑用户”对话框中，执行以下步骤：
   
    ![编辑用户](./media/sprinklr-tutorial/ic782916.png "Edit user") 

    a. 在“电子邮件”、“名字”和“姓氏”文本框中，键入要预配的 Azure AD 用户帐户的信息。

    b. 选择“禁用密码”。

    c. 选择“语言”。

    d. 选择“用户类型”。

    e. 单击“更新”。
   
     >[!IMPORTANT]
     >必须选择“禁用密码”才能让用户通过标识提供者登录。 
     
6. 单击“角色”，并执行以下步骤：
   
    ![合作伙伴角色](./media/sprinklr-tutorial/ic782917.png "Partner Roles")

    a. 从“全局”列表中，选择“ALL\_Permissions”。  

    b. 单击“更新”。

>[!NOTE]
>可以使用任何其他 Sprinklr 用户帐户创建工具或 Sprinklr 提供的 API 来预配 Azure AD 用户帐户。 

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，通过授予 Britta Simon 访问 Sprinklr 的权限，使其能够使用 Azure 单一登录。

![分配用户][200] 

**若要将 Britta Simon 分配到 Sprinklr，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Sprinklr”。

    ![配置单一登录](./media/sprinklr-tutorial/tutorial_sprinklr_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击 Sprinklr 磁贴时，应会自动登录到 Sprinklr 应用程序。有关访问面板的详细信息，请参阅[访问面板简介](../active-directory-saas-access-panel-introduction.md)。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/sprinklr-tutorial/tutorial_general_01.png
[2]: ./media/sprinklr-tutorial/tutorial_general_02.png
[3]: ./media/sprinklr-tutorial/tutorial_general_03.png
[4]: ./media/sprinklr-tutorial/tutorial_general_04.png

[100]: ./media/sprinklr-tutorial/tutorial_general_100.png

[200]: ./media/sprinklr-tutorial/tutorial_general_200.png
[201]: ./media/sprinklr-tutorial/tutorial_general_201.png
[202]: ./media/sprinklr-tutorial/tutorial_general_202.png
[203]: ./media/sprinklr-tutorial/tutorial_general_203.png

