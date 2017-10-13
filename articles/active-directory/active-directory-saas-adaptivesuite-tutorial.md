---
title: "教程：Azure Active Directory 与 Adaptive Suite 集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 Adaptive Suite 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: jeedes
ms.openlocfilehash: 5d7ba2f4c7d814e3aaa1bf804ddc5030380ccb2d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-adaptive-suite"></a>教程：Azure Active Directory 与 Adaptive Suite 集成

在本教程中，了解如何将 Adaptive Suite 与 Azure Active Directory (Azure AD) 集成。

将 Adaptive Suite 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Adaptive Suite
- 可以让用户使用其 Azure AD 帐户自动登录到 Adaptive Suite（单一登录）
- 可以在一个中心位置（即 Azure 门户）中管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Adaptive Suite 的集成，需备齐以下项目：

- 一个 Azure AD 订阅
- 已启用 Adaptive Suite 单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库添加 Adaptive Suite
2. 配置和测试 Azure AD 单一登录

## <a name="adding-adaptive-suite-from-the-gallery"></a>从库添加 Adaptive Suite
若要配置 Adaptive Suite 与 Azure AD 的集成，需要从库中将 Adaptive Suite 添加到托管 SaaS 应用列表。

**若要从库中添加 Adaptive Suite，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

4. 在搜索框中，键入“Adaptive Suite”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_search.png)

5. 在结果面板中，选择“Adaptive Suite”，然后单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，基于一个名为“Britta Simon”的测试用户使用 Adaptive Suite 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Adaptive Suite 用户。 换句话说，需要在 Azure AD 用户与 Adaptive Suite 中相关用户之间建立链接关系。

在 Adaptive Suite 中，将 Azure AD 中“用户名”的值指定为“用户名”的值来建立此链接关系。

若要配置和测试 Adaptive Suite 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. [创建 Adaptive Suite 测试用户](#creating-an-adaptive-suite-test-user) - 在 Adaptive Suite 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Adaptive Suite 应用程序中配置单一登录。

若要配置 Adaptive Suite 的 Azure AD 单一登录，需要以下项：

1. 在 Azure 门户中，在 Adaptive Suite 应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_samlbase.png)

3. 在“Adaptive Suite 域和 URL”部分中，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_url.png)

    在“回复 URL”文本框中，使用以下模式键入 URL：`https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    >[!NOTE]
    > 可从 Adaptive Suite 的“SAML SSO 设置”页面获取此值。
    >  

4. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。

    ![配置单一登录](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_certificate.png) 

5. 单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_400.png)

6. 在“Adaptive Suite 配置”部分，单击“配置 Adaptive Suite”打开“配置登录”窗口。 从“快速参考”部分中复制“SAML 实体 ID 和 SAML 单一登录服务 URL”。

    ![配置单一登录](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_configure.png) 

7. 在其他 Web 浏览器窗口中，以管理员身份登录 Adaptive Suite 公司站点。

8. 转到“管理员”。
   
    ![管理员](./media/active-directory-saas-adaptivesuite-tutorial/IC805644.png "管理员")

9. 在“用户和角色”部分中，单击“管理 SAML SSO 设置”。
   
    ![管理 SAML SSO 设置](./media/active-directory-saas-adaptivesuite-tutorial/IC805645.png "管理 SAML SSO 设置")

10. 在“SAML SSO 设置”页上，执行以下步骤：
   
    ![SAML SSO 设置](./media/active-directory-saas-adaptivesuite-tutorial/IC805646.png "SAML SSO 设置")

    a. 在“标识提供者名称”文本框中，键入配置名称。
    
    b. 将从 Azure 门户复制的“SAML 实体 ID”值粘贴到“标识提供者实体 ID”文本框。
  
    c. 将从 Azure 门户复制的“SAML 单一登录服务 URL”值粘贴到“标识提供者 SSO URL”文本框。
  
    d. 将从 Azure 门户复制的“SAML 单一登录服务 URL”值粘贴到“自定义注销 URL”文本框。
  
    e. 若要上传所下载的证书，请单击“选择文件”。
  
    f. 针对各对象，选择以下项：
    * 对于“SAML 用户 ID”，请选择“用户的 Adaptive Insights 用户名称”。
    * 对于“SAML 用户 ID 位置”，请选择“主题 NameID 中的用户 ID”。
    * 对于“SAML NameID 格式”，请选择“电子邮件地址”。
    * 对于“启用 SAML”，请选择“允许 SAML SSO 和直接 Adaptive Insights 登录”。
    
    g. 单击“保存” 。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-adaptivesuite-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-adaptivesuite-tutorial/create_aaduser_02.png) 

3. 若要打开“用户”对话框，请在对话框顶部单击“添加”。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-adaptivesuite-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-adaptivesuite-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d.单击“下一步”。 单击“创建” 。
 
### <a name="creating-an-adaptive-suite-test-user"></a>创建 Adaptive Suite 测试用户

若要使 Azure AD 用户能够登录 Adaptive Suite，必须将其预配到 Adaptive Suite 中。  

* 就 Adaptive Suite 来说，预配任务需要手动完成。

**若要配置用户预配，请执行以下步骤：** 

1. 以管理员身份登录 **Adaptive Suite** 公司站点。
2. 转到“管理员”。
   
   ![管理员](./media/active-directory-saas-adaptivesuite-tutorial/IC805644.png "管理员")
3. 在“用户和角色”部分中，单击“添加用户”。
   
   ![添加用户](./media/active-directory-saas-adaptivesuite-tutorial/IC805648.png "添加用户")
4. 在“新建用户”部分中，执行以下步骤：
   
   ![提交](./media/active-directory-saas-adaptivesuite-tutorial/IC805649.png "提交")   

   a. 键入希望在相关文本框中预配的有效 Azure Active Directory 用户的“姓名”、“登录名”、“电子邮件”、“密码”。
  
   b. 选择“角色”。
  
   c. 单击“提交”。

>[!NOTE]
>可以使用其他任何 Adaptive Suite 用户帐户创建工具或 Adaptive Suite 提供的 API 来预配 AAD 用户帐户。
>  

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过向 Britta Simon 授予 Adaptive Suite 的访问权限使其可以使用 Azure 单一登录。

![分配用户][200] 

若要将 Britta Simon 分配到 Adaptive Suite，请执行以下步骤：

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Adaptive Suite”。

    ![配置单一登录](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

本部分用于使用“访问面板”测试 Microsoft Azure AD 单一登录配置。

单击访问面板中的“Adaptive Suite”磁贴时，用户应自动登录到 Adaptive Suite 应用程序。


## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_203.png

