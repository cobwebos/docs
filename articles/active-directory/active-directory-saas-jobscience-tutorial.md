---
title: 教程：Azure Active Directory 与 Jobscience 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Jobscience 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 77282dcc-bbe2-4728-953d-adb4ab6a713b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: cf6dccf02fa56b90ea4737e1d1c7b9a5e9d1d4af
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
ms.locfileid: "34346224"
---
# <a name="tutorial-azure-active-directory-integration-with-jobscience"></a>教程：Azure Active Directory 与 Jobscience 集成

本教程介绍如何将 Jobscience 与 Azure Active Directory (Azure AD) 集成。

将 Jobscience 与 Azure AD 集成具有以下优势：

- 可在 Azure AD 中控制谁有权访问 Jobscience
- 可以让用户使用其 Azure AD 帐户自动登录到 Jobscience（单一登录）
- 可以在一个中心位置（即 Azure 门户）管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Jobscience 的集成，需要以下项：

- Azure AD 订阅
- 已启用 Jobscience 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可在此处获取一个月的试用版：[试用产品/服务](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Jobscience
2. 配置和测试 Azure AD 单一登录

## <a name="adding-jobscience-from-the-gallery"></a>从库中添加 Jobscience
若要配置 Jobscience 与 Azure AD 的集成，需要从库中将 Jobscience 添加到托管 SaaS 应用列表。

若要从库中添加 Jobscience，请执行以下步骤：

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

4. 在搜索框中，键入“jobscience”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-jobscience-tutorial/tutorial_jobscience_search.png)

5. 在结果面板中，选择“Jobscience”，然后单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-jobscience-tutorial/tutorial_jobscience_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，基于一个名为“Britta Simon”的测试用户使用 Jobscience 配置和测试 Azure AD 单一登录。

为使单一登录能正常工作，Azure AD 需要知道与 Azure AD 用户相对应的 Jobscience 用户。 换句话说，需要建立 Azure AD 用户与 Jobscience 中相关用户之间的链接关系。

可通过将 Azure AD 中“用户名”的值指定为 Jobscience 中“用户名”的值来建立此链接关系。

若要配置和测试 Jobscience 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. [创建 Jobscience 测试用户](#creating-a-jobscience-test-user) - 在 Jobscience 中创建 Britta Simon 的对应用户，将其链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Jobscience 应用程序中配置单一登录。

若要配置 Jobscience 的 Azure AD 单一登录，请执行以下步骤：

1. 在 Azure 门户中的“Jobscience”应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-jobscience-tutorial/tutorial_jobscience_samlbase.png)

3. 在“Jobscience 域和 URL”部分中，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-jobscience-tutorial/tutorial_jobscience_url.png)

    在“登录 URL”文本框中，使用以下模式键入 URL：`http://<company name>.my.salesforce.com`
    
    > [!NOTE] 
    > 此值不是真实值。 使用实际登录 URL 更新此值。 通过 [Jobscience 客户端支持团队](https://www.jobscience.com/support)获取此值，或从 SSO 配置文件可以创建将在教程后面部分进行说明的内容。 
 
4. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。

    ![配置单一登录](./media/active-directory-saas-jobscience-tutorial/tutorial_jobscience_certificate.png) 

5. 单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-jobscience-tutorial/tutorial_general_400.png)

6. 在“Jobscience 配置”部分，单击“配置 Jobscience”打开“配置登录”窗口。 从“快速参考”部分中复制“注销 URL”、“SAML 实体 ID”和“SAML 单一登录服务 URL”。

    ![配置单一登录](./media/active-directory-saas-jobscience-tutorial/tutorial_jobscience_configure.png) 

7. 以管理员身份登录到 Jobscience 公司站点。

8. 转到“设置”。
   
   ![设置](./media/active-directory-saas-jobscience-tutorial/IC784358.png "设置")

9. 在左侧导航窗格中的“管理”部分中，单击“域管理”以展开相关部分，并单击“我的域”，打开“我的域”页。 
   
   ![我的域](./media/active-directory-saas-jobscience-tutorial/ic767825.png "我的域")

10. 要验证域是否已正确设置，请确保它在“步骤 4 部署到用户”中，并复查“我的域设置”。

    ![部署到用户的域](./media/active-directory-saas-jobscience-tutorial/ic784377.png "部署到用户的域")

11. 在 Jobscience 公司站点上，单击“安全控制”，并单击“单一登录设置”。
    
    ![安全性控制](./media/active-directory-saas-jobscience-tutorial/ic784364.png "安全性控制")

12. 在“单一登录设置”部分中，执行以下步骤：
    
    ![单一登录设置](./media/active-directory-saas-jobscience-tutorial/ic781026.png "Single Sign-On Settings")
    
    a. 选择“已启用 SAML”。

    b. 单击“新建” 。

