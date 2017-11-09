---
title: "教程：Azure Active Directory 与 @Task 集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 与 @Task 之间配置单一登录。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: aab8bd2f-f9dd-42da-a18e-d707865687d7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jeedes
ms.openlocfilehash: ebb19ca6cbaf04106fbce937d95651e709854cfd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-task"></a>教程：Azure Active Directory 与 @Task 集成
本教程旨在展示如何将 @Task 与 Azure Active Directory (Azure AD) 集成。  
将 @Task 与 Azure AD 集成提供以下优势： 

* 可在 Azure AD 中控制谁有权访问 @Task
* 可使用户通过其 Azure AD 帐户自动登录 @Task（单一登录）
* 可在一个中心位置（即 Azure 经典门户）管理帐户

如果想要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件
若要配置 Azure AD 与 @Task 的集成，需要以下项：

* Azure AD 订阅
* 启用的订阅上的 @Task 单一登录

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。
> 
> 

测试本教程中的步骤应遵循以下建议：

* 除非必要，请勿使用生产环境。
* 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。 

## <a name="scenario-description"></a>方案描述
本教程的目的是介绍如何在测试环境中测试 Azure AD 单一登录。  
在本教程中概述的方案包括三个主要构建基块：

1. 从库中添加 @Task 
2. 配置和测试 Azure AD 单一登录

## <a name="adding-task-from-the-gallery"></a>从库中添加 @Task
要配置 @Task 与 Azure AD 的集成，需要将库中的 @Task 添加到托管的 SaaS 应用列表。

**若要从库中添加 @Task，请执行以下步骤：**

1. 在 **Azure 经典门户**的左侧导航窗格上，单击“Active Directory”。 
   
    ![Active Directory][1] 
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序][2] 
4. 在页面底部单击“添加”。
   
    ![应用程序][3] 
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![应用程序][4] 
6. 在“搜索框”中，键入 **@Task**。
   
    ![应用程序][5] 
7. 在“结果”窗格中，选择 **@Task**，并单击“完成”，添加该应用程序。
   
    ![应用程序][30] 

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
本部分旨在展示如何根据名为“Britta Simon”的测试用户的指令配置和测试 @Task 的 Azure AD 单一登录。

对于单一登录到工作帐户，Azure AD 需要知道 Azure AD 用户在 @Task 中的对应用户是谁。 换句话说，需要建立 Azure AD 用户与 @Task 中相关用户之间的链接关系。   
可以通过将 Azure AD 中“用户名”的值分配为 @Task 中“用户名”的值来建立此链接关系。

若要配置和测试 @Task 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 @Tasktest 用户](#creating-a-halogen-software-test-user)** - 在 @Taskthat 中创建 Britta Simon 的对应用户，并将其链接到她的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录
本部分要在 Azure 经典门户中启用 Azure AD 单一登录，并且在 @Task 应用程序中配置单一登录。

**若要配置 @Task 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 经典门户中的 **@Task** 应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
    ![配置单一登录][6] 
2. 在“你希望用户如何登录 @Task”页上，选择“Azure AD 单一登录”，并单击“下一步”。
   
    ![Azure AD 单一登录][7] 
3. 在“配置应用设置”对话框页上，执行以下步骤：
   
    ![配置应用设置][8] 
   
     a. 在“登录 URL”文本框中，键入用户用于登录 @Task 应用程序的 URL（例如：*https://<Tenant name>.attask-ondemand.com*）。
   
     b. 单击“下一步”。
4. 在“配置 @Task 的单一登录”页面上，单击“下载元数据”，然后将元数据文件本地保存在计算机上，随后单击“下一步”。
   
    ![什么是 Azure AD Connect][9] 
5. 以管理员身份登录 @Task 公司站点。
6. 转到“单一登录配置”。
7. 在“单一登录”对话框上，执行以下步骤
   
    ![配置单一登录][23]
   
    a. 对于“类型”，选择“SAML 2.0”。
   
    b. 选择“服务提供商 ID”。
   
    c. 在 Azure 经典门户中，复制“远程登录 URL”，然后将其粘贴到“登录门户 URL”文本框。
   
    d.单击“下一步”。 在 Azure 经典门户中，复制“单一注销服务 URL”，然后将其粘贴到“注销 URL”文本框。
   
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，并单击“确定”。 在 Azure 经典门户中，复制“更改密码 URL”，然后将其粘贴到“更改密码 URL”文本框。
   
    f. 单击“保存” 。
8. 在 Azure 经典门户上，选择“单一登录配置确认”，并单击“下一步”。 
   
    ![什么是 Azure AD Connect][10]
9. 在“单一登录确认”页上，单击“完成”。  
   
    ![什么是 Azure AD Connect][11]

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 经典门户中创建名为 Britta Simon 的测试用户。  

![创建 Azure AD 用户][20]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-attask-tutorial/create_aaduser_02.png) 
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要显示用户列表，请在顶部菜单中，单击“用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-attask-tutorial/create_aaduser_03.png) 
4. 若要打开“添加用户”对话框，请在底部工具栏中单击“添加用户”。 
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-attask-tutorial/create_aaduser_04.png) 
5. 在“告诉我们有关此用户的信息”对话框页上，执行以下步骤： 
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-attask-tutorial/create_aaduser_05.png) 
   
    a. 对于“用户类型”，选择“组织中的新用户”。
   
    b. 在“用户名”文本框中，键入“BrittaSimon”。
   
    c. 单击“下一步”。
