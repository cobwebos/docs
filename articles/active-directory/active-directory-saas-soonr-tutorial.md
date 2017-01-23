---
title: "教程：Azure Active Directory 与 Soonr Workplace 集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 Soonr Workplace 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
editor: na
ms.assetid: b75f5f00-ea8b-4850-ae2e-134e5d678d97
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 181760d0bff399790143e7313be760e14a2e019e
ms.openlocfilehash: 4cba977d3bc302fdf7a85a71098043098ac6de12


---
# <a name="tutorial-azure-active-directory-integration-with-soonr-workplace"></a>教程：Azure Active Directory 与 Soonr Workplace 集成

本教程的目的是说明如何将 Soonr Workplace 与 Azure Active Directory (Azure AD) 集成。  
将 Soonr Workplace 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Soonr Workplace
- 可以让用户使用其 Azure AD 帐户自动登录到 Soonr Workplace（单一登录）
- 可以在一个中心位置（即 Azure 经典门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Soonr Workplace 的集成，需备齐以下项目：

- Azure AD 订阅
- 已启用 Soonr Workplace 单一登录的订阅


> [!NOTE] 
> 测试本教程中的步骤时，建议不要使用生产环境。


测试本教程中的步骤应遵循以下建议：

- 不应使用生产环境，除非有此必要。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。


## <a name="scenario-description"></a>方案描述
本教程的目的是介绍如何在测试环境中测试 Azure AD 单一登录。  
本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Soonr Workplace
2. 配置并测试 Azure AD 单一登录


## <a name="adding-soonr-workplace-from-the-gallery"></a>从库中添加 Soonr Workplace
若要配置 Soonr Workplace 与 Azure AD 的集成，需要从库中将 Soonr Workplace 添加到托管 SaaS 应用列表。

**若要从库中添加 Soonr Workplace，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。 

    ![Active Directory][1]

2. 从“目录”列表中，选择要为其启用目录集成的目录。

3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。

    ![应用程序][2]

4. 在页面底部单击“添加”。

    ![应用程序][3]

5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
 
    ![应用程序][4]

6. 在搜索框中，键入“Soonr Workplace”。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-soonr-tutorial/tutorial_soonr_01.png)

7. 在结果窗格中，选择“Soonr Workplace”，然后单击“完成”以添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-soonr-tutorial/tutorial_soonr_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置并测试 Azure AD 单一登录
本部分的目的是说明如何基于名为“Britta Simon”的测试用户配置和测试 Soonr Workplace 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Soonr Workplace 用户。 换句话说，需要在 Azure AD 用户与 Soonr Workplace 中相关用户之间建立链接关系。  

通过将 Azure AD 中“用户名”的值分配为 Soonr Workplace 中“用户名”的值来建立此链接关系。

