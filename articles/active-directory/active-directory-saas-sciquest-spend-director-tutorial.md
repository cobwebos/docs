---
title: "教程：Azure Active Directory 与 SciQuest Spend Director 集成 | Microsoft 文档"
description: "了解如何在 Azure Active Directory 和 SciQuest Spend Director 之间配置单一登录。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 9fab641b-292e-4bef-91d1-8ccc4f3a0c1f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/18/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 9cc6d0b42f5e3ace2acff2f68666a22accc4f8b3
ms.openlocfilehash: bec9f0cd3a6801dbcefeaed9d2b2839245f7014f


---
# <a name="tutorial-azure-active-directory-integration-with-sciquest-spend-director"></a>教程：Azure Active Directory 与 SciQuest Spend Director 集成
本教程的目的是说明如何将 SciQuest Spend Director 与 Azure Active Directory (Azure AD) 集成。  
将 SciQuest Spend Director 与 Azure AD 集成提供以下优势： 

* 可在 Azure AD 中控制谁有权访问 SciQuest Spend Director 
* 可以让用户使用其 Azure AD 帐户自动登录到 SciQuest Spend Director（单一登录）
* 可以在一个中心位置（即 Azure 经典门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件
若要配置 Azure AD 与 SciQuest Spend Director 的集成，需备齐以下项目：

* Azure AD 订阅
* 已启用 SciQuest Spend Director 单一登录的订阅

> [!NOTE]
> 测试本教程中的步骤时，建议不要使用生产环境。
> 
> 

测试本教程中的步骤应遵循以下建议：

* 不应使用生产环境，除非有此必要。
* 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。 

## <a name="scenario-description"></a>方案描述
本教程的目的是介绍如何在测试环境中测试 Azure AD 单一登录。  
本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 SciQuest Spend Director 
2. 配置并测试 Azure AD 单一登录

## <a name="adding-sciquest-spend-director-from-the-gallery"></a>从库中添加 SciQuest Spend Director
若要配置 SciQuest Spend Director 与 Azure AD 的集成，需要从库中将 SciQuest Spend Director 添加到托管 SaaS 应用列表。

**若要从库中添加 SciQuest Spend Director，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。 
   
    ![Active Directory][1]

2. 从“目录”列表中，选择要为其启用目录集成的目录。

3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序][2]

4. 在页面底部单击“添加”。
   
    ![应用程序][3]

5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![应用程序][4]

6. 在搜索框中，键入“SciQuest Spend Director”。
   
    ![应用程序][5]

7. 在结果窗格中，选择“SciQuest Spend Director”，然后单击“完成”以添加该应用程序。
   
    ![应用程序][6]

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置并测试 Azure AD 单一登录
本部分的目的是说明如何基于名为“Britta Simon”的测试用户配置和测试 SciQuest Spend Director 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 SciQuest Spend Director 用户。 换句话说，需要在 Azure AD 用户与 SciQuest Spend Director 中相关用户之间建立链接关系。  
通过将 Azure AD 中“用户名”的值分配为 SciQuest Spend Director 中“用户名”的值来建立此链接关系。

