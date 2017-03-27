---
title: "教程：Azure Active Directory 与 Reward Gateway 集成 | Microsoft 文档"
description: "了解如何在 Azure Active Directory 和 Reward Gateway 之间配置单一登录。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 34336386-998a-4d47-ab55-721d97708e5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2d8d925f80830a0d7047e9567fdd413af2e8c5c3
ms.openlocfilehash: 4d4cc0c13fb0d05ecad6c81c4f7b704fa3152ed5
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-reward-gateway"></a>教程：Azure Active Directory 与 Reward Gateway 集成
在本教程中，了解如何将 Reward Gateway 与 Azure Active Directory (Azure AD) 集成。

将 Reward Gateway 与 Azure AD 集成提供以下优势：

* 可在 Azure AD 中控制谁有权访问 Reward Gateway
* 可以让用户使用其 Azure AD 帐户自动登录到 Reward Gateway 单一登录 (SSO)
* 可以在一个中心位置（即 Azure 经典门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件
若要配置 Azure AD 与 Reward Gateway 的集成，需要以下项：

* Azure AD 订阅
* 已启用 Reward Gateway SSO 的订阅

>[!NOTE]
>不建议使用生产环境测试本教程中的步骤。 
> 

测试本教程中的步骤应遵循以下建议：

* 不应使用生产环境，除非有此必要。
* 如果没有 Azure AD 试用环境，可以获取[一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。

本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Reward Gateway
2. 配置和测试 Azure AD SSO

## <a name="add-reward-gateway-from-the-gallery"></a>从库中添加 Reward Gateway
若要配置 Reward Gateway 与 Azure AD 的集成，需要从库中将 Reward Gateway 添加到托管 SaaS 应用列表。

**若要从库中添加 Reward Gateway，请执行以下步骤：**

1. 在 **Azure 经典门户**的左侧导航窗格上，单击“Active Directory”。
   
    ![Active Directory][1]
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序][2]
4. 在页面底部单击“添加”。
   
    ![应用程序][3]
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![应用程序][4]
6. 在搜索框中，键入“Reward Gateway”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-reward-gateway-tutorial/tutorial_rewardgateway_01.png)
7. 在结果窗格中，选择“Reward Gateway”，然后单击“完成”以添加该应用程序。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-reward-gateway-tutorial/tutorial_rewardgateway_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>配置和测试 Azure AD SSO
在本部分中，将基于名为“Britta Simon”的测试用户配置和测试 Reward Gateway 的 Azure AD 单一登录。

若要运行 SSO，Azure AD 需要知道与 Azure AD 用户相对应的 Reward Gateway 用户。 换句话说，需要在 Azure AD 用户与 Reward Gateway 中相关用户之间建立链接关系。

通过将 Azure AD 中“用户名”的值分配为 Reward Gateway 中“用户名”的值来建立此链接关系。

若要配置和测试 Reward Gateway 的 Azure AD SSO，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Reward Gateway 测试用户](#creating-a-reward-gateway-test-user)** - 在 Reward Gateway 中创建 Britta Simon 的对应用户，将其链接到她的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO
在本部分中，将在经典门户中启用 Azure AD SSO，并在 Reward Gateway 应用程序中配置 SSO。

**若要配置 Reward Gateway 的 Azure AD 单一登录，请执行以下步骤：**

1. 在经典门户中的“Reward Gateway”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
    ![配置单一登录][6] 
2. 在“你希望用户如何登录 Reward Gateway”页上，选择“Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-reward-gateway-tutorial/tutorial_rewardgateway_03.png) 
3. 在“配置应用设置”对话框页上，执行以下步骤：
   
    ![配置单一登录](./media/active-directory-saas-reward-gateway-tutorial/tutorial_rewardgateway_04.png) 
  1. 在“标识符 URL”文本框中，使用以下模式键入用户用于登录 Reward Gateway 应用程序的 URL： 
   
    | 标识符 URL |
    | --- |
    | `https://<company name>.rewardgateway.com/` |
    | `https://<company name>.rewardgateway.co.uk/` |
    | `https://<company name>.rewardgateway.co.nz/` |
    | `https://<company name>.rewardgateway.com.au/` |
  2. 在“回复 URL”文本框中，使用以下模式键入 URL： 

    | 回复 URL |
    | --- |
    | `https://<company name>.rewardgateway.com/Authentication/EndLogin?idp=<Unique Id>` |
    | `https://<company name>.rewardgateway.co.uk/Authentication/EndLogin?idp=<Unique Id>` |
    | `https://<company name>.rewardgateway.co.nz/Authentication/EndLogin?idp=<Unique Id>` |
    | `https://<company name>.rewardgateway.com.au/Authentication/EndLogin?idp=<Unique Id>` |

  3. 单击“下一步”。