6. 在“用户配置文件”对话框页上，执行以下步骤： 
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-attask-tutorial/create_aaduser_06.png) 
   
    a. 在“名字”文本框中，键入“Britta”。  
   
    b. 在“姓氏”文本框中，键入“Simon”。
   
    c. 在“显示名称”文本框中，键入“Britta Simon”。
   
    d.单击“下一步”。 在“角色”列表中，选择“用户”。

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，并单击“确定”。 单击“下一步”。

7. 在“获取临时密码”对话框页上，单击“创建”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-attask-tutorial/create_aaduser_07.png) 
8. 在“获取临时密码”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-attask-tutorial/create_aaduser_08.png) 
   
    a. 写下“新密码”的值。
   
    b. 单击“完成”。   

### <a name="creating-an-task-test-user"></a>创建 @Task 测试用户
本部分的目的是在 @Task 中创建名为“Britta Simon”的用户。

**若要在 @Task 中创建名为“Britta Simon”的用户，请执行以下步骤：**

1. 以管理员身份登录 @Task 公司站点。
2. 在顶部菜单中，单击“人脉”。
3. 单击“新建用户”。 
4. 在“新建用户”对话框中，执行以下步骤：
   
    ![创建 @Task 测试用户][21] 
   
    a. 在“名字”文本框中，键入“Britta”。
   
    b. 在“姓氏”文本框中，键入“Simon”。
   
    c. 在“电子邮件地址”文本框中，键入 Britta Simon 在 Azure Active Directory 中的电子邮件地址。
   
    d.单击“下一步”。 单击“添加用户”。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户
本部分旨在通过授予 Britta Simon 访问 @Task 的权限，支持她使用 Azure 单一登录。

![分配用户][200] 

**若要将 Britta Simon 分配到 @Task，请执行以下步骤：**

1. 在 Azure 经典门户中，若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![分配用户][201] 
2. 在应用程序列表中，选择 **@Task**。
   
    ![分配用户][202] 
3. 在顶部菜单中，单击“用户”。
   
    ![分配用户][203] 
4. 在“用户”列表中，选择“Britta Simon”。
5. 在底部工具栏中，单击“分配”。
   
    ![分配用户][205]

### <a name="testing-single-sign-on"></a>测试单一登录
本部分旨在使用“访问面板”测试 Azure AD 单一登录配置。  
当在访问面板中单击 @Task 磁贴时，应该会自动登录 @Task 应用程序。

## <a name="additional-resources"></a>其他资源
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-attask-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-attask-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-attask-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-attask-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_01.png
[30]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_02.png


[6]: ./media/active-directory-saas-attask-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_03.png
[8]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_04.png
[9]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_05.png
[10]: ./media/active-directory-saas-attask-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-attask-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-attask-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_08.png


[23]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_06.png

[200]: ./media/active-directory-saas-attask-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-attask-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_09.png
[203]: ./media/active-directory-saas-attask-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-attask-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-attask-tutorial/tutorial_general_205.png






