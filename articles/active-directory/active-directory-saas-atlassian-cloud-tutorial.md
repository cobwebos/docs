---
title: "教程：Azure Active Directory 与 Atlassian Cloud 的集成 | Microsoft 文档"
description: "了解如何在 Azure Active Directory 与 Atlassian Cloud 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
editor: na
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: da9e5f015f93090f4efb00f6c3af07ba2f5503bc
ms.contentlocale: zh-cn
ms.lasthandoff: 05/26/2017


---
# <a name="tutorial-azure-active-directory-integration-with-atlassian-cloud"></a>教程：Azure Active Directory 与 Atlassian Cloud 的集成

本教程介绍如何将 Atlassian Cloud 与 Azure Active Directory (Azure AD) 集成。

将 Atlassian Cloud 与 Azure AD 集成可提供以下优势：

- 可以在 Azure AD 中控制谁有权访问 Atlassian Cloud
- 可以让用户使用其 Azure AD 帐户自动登录到 Atlassian Cloud（单一登录）
- 可在一个中心位置（即 Azure 经典门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Atlassian Cloud 的集成，需要具有以下项：

- Azure AD 订阅
- 启用了 Atlassian Cloud 单一登录的订阅


>[!NOTE] 
>不建议使用生产环境测试本教程中的步骤。

测试本教程中的步骤应遵循以下建议：

- 不应使用生产环境，除非有此必要。
- 如果没有 Azure AD 试用环境，可以获取[一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。


## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。

本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Atlassian Cloud
2. 配置和测试 Azure AD SSO


## <a name="add-atlassian-cloud-from-the-gallery"></a>从库中添加 Atlassian Cloud
若要配置 Atlassian Cloud 与 Azure AD 的集成，需要从库中将 Atlassian Cloud 添加到托管 SaaS 应用列表。

**若要从库中添加 Atlassian Cloud，请执行以下步骤：**

1. 在 **Azure 经典门户**的左侧导航窗格上，单击“Active Directory”。

    ![Active Directory][1]
2. 从“目录”列表中，选择要为其启用目录集成的目录。

3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。

    ![应用程序][2]

4. 在页面底部单击“添加”。

    ![应用程序][3]

5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。

    ![应用程序][4]

6. 在搜索框中，键入 **Atlassian Cloud**。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_01.png)

7. 在结果窗格中，选择“Atlassian Cloud”，然后单击“完成”添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_02.png)

##  <a name="configure-and-test-azure-ad-sso"></a>配置和测试 Azure AD SSO
在本部分中，将基于名为“Britta Simon”的测试用户配置并测试 Atlassian Cloud 的 Azure AD 单一登录。

若要运行 SSO，Azure AD 需要知道与 Azure AD 用户相对应的 Atlassian Cloud 用户。 换句话说，需要在 Azure AD 用户与 Atlassian Cloud 中的相关用户之间建立链接关系。

可以通过将 Azure AD 中“用户名”的值分配为 Atlassian Cloud 中“用户名”的值来建立此链接关系。

若要配置并测试 Atlassian Cloud 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Atlassian Cloud 测试用户](#creating-Atlassian-cloud-test-user)** - 在 Atlassian Cloud 中创建 Britta Simon 的对应用户，将其链接到其 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

在本部分中，将在经典门户中启用 Azure AD 单一登录并在 Atlassian Cloud 应用程序中配置 SSO。


**若要配置 Atlassian Cloud 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 经典门户中，在 **Atlassian Cloud** 应用程序集成页上，单击“配置单一登录”，以打开“配置单一登录”对话框。
     
    ![配置单一登录][6] 

2. 在“你希望用户如何登录到 Atlassian Cloud”页上，选择“Azure AD 单一登录”，然后单击“下一步”。

    ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_03.png) 

3. 在“配置应用设置”对话框页上，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_04.png) 
 1. 在“登录 URL”文本框中，使用以下模式键入用户用来登录 Atlassian Cloud 应用程序的 URL：`https://<instancename>.atlassian.net`    
 2. 在“标识符”文本框中，使用以下模式键入 URL：`https://id.atlassian.com/login`

    >[!NOTE] 
    >可以从“Atlassian Cloud SAML 配置”屏幕获取“标识符”的确切值。
    >

 3. 单击“下一步”。
 
4. 在“在 Atlassian Cloud 处配置单一登录”页上，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_05.png)
 1. 单击“下载证书”，然后将文件保存在计算机上。
 2. 单击“下一步”。

5. 若要为应用程序配置 SSO，请使用管理员权限登录到 Atlassian 门户。

