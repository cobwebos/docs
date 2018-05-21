---
title: 教程：Azure Active Directory 与 Wdesk 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Wdesk 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 06900a91-a326-4663-8ba6-69ae741a536e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: jeedes
ms.openlocfilehash: c55b9c32ccf6712da69c869a6f44c71e1c209627
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-wdesk"></a>教程：Azure Active Directory 与 Wdesk 集成

在本教程中，了解如何将 Wdesk 与 Azure Active Directory (Azure AD) 集成。

将 Wdesk 与 Azure AD 集成有以下优势：

- 可在 Azure AD 中控制谁有权访问 Wdesk
- 可以让用户使用其 Azure AD 帐户自动登录到 Wdesk（单一登录）
- 可以在一个中心位置（即 Azure 门户）管理帐户

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅。 [什么是使用 Azure Active Directory 的应用程序访问和单一登录](manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Wdesk 的集成，需要以下项目：

- Azure AD 订阅
- 启用的订阅上的 Wdesk 单一登录

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Wdesk
2. 配置和测试 Azure AD 单一登录

## <a name="adding-wdesk-from-the-gallery"></a>从库中添加 Wdesk
若要配置 Wdesk 与 Azure AD 的集成，需要从库中将 Wdesk 添加到托管 SaaS 应用列表。

若要从库中添加 Wdesk，请执行以下步骤：

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

4. 在搜索框中，键入“Wdesk”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_search.png)

5. 在结果窗格中，选择“Wdesk”，然后单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，基于一个名为“Britta Simon”的测试用户使用 Wdesk 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Wdesk 用户。 换句话说，需要建立 Azure AD 用户与 Wdesk 中相关用户之间的链接关系。

通过将 Azure AD 中“用户名”的值分配为 Wdesk 中“用户名”的值来建立此链接关系。

若要配置和测试 Wdesk 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. [创建 Wdesk 测试用户](#creating-a-wdesk-test-user) - 在 Wdesk 中创建 Britta Simon 的对应用户，将其链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Wdesk 应用程序中配置单一登录。

若要配置 Wdesk 的 Azure AD 单一登录，请执行以下步骤：

1. 在 Azure 门户中的 Wdesk 应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_samlbase.png)

3. 在“BWdesk 域和 URL”部分，如果要在“IDP 发起的模式”下配置应用程序，请执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_url.png)

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`

4. 选中“显示高级 URL 设置”。 如果要在“SP”发起的模式下配置应用程序，请执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_url1.png)

    在“登录 URL”文本框中，使用以下模式键入 URL：`https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`
     
    > [!NOTE] 
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 配置 SSO 时，你会从 WDesk 门户获得这些值。 
  
5. 在“SAML 签名证书”部分中，单击“元数据 XML”，并在计算机上保存元数据文件。

    ![配置单一登录](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_certificate.png) 

6. 单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-wdesk-tutorial/tutorial_general_400.png)
    
7. 在另一个 Web 浏览器窗口中，以安全管理员身份登录到 Wdesk。

8. 在左下角，单击“管理员”，然后选择“管理员帐户”：
 
     ![配置单一登录](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

9. 在 Wdesk 管理员中，导航到“安全性”、“SAML”、“SAML 设置” > ：

    ![配置单一登录](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig2.png)

10. 在“常规设置”下，选中“启用 SAML 单一登录”：

    ![配置单一登录](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig3.png)

11. 在“服务提供程序详细信息”下，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig4.png)

      a. 复制登录 URL 并将其粘贴到 Azure 门户的“登录 URL”文本框。
   
      b. 复制元数据 URL 并将其粘贴到 Azure 门户的“标识符”文本框。
       
      c. 复制使用者 URL 并将其粘贴到 Azure 门户的“回复 URL”文本框。
   
      d. 单击 Azure 门户的“保存”以保存更改。      

12. 单击“配置 IdP 设置”以打开“编辑 IdP 设置”对话框。 单击“选择文件”以查找从 Azure 门户保存的 Metadata.xml 文件，然后将其上传。
    
    ![配置单一登录](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig5.png)
  
13. 单击“保存更改”。

    ![配置单一登录](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfigsavebutton.png)

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-wdesk-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-wdesk-tutorial/create_aaduser_02.png) 

3. 若要打开“用户”对话框，请在对话框顶部单击“添加”。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-wdesk-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-wdesk-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b. 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d. 单击“创建”。
 
### <a name="creating-a-wdesk-test-user"></a>创建 Wdesk 测试用户

为了使 Azure AD 用户能够登录 Wdesk，必须对其进行预配才能使其登录 Wdesk。 在“Wdesk”中，预配属手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 以安全管理员身份登录 Wdesk。
2. 导航到“管理员” > “管理员帐户”。

     ![配置单一登录](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. 单击“人员”下的“成员”。

4. 现在请单击“添加成员”以打开“添加成员”对话框。 
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-wdesk-tutorial/createuser1.png)  

5. 在“用户”文本框中，输入用户的用户名，如 brittasimon@contoso.com然后单击“继续”按钮。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-wdesk-tutorial/createuser3.png)

6.  输入详细信息，如下所示：
  
    ![创建 Azure AD 测试用户](./media/active-directory-saas-wdesk-tutorial/createuser4.png)
 
    a. 在“电子邮件”文本框中，输入用户的电子邮件，如 brittasimon@contoso.com。

    b. 在“名字”文本框中，输入用户的名字，如 Britta。

    c. 在“姓氏”文本框中，输入用户的名字，如 Simon。

7. 单击“保存成员”按钮。  

    ![创建 Azure AD 测试用户](./media/active-directory-saas-wdesk-tutorial/createuser5.png)

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Wdesk 的权限，允许她使用 Azure 单一登录。

![分配用户][200] 

若要将 Britta Simon 分配到 Wdesk，请执行以下步骤：

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Wdesk”。

    ![配置单一登录](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

当在访问面板中单击 Wdesk 磁贴时，应该会自动登录 Wdesk 应用程序。
有关访问面板的详细信息，请参阅 [introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。


## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_203.png

