---
title: "教程：Azure Active Directory 与 Nexonia 的集成 | Microsoft Docs"
description: "了解如何使用 Nexonia 与 Azure Active Directory 启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: af9618c0-3437-4bb4-ad5a-568229b496db
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/15/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 1b5527090a41c274b590ed9d1ac7b561b6f4ed14
ms.openlocfilehash: 05db0ade3f74f380ccf287be61ae19b96bfee902


---

# <a name="tutorial-azure-active-directory-integration-with-nexonia"></a>教程：Azure Active Directory 与 Nexonia 集成

本教程介绍了如何将 Nexonia 与 Azure Active Directory (Azure AD) 集成。

将 Nexonia 与 Azure AD 集成可提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Nexonia
- 可使用户通过其 Azure AD 帐户自动登录 Nexonia（单一登录）
- 可在一个中心位置（即 Azure 经典门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Nexonia 的集成，需要以下项目：

- Azure AD 订阅
- 已启用 Nexonia 单一登录的订阅


> [!NOTE] 
> 不建议使用生产环境测试本教程中的步骤。


测试本教程中的步骤应遵循以下建议：

- 不应使用生产环境，除非有此必要。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。


## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。

本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Nexonia
2. 配置和测试 Azure AD 单一登录


## <a name="adding-nexonia-from-the-gallery"></a>从库中添加 Nexonia
若要配置 Nexonia 与 Azure AD 的集成，需要将 Nexonia 从库中添加到托管的 SaaS 应用列表。

**若要从库中添加 Nexonia，请执行以下步骤：**

1. 在 **Azure 经典门户**的左侧导航窗格上，单击“Active Directory”。

    ![Active Directory][1]
2. 从“目录”列表中，选择要为其启用目录集成的目录。

3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。

    ![应用程序][2]

4. 在页面底部单击“添加”。

    ![应用程序][3]

5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。

    ![应用程序][4]

6. 在“搜索”框中键入“Nexonia”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-nexonia-tutorial/tutorial_nexonia_01.png)

7. 在“结果”窗格中，选择“Nexonia”，然后单击“完成”，添加该应用程序。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-nexonia-tutorial/tutorial_nexonia_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，根据名为“Britta Simon”的测试用户配置和测试 Nexonia 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 中的用户相对应的 Nexonia 用户。 换句话说，需要建立 Azure AD 用户与 Nexonia 中相关用户之间的链接关系。

可通过将 Azure AD 中“用户名”的值分配为 Nexonia 中“用户名”的值来建立此链接关系。

若要配置和测试 Nexonia 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Nexonia 测试用户](#creating-a-nexonia-test-user)** - 在 Nexonia 中创建 Britta Simon 的对应用户，将其链接到其 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

本部分需在经典门户中启用 Azure AD 单一登录，并在 Nexonia 应用程序中配置单一登录。


**若要配置 Nexonia 的 Azure AD 单一登录，请执行以下步骤：**

1. 在经典门户中的“Nexonia”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
     
    ![配置单一登录][6] 

2. 在“你希望用户如何登录 Nexonia”页上，选择“Azure AD 单一登录”，然后单击“下一步”。

    ![配置单一登录](./media/active-directory-saas-nexonia-tutorial/tutorial_nexonia_03.png) 

3. 在“配置应用设置”对话框页上，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-nexonia-tutorial/tutorial_nexonia_04.png) 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“回复 URL”文本框中，键入使用以下模式的 URL：`https://system.nexonia.com/assistant/saml.do?orgCode=<organizationcode>`
    
    b. 单击“下一步”
 
4. 在“配置 Nexonia 的单一登录”页上，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-nexonia-tutorial/tutorial_nexonia_05.png)

    a. 单击“下载证书”，然后将文件保存在计算机上。

    b. 单击“下一步”。


5. 若要为应用程序配置 SSO，请联系 Nexonia 支持团队，并向他们提供以下内容：

    • 下载的 **证书**

    • **实体 ID**

    • **SAML SSO URL**

    • **单一注销服务 URL**

6. 在经典门户中，选择“单一登录配置确认”，然后单击“下一步”。
    
    ![Azure AD 单一登录][10]

7. 在“单一登录确认”页上，单击“完成”。  
 
    ![Azure AD 单一登录][11]


### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
在本部分中，在经典门户中创建名为“Britta Simon”的测试用户。


![创建 Azure AD 用户][20]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-nexonia-tutorial/create_aaduser_09.png) 

2. 在“目录”列表中，选择要启用目录集成的目录。

3. 若要显示用户列表，请在顶部菜单中，单击“用户”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-nexonia-tutorial/create_aaduser_03.png) 

4. 若要打开“添加用户”对话框，请在底部工具栏中单击“添加用户”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-nexonia-tutorial/create_aaduser_04.png) 

5. 在“告诉我们有关此用户的信息”对话框页中，执行以下步骤： ![创建 Azure AD 测试用户](./media/active-directory-saas-nexonia-tutorial/create_aaduser_05.png) 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 对于“用户类型”，选择“组织中的新用户”。

    b. 在“用户名”文本框中，键入“BrittaSimon”。

    c. 单击“下一步”。

6.  在“用户配置文件”对话框页面上，执行以下步骤：![创建 Azure AD 测试用户](./media/active-directory-saas-nexonia-tutorial/create_aaduser_06.png) 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名字”文本框中，键入“Britta”。  

    b. 在“姓氏”文本框中，键入“Simon”。

    c. 在“显示名称”文本框中，键入“Britta Simon”。

    d.单击“下一步”。 在“角色”列表中，选择“用户”。

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 单击“下一步”。

7. 在“获取临时密码”对话框页上，单击“创建”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-nexonia-tutorial/create_aaduser_07.png) 

8. 在“获取临时密码”对话框页上，执行以下步骤：

    ![创建 Azure AD 测试用户](./media/active-directory-saas-nexonia-tutorial/create_aaduser_08.png) 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 写下“新密码”的值。

    b. 单击“完成”。   



### <a name="creating-an-nexonia-test-user"></a>创建 Nexonia 测试用户

在本部分中，需在 Nexonia 中创建名为“Britta Simon”的用户。 请与 Nexonia 支持团队协作，将用户添加到 Nexonia 平台中。 使用单一登录前，必须先创建并激活用户。


### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

本部分需授予 Britta Simon 访问 Nexonia 的权限，使之能够使用 Azure 单一登录。

![分配用户][200] 

**若要将 Britta Simon 分配到 Nexonia，请执行以下步骤：**

1. 在经典门户中，若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Nexonia”。

    ![配置单一登录](./media/active-directory-saas-nexonia-tutorial/tutorial_nexonia_50.png) 

3. 在顶部菜单中，单击“用户”。

    ![分配用户][203]

4. 在“用户”列表中，选择“Britta Simon”。

5. 在底部工具栏中，单击“分配”。

    ![分配用户][205]


### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的“Nexonia”磁贴时，应会自动登录 Nexonia 应用程序。


## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-nexonia-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-nexonia-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-nexonia-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-nexonia-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-nexonia-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-nexonia-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-nexonia-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-nexonia-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-nexonia-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-nexonia-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-nexonia-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-nexonia-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-nexonia-tutorial/tutorial_general_205.png



<!--HONumber=Feb17_HO3-->


