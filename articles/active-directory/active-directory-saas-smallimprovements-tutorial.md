---
title: 教程：Azure Active Directory 与 Small Improvements 集成 | Microsoft 文档
description: 了解如何在 Azure Active Directory 和 Small Improvements 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 59c8a112-41e1-4337-9ef3-3d7029780d61
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 2f6cab5dd7c10e4036cdd2013c809142bf7ec846
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
ms.locfileid: "34352559"
---
# <a name="tutorial-azure-active-directory-integration-with-small-improvements"></a>教程：Azure Active Directory 与 Small Improvements 集成

本教程介绍如何将 Small Improvements 与 Azure Active Directory (Azure AD) 集成。

将 Small Improvements 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Small Improvements
- 可以让用户使用其 Azure AD 帐户自动登录到 Small Improvements（单一登录）
- 可以在一个中心位置（即 Azure 门户）管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Small Improvements 的集成，需备齐以下项目：

- Azure AD 订阅
- 已启用 Small Improvements 单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可在此处获取一个月的试用版：[试用产品/服务](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Small Improvements
2. 配置和测试 Azure AD 单一登录

## <a name="adding-small-improvements-from-the-gallery"></a>从库中添加 Small Improvements
要配置 Small Improvements 与 Azure AD 的集成，需要从库中将 Small Improvements 添加到托管 SaaS 应用列表。

**若要从库中添加 Small Improvements，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

4. 在搜索框中，键入“Small Improvements”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_search.png)

5. 在结果面板中，选择“Small Improvements”，然后单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，将基于一个名为“Britta Simon”的测试用户配置和测试 Small Improvements 的 Azure AD 单一登录。

为使单一登录能正常工作，Azure AD 需要知道与 Azure AD 用户相对应的 Small Improvements 用户。 换句话说，需要在 Azure AD 用户与 Small Improvements 中相关用户之间建立链接关系。

将 Azure AD 中“用户名”的值指定为 Small Improvements 中“用户名”的值来建立此链接关系。

若要配置和测试 Small Improvements 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. [创建 Small Improvements 测试用户](#creating-a-small-improvements-test-user) - 在 Small Improvements 中创建 Britta Simon 的对应用户，将其链接到用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Small Improvements 应用程序中配置单一登录。

**若要配置 Small Improvements 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中，在 Small Improvements 应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_samlbase.png)

3. 在“Small Improvements 域和 URL”部分中，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_url.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<subdomain>.small-improvements.com`

    b. 在“标识符”文本框中，使用以下模式键入 URL：`https://<subdomain>.small-improvements.com`

    > [!NOTE] 
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 请联系 [Small Improvements 客户端支持团队](mailto:support@small-improvements.com)获取这些值。 
 
4. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。

    ![配置单一登录](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_certificate.png) 

5. 单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_400.png)

6. 在“Small Improvements 配置”部分中，单击“配置 Small Improvements”，打开“配置登录”窗口。 从“快速参考”部分中复制“SAML 单一登录服务 URL”

    ![配置单一登录](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_configure.png) 

7. 在另一浏览器窗口中，以管理员身份登录 Small Improvements 公司站点。

8. 在主仪表板页中，单击左侧的“管理”按钮。
   
    ![配置单一登录](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_06.png) 

9. 在“集成”部分中，单击“SAML SSO”按钮。
   
    ![配置单一登录](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_07.png) 

10. 在“SSO 设置”页上，执行以下步骤：
   
    ![配置单一登录](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_08.png)  

    a. 在“HTTP 终结点”文本框中，粘贴从 Azure 门户复制的“SAML 单一登录服务 URL”值。

    b. 在记事本中打开下载的证书，复制其内容，并将其粘贴到“x509 证书”文本框中。 

    c. 如果想要让 SSO 和登录表单身份验证选项可供用户使用，请选中“也启用通过登录名/密码访问”选项。  

    d. 在“SAML 提示”文本框中输入用于命名“SSO 登录”按钮的相应值。  

    e. 单击“ **保存**”。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_02.png) 

3. 若要打开“用户”对话框，请在对话框顶部单击“添加”。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-smallimprovements-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b. 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d. 单击“创建”。
 
### <a name="creating-a-small-improvements-test-user"></a>创建 Small Improvements 测试用户

为使 Azure AD 用户可登录 Small Improvements，必须将用户预配到 Small Improvements 中。 使用 Small Improvements 时，需手动预配。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录 Small Improvements 公司站点。

2. 从主页转到左侧菜单，单击“管理”。

3. 从“用户管理”部分单击“用户目录”按钮。 
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_10.png) 

4. 单击“添加用户”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_11.png) 

5. 在“添加用户”对话框中，执行以下步骤： 

    ![创建 Azure AD 测试用户](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_12.png)
    
    a. 输入用户的名字，如 Britta。

    b. 输入用户的姓氏，如 Simon。

    c. 输入用户的“电子邮件地址”，如 brittasimon@contoso.com。 

    d. 还可以选择在“发送通知电子邮件”框中输入个人消息。 如果不希望发送通知，请取消选中此复选框。

    e. 单击“创建用户”。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，将通过授予 Britta Simon 访问 Small Improvements 的权限，允许她使用 Azure 单一登录。

![分配用户][200] 

**要将 Britta Simon 分配到 Small Improvements，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Small Improvements”。

    ![配置单一登录](./media/active-directory-saas-smallimprovements-tutorial/tutorial_smallimprovements_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

本部分旨在使用“访问面板”测试 Azure AD SSO 配置。  

单击访问面板中的“Small Improvements”磁贴时，用户应自动登录到 Small Improvements 应用程序。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-smallimprovements-tutorial/tutorial_general_203.png