6. 在左侧导航窗格的“身份验证”部分中单击“域”。

    ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_06.png)
 1. 在文本框中键入域名，然后单击“添加域”。
        
    ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_07.png)
 2. 若要验证该域，请单击“验证”。 

    ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_08.png) 
  3. 下载域验证 html 文件，将其上传到域网站的根文件夹，然后单击“验证域”。
    
    ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_09.png)
  4. 验证域后，“状态”字段的值将变为“已验证”。

    ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_10.png)

7. 在左侧导航栏中，单击“SAML”。
 
    ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_11.png)

8. 创建新 SAML 配置并添加标识提供者配置。
  1. 从 Azure AD 中复制“实体 ID”值并将其粘贴到“标识提供者实体 ID”字段中。
  2. 复制“SAML SSO URL”并将其粘贴到“标识提供者 SSO URL”框中。
  3. 在记事本中打开从 Azure AD 下载的证书并复制其中包含的值（不要复制 Begin 和 End 行），然后将其粘贴到“公共 X509 证书”框中。
  4. 保存设置。

      ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_12.png)
 
9. 更新 Azure AD 设置，确保设置正确的标识符 URL。
  1. 从 SAML 屏幕复制“SP 标识 ID”并将其作为“标识符”值粘贴到 Azure AD 中。
  2. “登录 URL”是 Atlassian Cloud 的租户 URL。     

     ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_13.png)
    
10. 在经典门户中，选择“单一登录配置确认”，然后单击“下一步”。
    
    ![Azure AD 单一登录][10]

7. 在“单一登录确认”页上，单击“完成”。  
 
    ![Azure AD 单一登录][11]


### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
在本部分中，将在经典门户中创建一个名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][20]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_09.png) 

2. 在“目录”列表中，选择要启用目录集成的目录。

3. 若要显示用户列表，请在顶部菜单中，单击“用户”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_03.png) 

4. 若要打开“添加用户”对话框，请在底部工具栏中单击“添加用户”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_04.png) 

5. 在“告诉我们有关此用户的信息”对话框页上，执行以下步骤：

    ![创建 Azure AD 测试用户](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_05.png) 
  1. 在“用户类型”中，选择“你的组织中的新用户”。
  2. 在“用户名”文本框中，键入“BrittaSimon”。
  3. 单击“下一步”。

6.  在“用户配置文件”对话框页上，执行以下步骤：

    ![创建 Azure AD 测试用户](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_06.png) 
   1. 在“名字”文本框中，键入“Britta”。  
   2. 在“姓氏”文本框中，键入“Simon”。
   3. 在“显示名称”文本框中，键入“Britta Simon”。
   4. 在“角色”列表中，选择“用户”。
   5. 单击“下一步”。

7. 在“获取临时密码”对话框页上，单击“创建”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_07.png) 

8. 在“获取临时密码”对话框页上，执行以下步骤：

    ![创建 Azure AD 测试用户](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_08.png) 
  1. 写下“新密码”的值。
  2. 单击“完成”。   

### <a name="create-an-atlassian-cloud-test-user"></a>创建 Atlassian Cloud 测试用户

在本部分中，将在 Atlassian Cloud 中创建一个名为 Britta Simon 的用户。 在执行 SSO 之前，该用户必须在 Atlassian Cloud 中存在。 

请使用管理员权限登录到 Atlassian Cloud 实例，然后执行以下步骤。

>[!NOTE] 
>也还可以通过单击“用户”部分中的“批量创建”按钮创建幸运用户。

1. 在“站点管理”部分中，单击“用户”按钮

    ![创建 Atlassian Cloud 用户](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_14.png) 

2. 单击“创建用户”按钮在 Atlassian Cloud 中创建一个用户

    ![创建 Atlassian Cloud 用户](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_15.png) 

3. 输入该用户的电子邮件地址、用户名和全名，然后分配应用程序访问权限。 

    ![创建 Atlassian Cloud 用户](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_16.png)
 
4. 单击“创建用户”按钮，随即会向该用户发送电子邮件邀请，接受邀请后，该用户将在系统中激活。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Atlassian Cloud 的权限，允许她使用 Azure SSO。

![分配用户][200] 

**若要将 Britta Simon 分配到 Atlassian Cloud，请执行以下步骤：**

1. 在经典门户中，若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Atlassian Cloud”。

    ![配置单一登录](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_50.png) 

3. 在顶部菜单中，单击“用户”。

    ![分配用户][203]

4. 在“用户”列表中，选择“Britta Simon”。

5. 在底部工具栏中，单击“分配”。

    ![分配用户][205]

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD SSO 配置。

当在访问面板中单击 Atlassian Cloud 磁贴时，应当会自动登录到 Atlassian Cloud 应用程序。


## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_205.png

