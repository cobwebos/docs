---
title: "教程：Azure Active Directory 与 Front 集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 Front 之间配置单一登录。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 88270b6d-2571-434a-b139-b6ccc3a2b19f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 71bc49398c576aa55159f4f62b6b6ad541d0fe5f


---
# <a name="tutorial-azure-active-directory-integration-with-front"></a>教程：Azure Active Directory 与 Front 集成
本教程的目的是演示如何将 Front 与 Azure Active Directory (Azure AD) 集成。

将 Front 与 Azure AD 集成具有以下优势：

* 可在 Azure AD 中控制谁有权访问 Front
* 可以让用户使用其 Azure AD 帐户自动登录到 Front（单一登录）
* 可在一个中心位置（即 Azure 经典门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件
若要配置 Azure AD 与 Front 的集成，需要以下项：

* Azure AD 订阅
* 已启用 Front 单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。
> 
> 

测试本教程中的步骤应遵循以下建议：

* 不应使用生产环境，除非有此必要。
* 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
本教程的目的是介绍如何在测试环境中测试 Azure AD 单一登录。

本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Front
2. 配置和测试 Azure AD 单一登录

## <a name="adding-front-from-the-gallery"></a>从库中添加 Front
若要配置 Front 与 Azure AD 的集成，需要从库中将 Front 添加到托管 SaaS 应用列表。

**若要从库中添加 Front，请执行以下步骤：**

1. 在 **Azure 经典门户**的左侧导航窗格上，单击“Active Directory”。 
   
    ![Active Directory][1]
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序][2]
4. 在页面底部单击“添加”。
   
    ![应用程序][3]
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![应用程序][4]
6. 在搜索框中，键入“Front”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-front-tutorial/tutorial_front_01.png)
7. 在结果面板中，选择“Front”，然后单击“完成”以添加该应用程序。
   
    ![在库中选择应用](./media/active-directory-saas-front-tutorial/tutorial_front_0001.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
本部分旨在展示如何根据名为“Britta Simon”的测试用户的指令配置和测试 Front 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Front 用户。 换句话说，需要建立 Azure AD 用户与 Front 中相关用户之间的关联关系。

可以通过将 Azure AD 中“用户名”的值分配为 Front 中“用户名”的值来建立此关联关系。

若要配置和测试 Front 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-single-sign-on)** - 让用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Front 测试用户](#creating-a-front-test-user)** - 在 Front 中创建 Britta Simon 的对应用户，将其链接到她的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录
在本部分中，将在经典门户中启用 Azure AD 单一登录并在 Front 应用程序中配置单一登录。

**若要配置 Front 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 经典门户中的“Front”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
    ![配置单一登录][6] 
2. 在“你希望用户如何登录 Front”页上，选择“Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-front-tutorial/tutorial_front_03.png)
3. 在“配置应用设置”对话框页上，如果要在“IDP 发起的模式”下配置应用程序，请执行以下步骤，然后单击“下一步”：
   
    ![配置单一登录](./media/active-directory-saas-front-tutorial/tutorial_front_04.png)
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“标识符”文本框中，使用以下模式键入 URL：`https://<company name>.frontapp.com`
   
    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<company name>.frontapp.com/sso/saml/callback`
   
    c. 单击“下一步”
4. 如果要在“配置应用设置”对话框页上以“SP 发起的模式”配置应用程序，则单击“显示高级设置(可选)”，然后输入“登录 URL”，并单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-front-tutorial/tutorial_front_05.png)
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<company name>.frontapp.com`
   
    b. 单击“下一步”
   
   > [!NOTE]
   > 请注意，这些不是实际值。 必须使用实际登录 URL、标识符和回复 URL 更新这些值。 若要获取这些值，可以参考**步骤 12** 以了解详细信息或者通过 [support@frontapp.com](emailTo:support@frontapp.com) 联系 Front。
   > 
   > 
5. 在“配置 Front 的单一登录”页上，执行以下步骤，然后单击“下一步”：
   
    ![配置单一登录](./media/active-directory-saas-front-tutorial/tutorial_front_06.png)
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 单击“下载证书”，然后将文件保存在计算机上。
   
    b. 单击“资源组名称” 的 Azure 数据工厂。