若要配置和测试 Soonr Workplace 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-single-sign-on)** - 让用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Soonr Workplace 测试用户](#creating-a-soonr-workplace-test-user)** - 在 Soonr Workplace 中创建 Britta Simon 的对应用户，将其链接到她的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在经典门户中启用 Azure AD 单一登录并在 Soonr Workplace 应用程序中配置单一登录。


**若要配置 Soonr Workplace 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 经典门户中的“Soonr Workplace”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。

    ![配置单一登录][6] 

2. 在“你希望用户如何登录 Soonr Workplace”页上，选择“Azure AD 单一登录”，然后单击“下一步”。

    ![配置单一登录](./media/active-directory-saas-soonr-tutorial/tutorial_soonr_03.png) 

3. 在“配置应用设置”对话框页上，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-soonr-tutorial/tutorial_soonr_04.png) 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<server-name>.soonr.com/singlesignon/saml/SSO`。

    b.保留“数据库类型”设置，即设置为“共享”。 单击“资源组名称” 的 Azure 数据工厂。

    > [!NOTE] 
    > 请注意，这不是实际值。 必须使用实际登录 URL 更新此值。 若要获取此值，请与 Soonr Workplace 支持团队联系。

4. 在“配置 Soonr Workplace 的单一登录”页上，单击“下载元数据”，然后将该文件保存在计算机上：

    ![配置单一登录](./media/active-directory-saas-soonr-tutorial/tutorial_soonr_05.png) 

5. 若要为应用程序配置 SSO，请联系 Soonr Workplace 支持团队，并向其提供以下内容： 

    •  下载的**元数据**文件

    •  **颁发者 URL**

    •  **SAML SSO URL**

    •  **单一注销服务 URL**

    >[!NOTE]
    >此应用程序已被 <a href="https://azure.microsoft.com/en-us/marketplace/partners/autotask-corporataion/autotask/">Autotask Workplace</a> 取代，可参阅<a href="https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-autotaskworkplace-tutorial">本</a>教程，以了解使用 Azure AD 配置该应用程序的信息。
   
6. 在 Azure 经典门户中，选择单一登录配置确认，然后单击“下一步”。

    ![Azure AD 单一登录][10]

7. 在“单一登录确认”页上，单击“完成”。  
  
    ![Azure AD 单一登录][11]



### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 经典门户中创建名为 Britta Simon 的测试用户。  

![创建 Azure AD 用户][20]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-soonr-tutorial/create_aaduser_09.png) 

2. 在“目录”列表中，选择要启用目录集成的目录。

3. 若要显示用户列表，请在顶部菜单中，单击“用户”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-soonr-tutorial/create_aaduser_03.png) 

4. 若要打开“添加用户”对话框，请在底部工具栏中单击“添加用户”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-soonr-tutorial/create_aaduser_04.png) 

5. 在“告诉我们有关此用户的信息”对话框页上，执行以下步骤：

    ![创建 Azure AD 测试用户](./media/active-directory-saas-soonr-tutorial/create_aaduser_05.png) 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 对于“用户类型”，选择“组织中的新用户”。

    b. 在“用户名”文本框中，键入“BrittaSimon”。

    c. 单击“下一步”。

6.  在“用户配置文件”对话框页上，执行以下步骤：

    ![创建 Azure AD 测试用户](./media/active-directory-saas-soonr-tutorial/create_aaduser_06.png) 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名字”文本框中，键入“Britta”。  

    b. 在“姓氏”文本框中，键入“Simon”。

    c. 在“显示名称”文本框中，键入“Britta Simon”。

    d.单击“下一步”。 在“角色”列表中，选择“用户”。

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 单击“下一步”。

7. 在“获取临时密码”对话框页上，单击“创建”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-soonr-tutorial/create_aaduser_07.png) 

8. 在“获取临时密码”对话框页上，执行以下步骤：

    ![创建 Azure AD 测试用户](./media/active-directory-saas-soonr-tutorial/create_aaduser_08.png) 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 写下“新密码”的值。

    b. 单击“完成”。   



### <a name="creating-a-soonr-workplace-test-user"></a>创建 Soonr Workplace 测试用户

本部分的目的是在 Soonr Workplace 中创建名为“Britta Simon”的用户。 请协助 Soonr Workplace 支持团队在该平台中创建用户。 可以在<a href="https://na01.safelinks.protection.outlook.com/?url=http%3A%2F%2Fsoonr.com%2FAWPHelp%2FContent%2F0_HOME%2FSupport_for_End_Clients.htm&data=01%7C01%7Cv-saikra%40microsoft.com%7Ccbb4367ab09b4dacaac408d3eebe3f42%7C72f988bf86f141af91ab2d7cd011db47%7C1&sdata=FB92qtE6m%2Fd8yox7AnL2f1h%2FGXwSkma9x9H8Pz0955M%3D&reserved=0/">此处</a>建立 Soonr 支持票证。


### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

本部分的目的是通过授予 Britta Simon 访问 Soonr Workplace 的权限，允许她使用 Azure 单一登录。

![分配用户][200] 

**若要将 Britta Simon 分配到 Soonr Workplace，请执行以下步骤：**

1. 在 Azure 经典门户中，若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Soonr Workplace”。

    ![配置单一登录](./media/active-directory-saas-soonr-tutorial/tutorial_soonr_50.png) 

1. 在顶部菜单中，单击“用户”。

    ![分配用户][203] 

1. 在“用户”列表中，选择“Britta Simon”。

2. 在底部工具栏中，单击“分配”。

    ![分配用户][205]



### <a name="testing-single-sign-on"></a>测试单一登录

本部分的目的是使用访问面板测试 Azure AD 单一登录配置。  
单击访问面板中的“Soonr Workplace”磁贴时，用户应自动登录到 Soonr Workplace 应用程序。


## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-soonr-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO1-->