若要配置和测试 SciQuest Spend Director 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 SciQuest Spend Director 测试用户](#creating-a-halogen-software-test-user)** - 在 SciQuest Spend Director 中创建 Britta Simon 的对应用户，将其链接到她的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-single-sign-on"></a>配置 Azure AD 单一登录
本部分的目的是在 Azure 经典门户中启用 Azure AD 单一登录并在 SciQuest Spend Director 应用程序中配置单一登录。

**若要配置 SciQuest Spend Director 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 经典门户中的“SciQuest Spend Director”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
    ![配置单一登录][8]

2. 在“你希望用户如何登录 SciQuest Spend Director”页上，选择“Azure AD 单一登录”，然后单击“下一步”。
   
    ![Azure AD 单一登录][9]

3. 在“配置应用设置”对话框页上，执行以下步骤： 
   
    ![配置应用设置][10]
   
     a. 在“登录 URL”文本框中，使用以下模式键入用户用于登录 SciQuest Spend Director 应用程序的 URL：*https://.*sciquest.com/。**
   
     b.保留“数据库类型”设置，即设置为“共享”。 在“回复 URL”文本框中键入的值与在“登录 URL”文本框中键入的值相同。 
   
     c. 单击“资源组名称” 的 Azure 数据工厂。

4. 在“配置 SciQuest Spend Director 的单一登录”页上，单击“下载元数据”，然后将元数据文件本地保存在计算机上。
   
    ![什么是 Azure AD Connect][11]

5. 联系 SciQuest 支持，以便使用前面下载的元数据启用此身份验证方法。

6. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。 
   
    ![什么是 Azure AD Connect][15]

7. 在“单一登录确认”页上，单击“完成”。  

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 经典门户中创建名为 Britta Simon 的测试用户。

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。
   
    ![什么是 Azure AD Connect][100] 

2. 在“目录”列表中，选择要启用目录集成的目录。

3. 若要显示用户列表，请在顶部菜单中单击“用户”。
   
    ![什么是 Azure AD Connect][101] 

4. 若要打开“添加用户”对话框，请在底部工具栏中单击“添加用户”。 
   
    ![什么是 Azure AD Connect][102] 

5. 在“告诉我们有关此用户的信息”对话框页上，执行以下步骤：
   
    ![什么是 Azure AD Connect][103] 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 对于“用户类型”，选择“组织中的新用户”。
   
    b. 在“用户名”文本框中，键入“BrittaSimon”。
   
    c. 单击“资源组名称” 的 Azure 数据工厂。

6. 在“用户配置文件”对话框页上，执行以下步骤： 
   
    ![什么是 Azure AD Connect][104] 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名字”文本框中，键入“Britta”。  
   
    b. 在“姓氏”文本框中，键入 **Simon**。
   
    c. 在“显示名称”文本框中，键入“Britta Simon”。
   
    d.单击“下一步”。 在“角色”列表中，选择“用户”。
   
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 单击“资源组名称” 的 Azure 数据工厂。

7. 在“获取临时密码”对话框页上，单击“创建”。
   
    ![什么是 Azure AD Connect][105]  

8. 在“获取临时密码”对话框页上，执行以下步骤：
   
    ![什么是 Azure AD Connect][106]   
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 写下“新密码”的值。
   
    b. 单击“完成”。   

### <a name="creating-a-sciquest-spend-director-test-user"></a>创建 SciQuest Spend Director 测试用户
本部分的目的是在 SciQuest Spend Director 中创建名为“Britta Simon”的用户。

你需要联系 SciQuest Spend Director 支持团队，向他们提供有关测试帐户的详细信息，以便他们创建测试帐户。

或者，可以利用实时预配（SciQuest Spend Director 提供支持的一项单一登录功能）。  
如果已启用实时预配，则在单一登录尝试期间 SciQuest Spend Director 将自动创建用户（如果这些用户不存在）。 此功能使你无需手动创建单一登录对应用户。

若要启用实时预配，需要联系 SciQuest Spend Director 支持团队。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户
本部分的目的是通过授予 Britta Simon 访问 SciQuest Spend Director 的权限，允许她使用 Azure 单一登录。

![什么是 Azure AD Connect][200]

**若要将 Britta Simon 分配到 SciQuest Spend Director，请执行以下步骤：**

1. 在 Azure 经典门户中，若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![什么是 Azure AD Connect][201]

2. 在应用程序列表中，选择“SciQuest Spend Director”。
   
    ![什么是 Azure AD Connect][202]

3. 在顶部菜单中，单击“用户”。
   
    ![什么是 Azure AD Connect][203]

4. 在“用户”列表中，选择“Britta Simon”。
   
    ![什么是 Azure AD Connect][204]

5. 在底部工具栏中，单击“分配”。
   
    ![什么是 Azure AD Connect][205]

### <a name="testing-single-sign-on"></a>测试单一登录
本部分的目的是使用访问面板测试 Azure AD 单一登录配置。  
单击访问面板中的“SciQuest Spend Director”磁贴时，用户应自动登录到 SciQuest Spend Director 应用程序。

## <a name="additional-resources"></a>其他资源
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_sciquest_spend_director_01.png
[6]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_sciquest_spend_director_05.png
[8]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_06.png
[9]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_07.png
[10]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_08.png
[11]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_sciquest_spend_director_03.png
[15]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_sciquest_spend_director_04.png

[100]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_09.png 
[101]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_10.png 
[102]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_11.png 
[103]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_12.png 
[104]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_13.png 
[105]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_14.png 
[106]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_15.png 
[200]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_16.png 
[201]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_17.png 
[202]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_sciquest_spend_director_06.png
[203]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_18.png
[204]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_19.png
[205]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_20.png




<!--HONumber=Nov16_HO4-->