6. 以管理员身份登录到 Front 租户。
7. 转到“设置(左侧边栏底部的齿轮图标) > 首选项”。
   
    ![在应用端配置单一登录](./media/active-directory-saas-front-tutorial/tutorial_front_000.png)
8. 单击“单一登录”链接。
   
    ![在应用端配置单一登录](./media/active-directory-saas-front-tutorial/tutorial_front_001.png)
9. 在“单一登录”下拉列表中选择“SAML”。
   
    ![在应用端配置单一登录](./media/active-directory-saas-front-tutorial/tutorial_front_002.png)
10. 在“入口点”文本框中放置 Azure AD 应用程序配置向导中“单一登录服务 URL”的值。
    
    ![在应用端配置单一登录](./media/active-directory-saas-front-tutorial/tutorial_front_003.png)
11. 复制已下载的证书文件的内容，然后将其粘贴到“签名证书”文本框中。
    
    ![在应用端配置单一登录](./media/active-directory-saas-front-tutorial/tutorial_front_004.png)
12. 确认这些 URl 与步骤 3 中的配置匹配。
    
    ![在应用端配置单一登录](./media/active-directory-saas-front-tutorial/tutorial_front_005.png)
13. 单击“保存”按钮。
14. 在经典门户中，选择“单一登录配置确认”，然后单击“下一步”。
    
    ![Azure AD 单一登录][10]
15. 在“单一登录确认”页上，单击“完成”。  
    
    ![Azure AD 单一登录][11]

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在经典门户中创建名为“Britta Simon”的测试用户。

![创建 Azure AD 用户][20]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-front-tutorial/create_aaduser_09.png)
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要显示用户列表，请在顶部菜单中，单击“用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-front-tutorial/create_aaduser_03.png)
4. 若要打开“添加用户”对话框，请在底部工具栏中单击“添加用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-front-tutorial/create_aaduser_04.png)
5. 在“告诉我们有关此用户的信息”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-front-tutorial/create_aaduser_05.png)
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 对于“用户类型”，选择“组织中的新用户”。
   
    b. 在“用户名”文本框中，键入“BrittaSimon”。
   
    c. 单击“资源组名称” 的 Azure 数据工厂。
6. 在“用户配置文件”对话框页上，执行以下步骤：
   
   ![创建 Azure AD 测试用户](./media/active-directory-saas-front-tutorial/create_aaduser_06.png)
   
   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名字”文本框中，键入“Britta”。  
   
   b. 在“姓氏”文本框中，键入“Simon”。
   
   c. 在“显示名称”文本框中，键入“Britta Simon”。
   
   d.单击“下一步”。 在“角色”列表中，选择“用户”。
   
   e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 单击“资源组名称” 的 Azure 数据工厂。
7. 在“获取临时密码”对话框页上，单击“创建”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-front-tutorial/create_aaduser_07.png)
8. 在“获取临时密码”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-front-tutorial/create_aaduser_08.png)
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 写下“新密码”的值。
   
    b. 单击“完成”。   

### <a name="creating-a-front-test-user"></a>创建 Front 测试用户
本部分的目的是在 Front 中创建名为 Britta Simon 的用户。请与 Front 支持团队合作，在 Front 帐户中添加用户。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户
本部分的目的是通过向 Britta Simon 授予对 Front 的访问权限，使她能够使用 Azure 单一登录。

![分配用户][200]

**若要将 Britta Simon 分配到 Front，请执行以下步骤：**

1. 在经典门户中，若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![分配用户][201]
2. 在应用程序列表中，选择“Front”。
   
    ![配置单一登录](./media/active-directory-saas-front-tutorial/tutorial_front_50.png)
3. 在顶部菜单中，单击“用户”。
   
    ![分配用户][203]
4. 在“用户”列表中，选择“Britta Simon”。
5. 在底部工具栏中，单击“分配”。
   
    ![分配用户][205]

### <a name="testing-single-sign-on"></a>测试单一登录
本部分的目的是使用“访问面板”测试 Azure AD 单一登录配置。

当在访问面板中单击 Front 磁贴时，应该会自动登录 Front 应用程序。

## <a name="additional-resources"></a>其他资源
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-front-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-front-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-front-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-front-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-front-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-front-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-front-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-front-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-front-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-front-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-front-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-front-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-front-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->


