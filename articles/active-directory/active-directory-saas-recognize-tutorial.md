---
title: "教程：Azure Active Directory 与 Recognize 集成 | Microsoft 文档"
description: "了解如何在 Azure Active Directory 和 Recognize 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: cfad939e-c8f4-45a0-bd25-c4eb9701acaa
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: jeedes
ms.openlocfilehash: 97d85183d0307c41a3b879d440d87a6fb0c53190
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>教程：Azure Active Directory 与 Recognize 集成

本教程介绍如何将 Recognize 与 Azure Active Directory (Azure AD) 集成。

将 Recognize 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Recognize
- 可以让用户使用其 Azure AD 帐户自动登录到 Recognize（单一登录）
- 可以在一个中心位置（即 Azure 门户）中管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Recognize 的集成，需要以下项：

- 一个 Azure AD 订阅
- 已启用 Recognize 单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可在此处获取一个月的试用版：[试用产品/服务](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Recognize
2. 配置和测试 Azure AD 单一登录

## <a name="adding-recognize-from-the-gallery"></a>从库中添加 Recognize
要配置 Recognize 与 Azure AD 的集成，需要从库中将 Recognize 添加到托管 SaaS 应用列表。

**若要从库中添加 Recognize，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

4. 在搜索框中，键入“Recognize”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_search.png)

5. 在结果面板中，选择“Recognize”，并单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
本部分将基于一个名为“Britta Simon”的测试用户使用 Recognize 配置和测试 Azure AD 单一登录。

为使单一登录能正常工作，Azure AD 需要知道 Recognize 中与 Azure AD 用户对应的用户。 换句话说，需要在 Azure AD 用户与 Recognize 中相关用户之间建立链接关系。

可通过将 Azure AD 中“用户名”的值指定为 Recognize 中“用户名”的值来建立此链接关系。

若要配置和测试 Recognize 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. [创建 Recognize 测试用户](#creating-a-recognize-test-user) - 在 Recognize 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 身份。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

本部分将在 Azure 门户中启用 Azure AD 单一登录并在 Recognize 应用程序中配置单一登录。

**若要配置 Recognize 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户的“Recognize”应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_samlbase.png)

3. 在“Recognize 域和 URL”部分中，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_url.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://recognizeapp.com/<your-domain>/saml/sso`

    b. 在“标识符”文本框中，使用以下模式键入 URL：`https://recognizeapp.com/<your-domain>`

    > [!NOTE] 
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 请联系 [Recognize 客户端支持团队](mailto:support@recognizeapp.com)获取登录 URL，可通过从 SSO 设置部分打开服务提供程序元数据 URL 获取标识符值，本教程稍后将对此进行介绍。 。 
 
4. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。

    ![配置单一登录](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_certificate.png) 

5. 单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-recognize-tutorial/tutorial_general_400.png)

6. 在“Recognize 配置”部分，单击“配置 Recognize”，打开“配置登录”窗口。 从“快速参考”部分中复制“注销 URL”、“SAML 实体 ID”和“SAML 单一登录服务 URL”。

    ![配置单一登录](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_configure.png) 

7. 在另一个 Web 浏览器窗口中，以管理员身份登录 Recognize 租户。

8. 在右上角，单击“菜单”。 转到“公司管理员”。
   
    ![在应用端配置单一登录](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_000.png)

9. 在左侧导航窗格上，单击“设置”。
   
    ![在应用端配置单一登录](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_001.png)

10. 在“SSO 设置”部分中执行以下步骤。
   
    ![在应用端配置单一登录](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_002.png)
    
    a. 对于“启用 SSO”，选择“启用”。

    b. 在“IDP 实体 ID”文本框中，粘贴从 Azure 门户复制的“SAML 实体 ID”值。
    
    c. 在“SSO 目标 URL”文本框中，粘贴从 Azure 门户复制的“SAML 单一登录服务 URL”值。
    
    d. 在“SLO 目标 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值。 
    
    e. 在记事本中打开下载的“证书(Base64)”文件，将其内容复制到剪贴板，然后将其粘贴到“证书”文本框中。
    
    f. 单击“保存设置”按钮。 

11. 在“SSO 设置”部分旁边，复制“服务提供程序元数据 url”下的 URL。
   
    ![在应用端配置单一登录](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_003.png)

12. 在空白浏览器下打开“元数据 URL 链接”以下载元数据文档。 从文件复制 EntityDescriptor 值 (entityID)，并将其粘贴到 Azure 门户的“Recognize 域和 URL”部分的“标识符”文本框。
    
    ![在应用端配置单一登录](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_004.png)

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-recognize-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-recognize-tutorial/create_aaduser_02.png) 

3. 若要打开“用户”对话框，请在对话框顶部单击“添加”。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-recognize-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-recognize-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d.单击“下一步”。 单击“创建” 。
 
### <a name="creating-a-recognize-test-user"></a>创建 Recognize 测试用户

要使 Azure AD 用户能够登录 Recognize，必须将这些用户预配到 Recognize 中。 对于 Recognize，预配是一项手动任务。

此应用不支持 SCIM 预配，但具有替代的可预配用户的用户同步。 

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录 Recognize 公司站点。

2. 在右上角，单击“菜单”。 转到“公司管理员”。

3. 在左侧导航窗格上，单击“设置”。

4. 在“用户同步”部分中，执行以下步骤。
   
   ![新建用户](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_005.png "New User")
   
   a. 对于“启用同步”，选择“启用”。
   
   b.保留“数据库类型”设置，即设置为“共享”。 对于“选择同步提供程序”，选择“Microsoft/Office 365”。
   
   c. 单击“运行用户同步”。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Recognize 的权限，允许她使用 Azure 单一登录。

![分配用户][200] 

**要将 Britta Simon 分配到 Recognize，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Recognize”。

    ![配置单一登录](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

本部分的目的是使用“访问面板”测试 Azure AD 单一登录配置。

单击访问面板中的“Recognize”磁贴时，用户应自动登录到 Recognize 应用程序。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_203.png

