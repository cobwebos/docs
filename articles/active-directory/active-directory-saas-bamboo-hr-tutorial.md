---
title: "教程：Azure Active Directory 与 BambooHR 集成 | Microsoft 文档"
description: "了解如何在 Azure Active Directory 和 BambooHR 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f826b5d2-9c64-47df-bbbf-0adf9eb0fa71
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2017
ms.author: jeedes
ms.openlocfilehash: 37a560ed6ea507084a5bbb7b022d2905746d2ba1
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-bamboohr"></a>教程：Azure Active Directory 与 BambooHR 集成

在本教程中，了解如何将 BambooHR 与 Azure Active Directory (Azure AD) 进行集成。

将 BambooHR 与 Azure AD 集成可提供以下优势：

- 可在 Azure AD 中控制谁有权限访问 BambooHR
- 可以让用户使用其 Azure AD 帐户自动登录到 BambooHR（单一登录）
- 可以在一个中心位置（即 Azure 门户）中管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 BambooHR 的集成，需要以下项：

- 一个 Azure AD 订阅
- 已启用 BambooHR 单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 BambooHR
2. 配置和测试 Azure AD 单一登录

## <a name="adding-bamboohr-from-the-gallery"></a>从库中添加 BambooHR
若要配置 BambooHR 与 Azure AD 的集成，需要从库中将 BambooHR 添加到托管 SaaS 应用列表。

若要从库中添加 BambooHR，请执行以下步骤：

1. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

4. 在搜索框中，键入 BambooHR。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_search.png)

5. 在“结果”窗格中，选择“BambooHR”，再单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，根据名为“Britta Simon”的测试用户的指示配置和测试 BambooHR 的 Azure AD 单一登录。

若要使用单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 BambooHR 用户。 换句话说，需要建立 Azure AD 用户与 BambooHR 中相关用户之间的链接关系。

可通过将 Azure AD 中“用户名”的值指定为 BambooHR 中“用户名”的值来建立此链接关系。

若要配置和测试 BambooHR 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 BambooHR 测试用户](#creating-a-bamboohr-test-user)** - 在 BambooHR 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 BambooHR 应用程序中配置单一登录。

若要配置 BambooHR 的 Azure AD 单一登录，请执行以下步骤：

1. 在 Azure 门户中的“BambooHR”应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_samlbase.png)

3. 在“BambooHR 域和 URL”部分中，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_url.png)

    在“登录 URL”文本框中，使用以下模式键入 URL：`https://<company>.bamboohr.com`
    
    > [!NOTE] 
    > 此值不是真实值。 请使用实际登录 URL 更新此值。 若要获取此值，请与 [BambooHR 客户端支持团队](https://www.bamboohr.com/contact.php)联系。 
 
4. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。

    ![配置单一登录](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_certificate.png) 

5. 单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_400.png)

6. 在“BambooHR 配置”部分，单击“配置 BambooHR”，以打开“配置登录”窗口。 从“快速参考”部分中复制“SAML 单一登录服务 URL”

    ![配置单一登录](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_configure.png) 

6. 在另一个 Web 浏览器窗口中，以管理员身份登录 BambooHR 公司站点。

7. 在主页上执行以下步骤：
   
    ![单一登录](./media/active-directory-saas-bamboo-hr-tutorial/ic796691.png "单一登录")   

    a. 单击“应用”。
   
    b. 在左侧的应用菜单中，单击“单一登录”。
   
    c. 单击“SAML 单一登录”。

8. 在“SAML 单一登录”部分中，执行以下步骤：
   
    ![SAML 单一登录](./media/active-directory-saas-bamboo-hr-tutorial/ic796692.png "SAML 单一登录")
   
    a. 将“SAML 单一登录服务 URL”值粘贴到“SSO 登录 URL”文本框。
      
    b. 在记事本中打开从 Azure 门户下载的 base-64 编码的证书，将其内容复制到剪贴板，再将其粘贴到“X.509 证书”文本框中
   
    c. 单击“保存” 。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_02.png) 

3. 若要打开“用户”对话框，请在对话框顶部单击“添加”。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-bamboo-hr-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d.单击“下一步”。 单击“创建”。
 
### <a name="creating-a-bamboohr-test-user"></a>创建 BambooHR 测试用户

为了使 Azure AD 用户能够登录到 BambooHR，必须将其预配到 BambooHR 中。  

就 BambooHR 来说，预配任务需要手动完成。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 **BambooHR** 站点。

2. 在顶部工具栏中，单击“设置”。
   
    ![设置](./media/active-directory-saas-bamboo-hr-tutorial/ic796694.png "设置")

3. 单击“概览”。

4. 在左侧导航窗格中，转到“安全”**\>**“用户”。

5. 在相关文本框中键入要预配的有效 AAD 帐户的用户名、密码和电子邮件地址。

6. 单击“保存” 。
        
>[!NOTE]
>可使用其他任何 BambooHR 用户帐户创建工具或 BambooHR 提供的 API 预配 AAD 用户帐户。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 BambooHR 的权限，以支持其使用 Azure 单一登录。

![分配用户][200] 

若要将 Britta Simon 分配到 BambooHR，请执行以下步骤：

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“BambooHR”。

    ![配置单一登录](./media/active-directory-saas-bamboo-hr-tutorial/tutorial_bamboohr_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，将使用访问面板测试 Azure AD 单一登录配置。

当在访问面板中单击 BambooHR 磁贴时，应该会自动登录 BambooHR 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bamboo-hr-tutorial/tutorial_general_203.png

