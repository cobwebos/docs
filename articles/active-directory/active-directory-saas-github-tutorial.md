---
title: "教程：Azure Active Directory 与 Github 的集成 | Microsoft 文档"
description: "了解如何在 Azure Active Directory 与 Github 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 4395bd95-05de-4deb-87a5-dc3bc8ac4d95
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 74b354778e775146ed6384b50b4e21cfd5c75858
ms.lasthandoff: 03/10/2017


---
# <a name="tutorial-azure-active-directory-integration-with-github"></a>教程：Azure Active Directory 与 Github 的集成

本教程介绍如何将 Github 与 Azure Active Directory (Azure AD) 集成。

将 Github 与 Azure AD 集成可提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Github
- 可以让用户使用其 Azure AD 帐户自动登录到 Github（单一登录）
- 可在一个中心位置（即 Azure 管理门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Github 的集成，需要准备好以下各项：

- Azure AD 订阅
- 启用了 Github 单一登录的订阅


> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。


测试本教程中的步骤应遵循以下建议：

- 不应使用生产环境，除非有此必要。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。


## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Github
2. 配置和测试 Azure AD 单一登录


## <a name="adding-github-from-the-gallery"></a>从库中添加 Github
若要配置 Github 与 Azure AD 的集成，需要从库中将 Github 添加到托管 SaaS 应用列表。

**若要从库中添加 Github，请执行以下步骤：**

1. 在 **[Azure 管理门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 单击对话框顶部的“添加”按钮。

    ![应用程序][3]

4. 在搜索框中，键入 **Github.com**。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-github-tutorial/tutorial_github_search02.png)

5. 在结果窗格中，选择“Github”，然后单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-github-tutorial/tutorial_github_search_result02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，基于一个名为“Britta Simon”的测试用户使用 Github 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Github 用户。 换句话说，需要建立 Azure AD 用户与 Github 中相关用户之间的链接关系。

通过将 Azure AD 中“用户名”的值分配为 Github 中“用户名”的值来建立此链接关系。

若要配置和测试 Github 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Github 测试用户](#creating-a-Github-test-user)** - 目的是在 Github 中拥有一个与 Azure AD 中的 Britta Simon 相对应的关联用户。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 管理门户中启用 Azure AD 单一登录并在 Github 应用程序中配置单一登录。

**若要配置 Github 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 管理门户的“Github”应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-github-tutorial/tutorial_github_01.png)

3. 在“Github 域和 URL”部分中，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-github-tutorial/tutorial_github_saml011.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“登录 URL”文本框中，键入 `https://github.com/orgs/<entity-id>/sso` 值

    b.保留“数据库类型”设置，即设置为“共享”。 在“标识符”文本框中，使用以下模式键入 URL：`https://github.com/orgs/<entity-id>`

    > [!NOTE] 
    > 请注意，这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 此处我们建议在“标识符”中使用字符串的唯一值。 转到“Github 管理”部分检索这些值。 

4. 在“用户属性”部分中，为“用户标识符”选择“user.mail”。

    ![配置单一登录](./media/active-directory-saas-github-tutorial/tutorial_github_attribute_new01.png)
     
5. 在“SAML 签名证书”部分中，单击“创建新证书”。

     ![配置单一登录](./media/active-directory-saas-github-tutorial/tutorial_github_03.png)

6. 在“创建新证书”对话框中，单击日历图标，然后选择“到期日期”。 然后单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-github-tutorial/tutorial_general_300.png)

7. 在“SAML 签名证书”部分中，选择“激活新证书”，然后单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-github-tutorial/tutorial_github_04.png)

8. 在弹出的“滚动更新证书”窗口中，单击“确定”。

    ![配置单一登录](./media/active-directory-saas-github-tutorial/tutorial_general_400.png)

9. 在“SAML 签名证书”部分中，单击“证书(Base64)”，然后在计算机上保存证书文件。

    ![配置单一登录](./media/active-directory-saas-github-tutorial/tutorial_github_05.png) 

10. 在“Github 配置”部分，单击“配置 Github”打开“配置登录”窗口。

    ![配置单一登录](./media/active-directory-saas-github-tutorial/tutorial_github_06.png) 

    ![配置单一登录](./media/active-directory-saas-github-tutorial/tutorial_github_07.png)

11. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Github 组织站点。

12. 导航“设置”并单击“安全性”。

    ![设置](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_03.png)

13. 选中“启用 SAML 身份验证”框，显示“单一登录”配置字段。 然后，使用单一登录 URL 值更新 Azure AD 配置中的单一登录 URL。

    ![设置](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_13.png)

14. 配置以下字段：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 **登录 URL**：输入 Azure AD 上的“配置 GitHub”部分中显示的“SAML 单一登录服务 URL”

      b.保留“数据库类型”设置，即设置为“共享”。 **颁发者**：输入 Azure AD 上的“配置 GitHub”部分中显示的“SAML 实体 ID”

       c. **公共证书**：在记事本中打开从 Azure AD 下载的证书，并复制包括“BEGIN CERTIFICATE”和“END CERTIFICATE”在内的内容

    ![设置](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_051.png)

15. 单击“测试 SAML 配置”，确认在 SSO 期间未发生验证失败错误。

    ![设置](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_06.png)

16. 单击“保存”

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 管理门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 管理门户的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-github-tutorial/create_aaduser_01.png) 

2. 转到“用户和组”，单击“所有用户”显示用户列表。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-github-tutorial/create_aaduser_02.png) 

3. 在对话框顶部单击“添加”，打开“用户”对话框。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-github-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-github-tutorial/create_aaduser_04.png) 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名称”文本框中，键入 **BrittaSimon**。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d.单击“下一步”。 单击“创建” 。 


### <a name="creating-a-github-test-user"></a>创建 Github 测试用户

要使 Azure AD 用户能够登录 Github，必须将这些用户预配到 Github 中。  
就 Github 来说，预配任务需要手动完成。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 Github 公司站点。

2. 单击“人员”。

    ![人员](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_08.png "人员")

3. 单击“邀请成员”。

    ![邀请用户](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_09.png "邀请用户")

4. 在“邀请成员”对话框页上，执行以下步骤：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“电子邮件”文本框中，键入 Britta Simon 帐户的电子邮件地址。

    ![邀请人员](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_10.png "邀请人员")
    
    b.保留“数据库类型”设置，即设置为“共享”。 单击“发送邀请”。

    ![邀请人员](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_11.png "邀请人员")

    > [!NOTE]
    > Azure Active Directory 帐户持有者将收到一封电子邮件，并且将单击其中的链接以在激活帐户前确认帐户。


### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

本部分通过授予 Britta Simon 访问 Github 的权限，允许她使用 Azure 单一登录。

![分配用户][200] 

**若要将 Britta Simon 分配到 Github，请执行以下步骤：**

1. 在 Azure 管理门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，然后单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Github.com”。

    ![配置单一登录](./media/active-directory-saas-github-tutorial/tutorial_github_search_result021.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    


### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击 Github 磁贴时，应会登录到 Github 应用程序。 一开始将以组织帐户登录，但随后需要使用个人帐户登录。


## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-github-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-github-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-github-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-github-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-github-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-github-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-github-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-github-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-github-tutorial/tutorial_general_203.png
