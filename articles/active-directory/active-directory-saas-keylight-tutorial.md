---
title: "教程：Azure Active Directory 与 LockPath Keylight 集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 LockPath Keylight 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 234a32f1-9f56-4650-9e31-7b38ad734b1a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.openlocfilehash: e64a966f24411818abc4cc4ab29a428b5577d012
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-lockpath-keylight"></a>教程：Azure Active Directory 与 LockPath Keylight 集成

本教程介绍如何将 LockPath Keylight 与 Azure Active Directory (Azure AD) 集成。

将 LockPath Keylight 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 LockPath Keylight
- 可让用户使用其 Azure AD 帐户自动登录到 LockPath Keylight（单一登录）
- 可以在一个中心位置（即 Azure 门户）中管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 LockPath Keylight 的集成，需要以下项：

- 一个 Azure AD 订阅
- 已启用 LockPath Keylight 单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 LockPath Keylight
2. 配置和测试 Azure AD 单一登录

## <a name="adding-lockpath-keylight-from-the-gallery"></a>从库中添加 LockPath Keylight
要配置 LockPath Keylight 与 Azure AD 的集成，需要从库中将 LockPath Keylight 添加到托管 SaaS 应用列表。

若要从库中添加 LockPath Keylight，请执行以下步骤：

1. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

4. 在搜索框中，键入“LockPath Keylight”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_search.png)

5. 在结果面板中，选择“LockPath Keylight”，并单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，基于一个名为“Britta Simon”的测试用户使用 LockPath Keylight 配置和测试 Azure AD 单一登录。

为使单一登录能正常工作，Azure AD 需要知道 LockPath Keylight 中与 Azure AD 用户对应的用户。 换句话说，需要在 Azure AD 用户与 LockPath Keylight 中的相关用户之间建立链接关系。

可通过将 Azure AD 中“用户名”的值指定为 LockPath Keylight 中“用户名”的值来建立此链接关系。

若要使用 LockPath Keylight 配置和测试 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. [创建 LockPath Keylight 测试用户](#creating-a-lockpath-keylight-test-user) - 在 LockPath Keylight 中创建 Britta Simon 的对应用户，将其链接到用户的 Azure AD 身份。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

本部分介绍如何在 Azure 门户中启用 Azure AD 单一登录，并在 LockPath Keylight 应用程序中配置单一登录。

若要使用 LockPath Keylight 配置 Azure AD 单一登录，请执行以下步骤：

1. 在 Azure 门户中的“LockPath Keylight”应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_samlbase.png)

3. 在“LockPath Keylight 域和 URL”部分中，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_url.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<company name>.keylightgrc.com/`

    b. 在“标识符”文本框中，使用以下模式键入 URL：`https://<company name>.keylightgrc.com`

    c. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<company name>.keylightgrc.com/Login.aspx`
    
    > [!NOTE] 
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 请联系 [LockPath Keylight 客户端支持团队](https://www.lockpath.com/contact/)获取这些值。 

4. 在“SAML 签名证书”部分中，单击“证书(原始)”，然后在计算机上保存证书文件。

    ![配置单一登录](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_certificate.png) 

5. 单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-keylight-tutorial/tutorial_general_400.png)
    
6. 在“LockPath Keylight 配置”部分，单击“配置 LockPath Keylight”，打开“配置登录”窗口。 从“快速参考”部分中复制“注销 URL 和 SAML 单一登录服务 URL”。

    ![配置单一登录](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_configure.png) 

7. 若要在 LockPath Keylight 中启用 SSO，请执行以下步骤：
   
    a. 以管理员身份登录到 LockPath Keylight 帐户。
    
    b. 在顶部菜单中，单击“人员”，并选择“Keylight 设置”。
   
    ![配置单一登录](./media/active-directory-saas-keylight-tutorial/401.png) 

    c. 在左侧树视图中，单击“SAML”。
   
    ![配置单一登录](./media/active-directory-saas-keylight-tutorial/402.png) 

    d.单击“下一步”。 在“SAML 设置”对话框中，单击“编辑”。
   
    ![配置单一登录](./media/active-directory-saas-keylight-tutorial/404.png) 

8. 在“编辑 SAML 设置”对话框页上，执行以下步骤：
   
    ![配置单一登录](./media/active-directory-saas-keylight-tutorial/405.png) 
   
    a. 将“SAML 身份验证”设置为“活动”。

    b. 在“标识提供者登录 URL”文本框中，粘贴从 Azure 门户复制的“SAML 单一登录服务 URL”值。

    c. 在“标识提供者注销 URL”文本框中，粘贴从 Azure 门户复制的“单一注销服务 URL”值。

    d. 单击“选择文件”，选择已下载的 LockPath Keylight 证书，并单击“打开”上传证书。

    e. 将“SAML 用户 ID 位置”设置为 subject 语句的 NameIdentifier 元素。
    
    f. 使用以下模式提供“Keylight 服务提供商”：https://&lt;CompanyName&gt;.keylightgrc.com。
    
    g. 将“自动预配用户”设置为“活动”。

    h. 将“自动预配帐户类型”设置为“全部用户”。

    i. 设置“自动预配安全角色”，选择“使用 SAML 的标准用户”。
    
    j. 设置“自动预配安全配置”，选择“标准用户配置”。
     
    k. 在“电子邮件属性”文本框中，键入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`。
    
    l. 在“名字属性”文本框中，键入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`。
    
    m. 在“姓氏属性”文本框中，键入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`。
    
    n. 单击“保存” 。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-keylight-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-keylight-tutorial/create_aaduser_02.png) 

3. 若要打开“用户”对话框，请在对话框顶部单击“添加”。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-keylight-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-keylight-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d.单击“下一步”。 单击“创建” 。
 
### <a name="creating-a-lockpath-keylight-test-user"></a>创建 LockPath Keylight 测试用户

本部分将在 LockPath Keylight 中创建一个名为“Britta Simon”的用户。 LockPath Keylight 支持在默认情况下启用的实时预配。

此部分不存在任何操作项。 访问 LockPath Keylight 时，如果该用户尚不存在，则将创建新用户。 

>[!NOTE]
>如果需要手动创建用户，则需要联系 [LockPath Keylight 客户端支持团队](https://www.lockpath.com/contact/)。 

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 LockPath Keylight 的权限，允许她使用 Azure 单一登录。

![分配用户][200] 

要将 Britta Simon 分配到 LockPath Keylight，请执行以下步骤：

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“LockPath Keylight”。

    ![配置单一登录](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击 LockPath Keylight 磁贴就会自动登录到 LockPath Keylight 应用程序。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_203.png

