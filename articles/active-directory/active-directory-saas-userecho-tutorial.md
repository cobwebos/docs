---
title: 教程：Azure Active Directory 与 UserEcho 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 UserEcho 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: bedd916b-8f69-4b50-9b8d-56f4ee3bd3ed
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 29a02a5324344330dae3f2e47a09c94343e7355e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-userecho"></a>教程：Azure Active Directory 与 UserEcho 的集成

本教程介绍如何将 UserEcho 与 Azure Active Directory (Azure AD) 集成。

将 UserEcho 与 Azure AD 集成可提供以下优势：

- 可在 Azure AD 中控制谁有权访问 UserEcho
- 可让用户通过其 Azure AD 帐户自动登录 UserEcho（单一登录）
- 可以在一个中心位置（即 Azure 门户）管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 UserEcho 的集成，需要以下项：

- Azure AD 订阅
- 已启用 UserEcho 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可在此处获取一个月的试用版：[试用产品/服务](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库添加 UserEcho
2. 配置和测试 Azure AD 单一登录

## <a name="adding-userecho-from-the-gallery"></a>从库添加 UserEcho
要配置 UserEcho 与 Azure AD 的集成，需要从库中将 UserEcho 添加到托管 SaaS 应用列表。

**若要从库添加 UserEcho，请按以下步骤操作：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

4. 在搜索框中，键入 **UserEcho**。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_search.png)

5. 在结果面板中，选择“UserEcho”，然后单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
本部分中，将基于名为“Britta Simon”的测试用户配置并测试 UserEcho 的 Azure AD 单一登录。

为使单一登录能正常工作，Azure AD 需要知道 UserEcho 中与 Azure AD 用户相对应的用户。 换句话说，需要在 Azure AD 用户与 UserEcho 中的相关用户间建立链接关系。

通过将 UserEcho 中“用户名”的值指定为 Azure AD 中“用户名”的值来建立此关联。

若要配置和测试 UserEcho 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 UserEcho 测试用户](#creating-a-userecho-test-user)** - 在 UserEcho 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 UserEcho 应用程序中配置单一登录。

**若要配置 UserEcho 的 Azure AD 单一登录，请按以下步骤操作：**

1. 在 Azure 门户中的 UserEcho 应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_samlbase.png)

3. 在“UserEcho 域和 URL”部分中，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_url.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<companyname>.userecho.com/`

    b. 在“标识符”文本框中，使用以下模式键入 URL：`https://<companyname>.userecho.com/saml/metadata/`

    > [!NOTE] 
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 请联系 [UserEcho 客户端支持团队](https://feedback.userecho.com/)获取这些值。 

4. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。

    ![配置单一登录](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_certificate.png) 

5. 单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-userecho-tutorial/tutorial_general_400.png)

6. 在“UserEcho 配置”部分，单击“配置 UserEcho”打开“配置登录”窗口。 从“快速参考”部分中复制“注销 URL 和 SAML 单一登录服务 URL”。

    ![配置单一登录](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_configure.png) 

7. 在另一浏览器窗口中，以管理员身份登录到 UserEcho 公司站点。

8. 在顶部工具栏中，单击用户名展开菜单，并单击“设置”。
   
    ![配置单一登录](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_06.png) 

9. 单击“集成”。
   
    ![配置单一登录](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_07.png) 

10. 单击“网站”，并单击“单一登录 (SAML2)”。
   
    ![配置单一登录](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_08.png) 

11. 在“单一登录 (SAML)”页上，执行以下步骤：
   
    ![配置单一登录](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_09.png)
    
    a. 至于“SAML 启用”，请选择“是”。
    
    b. 将从 Azure 门户复制的“SAML 单一登录服务 URL”粘贴到“SAML SSO URL”文本框中。
    
    c. 将从 Azure 门户复制的“注销 URL”粘贴到“远程注销 URL”文本框中。
    
    d. 在记事本中打开下载的证书，复制其内容，并将其粘贴到“X.509 证书”文本框中。
    
    e. 单击“ **保存**”。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-userecho-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-userecho-tutorial/create_aaduser_02.png) 

3. 若要打开“用户”对话框，请在对话框顶部单击“添加”。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-userecho-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-userecho-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b. 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d. 单击“创建”。
 
### <a name="creating-a-userecho-test-user"></a>创建 UserEcho 测试用户

本部分的目的是在 UserEcho 中创建名为 Britta Simon 的用户。

**若要在 UserEcho 中创建名为 Britta Simon 的用户，请执行以下步骤：**

1. 以管理员身份登录到 UserEcho 公司站点。

2. 在顶部工具栏中，单击用户名展开菜单，并单击“设置”。
   
    ![配置单一登录](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_06.png)

3. 单击“用户”，展开“用户”部分。
   
    ![配置单一登录](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_10.png)

4. 单击“用户”。
   
    ![配置单一登录](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_11.png)

5. 单击“邀请新用户”。
   
    ![配置单一登录](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_12.png)

6. 在“邀请新用户”对话框中，执行以下步骤：
   
    ![配置单一登录](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_13.png)

    a. 在“名称”文本框中，键入用户名（如 Britta Simon）。
    
    b.  在“电子邮件”文本框中，键入用户的电子邮件地址（例如 Brittasimon@contoso.com）。
    
    c. 单击“邀请”。

将向 Britta 发送邀请，通过此邀请她可开始使用 UserEcho。 

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 UserEcho 的权限，使她能够使用 Azure 单一登录。

![分配用户][200] 

**要将 Britta Simon 分配到 UserEcho，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“UserEcho”。

    ![配置单一登录](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

本部分旨在使用“访问面板”测试 Azure AD SSO 配置。  

单击“访问面板”中的 UserEcho 磁贴时，应会自动登录到 UserEcho 应用程序。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_203.png