13. 在“SAML 单一登录设置编辑”对话框中，执行以下步骤：
    
    ![SAML 单一登录设置](./media/active-directory-saas-jobscience-tutorial/ic784365.png "SAML Single Sign-On Settings")
    
    a. 在“名称”文本框中，键入配置名称。

    b. 将从 Azure 门户复制的“SAML 实体 ID”的值粘贴到“颁发者”文本框中。

    c. 在“实体 Id”文本框中，键入 `https://salesforce-jobscience.com`

    d. 单击“浏览”以上传 Azure AD 证书。

    e. 对于“SAML 标识类型”，选择“断言包含用户对象的联合 ID”。

    f. 对于“SAML 标识位置”，选择“标识位于 Subject 语句的 NameIdentifier 元素中”。

    g. 在“标识提供者登录 URL”文本框中，粘贴从 Azure 门户复制的“SAML 单一登录服务 URL”值。

    h. 在“标识提供者注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值。

    i. 单击“ **保存**”。

14. 在左侧导航窗格中的“管理”部分中，单击“域管理”以展开相关部分，并单击“我的域”，打开“我的域”页。 
    
    ![我的域](./media/active-directory-saas-jobscience-tutorial/ic767825.png "我的域")

15. 在“我的域”页上的“登录页品牌打造”部分中，单击“编辑”。
    
    ![登录页品牌打造](./media/active-directory-saas-jobscience-tutorial/ic767826.png "登录页品牌打造")

16. 在“登录页品牌打造”页上的“身份验证服务”部分中，会显示 **SAML SSO 设置**的名称。 选择它，并单击“保存”。
    
    ![登录页品牌打造](./media/active-directory-saas-jobscience-tutorial/ic784366.png "登录页品牌打造")

17. 若要获取 SP 启动的单一登录的登录 URL，请单击“安全控制”菜单部分中的“单一登录设置”。

    ![安全性控制](./media/active-directory-saas-jobscience-tutorial/ic784368.png "安全性控制")
    
    单击上一步中创建的 SSO 配置文件。 此页显示你公司的单一登录 URL（例如，[https://companyname.my.salesforce.com?so=companyid](https://companyname.my.salesforce.com?so=companyid)）。    

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-jobscience-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-jobscience-tutorial/create_aaduser_02.png) 

3. 若要打开“用户”对话框，请在对话框顶部单击“添加”。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-jobscience-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-jobscience-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b. 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d. 单击“创建”。
 
### <a name="creating-a-jobscience-test-user"></a>创建 Jobscience 测试用户

要使 Azure AD 用户能够登录 Jobscience，必须将这些用户预配到 Jobscience 中。 对于 Jobscience，预配是一项手动任务。

>[!NOTE]
>可以使用 Jobscience 提供的任何其他 Jobscience 用户帐户创建工具或 API 来预配 Azure Active Directory 用户帐户。
>  
        
**若要配置用户设置，请执行以下步骤：**

1. 以管理员身份登录到 **Jobscience** 公司站点。

2. 转到“设置”。
   
   ![设置](./media/active-directory-saas-jobscience-tutorial/ic784358.png "设置")
3. 转到“管理用户”\>“用户”。
   
   ![用户](./media/active-directory-saas-jobscience-tutorial/ic784369.png "用户")
4. 单击“新建用户”。
   
   ![所有用户](./media/active-directory-saas-jobscience-tutorial/ic784370.png "所有用户")
5. 在“编辑用户”对话框中，执行以下步骤：
   
   ![用户编辑](./media/active-directory-saas-jobscience-tutorial/ic784371.png "用户编辑")
   
   a. 在“名”文本框中键入用户的名（如 Britta）。
   
   b. 在“姓”文本框中键入用户的姓（如 Simon）。
   
   c. 在“别名”文本框中键入用户的别名（如 brittas）。

   d. 在“电子邮件”文本框中，键入用户的电子邮件地址（例如 Brittasimon@contoso.com）。

   e. 在“用户名”文本框中，键入用户的用户名（例如 Brittasimon@contoso.com）。

   f. 在“昵称”文本框中，键入用户的昵称（例如 Simon）。

   g. 单击“ **保存**”。

    
> [!NOTE]
> Azure Active Directory 帐户持有者将收到一封电子邮件，其中包含用于在激活帐户前确认帐户的链接。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Jobscience 的权限，允许使用 Azure 单一登录。

![分配用户][200] 

若要将 Britta Simon 分配到 Jobscience，请执行以下步骤：

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Jobscience”。

    ![配置单一登录](./media/active-directory-saas-jobscience-tutorial/tutorial_jobscience_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Jobscience 磁贴时，应自动登录到 Jobscience 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-jobscience-tutorial/tutorial_general_203.png