4. 在“配置 Reward Gateway 的单一登录”页上，执行以下步骤：
   
    ![配置单一登录](./media/active-directory-saas-reward-gateway-tutorial/tutorial_rewardgateway_05.png)
  * 单击“下载元数据”，然后在计算机上保存该文件。
5. 若要为应用程序配置 SSO，请联系 Reward Gateway [支持团队](mailTo:clientsupport@rewardgateway.com)，并向他们提供以下内容：
   
    • 下载的**元数据**
6. 在经典门户中，选择“单一登录配置确认”，然后单击“下一步”。
   
    ![Azure AD 单一登录][10]
6. 在“单一登录确认”页上，单击“完成”。  
   
    ![Azure AD 单一登录][11]

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
在本部分中，将在经典门户中创建一个名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][20]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-reward-gateway-tutorial/create_aaduser_09.png) 
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要显示用户列表，请在顶部菜单中，单击“用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-reward-gateway-tutorial/create_aaduser_03.png) 
4. 若要打开“添加用户”对话框，请在底部工具栏中单击“添加用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-reward-gateway-tutorial/create_aaduser_04.png) 
5. 在“告诉我们有关此用户的信息”对话框页上，执行以下步骤：

    ![创建 Azure AD 测试用户](./media/active-directory-saas-reward-gateway-tutorial/create_aaduser_05.png)    
  1. 在“用户类型”中，选择“你的组织中的新用户”。
  2. 在“用户名”文本框中，键入“BrittaSimon”。
  3. 单击“资源组名称” 的 Azure 数据工厂。
6. 在“用户配置文件”对话框页上，执行以下步骤：

   ![创建 Azure AD 测试用户](./media/active-directory-saas-reward-gateway-tutorial/create_aaduser_06.png)    
  1. 在“名字”文本框中，键入“Britta”。   
  2. 在“姓氏”文本框中，键入“Simon”。 
  3. 在“显示名称”文本框中，键入“Britta Simon”。
  4. 在“角色”列表中，选择“用户”。
  5. 单击“资源组名称” 的 Azure 数据工厂。
7. 在“获取临时密码”对话框页上，单击“创建”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-reward-gateway-tutorial/create_aaduser_07.png) 
8. 在“获取临时密码”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-reward-gateway-tutorial/create_aaduser_08.png) 
  1. 写下“新密码”的值。 
  2. 单击“完成”。   

### <a name="create-an-reward-gateway-test-user"></a>创建 Reward Gateway 测试用户
在本部分中，将在 Reward Gateway 中创建一个名为“Britta Simon”的用户。 请协助 Reward Gateway [支持团队](mailTo:clientsupport@rewardgateway.com)将用户添加到 Reward Gateway 平台。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户
在本部分中，通过授予 Britta Simon 访问 Reward Gateway 的权限，允许她使用 Azure 单一登录。

![分配用户][200] 

**若要将 Britta Simon 分配到 Reward Gateway，请执行以下步骤：**

1. 在经典门户中，若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![分配用户][201] 
2. 在应用程序列表中，选择“Reward Gateway”。
   
    ![配置单一登录](./media/active-directory-saas-reward-gateway-tutorial/tutorial_rewardgateway_50.png) 
3. 在顶部菜单中，单击“用户”。
   
    ![分配用户][203]
4. 在“用户”列表中，选择“Britta Simon”。
5. 在底部工具栏中，单击“分配”。
   
    ![分配用户][205]

### <a name="test-single-sign-on"></a>测试单一登录
在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的“Reward Gateway”磁贴时，用户应自动登录到 Reward Gateway 应用程序。

## <a name="additional-resources"></a>其他资源
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-reward-gateway-tutorial/tutorial_general_205.png

