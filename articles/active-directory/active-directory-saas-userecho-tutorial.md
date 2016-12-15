---
title: "教程：Azure Active Directory 与 UserEcho 的集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 UserEcho 之间配置单一登录。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: bedd916b-8f69-4b50-9b8d-56f4ee3bd3ed
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a59878b0cb6f33af27f5008faf828cd4eb0eb537


---
# <a name="tutorial-azure-active-directory-integration-with-userecho"></a>教程：Azure Active Directory 与 UserEcho 的集成
本教程旨在演示如何集成 UserEcho 与 Azure Active Directory (Azure AD)。  
将 UserEcho 与 Azure AD 集成可提供以下优势： 

* 可在 Azure AD 中控制谁有权访问 UserEcho 
* 可让用户通过其 Azure AD 帐户自动登录 UserEcho（单一登录）
* 可在一个中心位置（即 Azure 经典门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件
若要配置 Azure AD 与 UserEcho 的集成，需要以下项：

* Azure AD 订阅
* 已启用 UserEcho 单一登录的订阅

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

1. 从库添加 UserEcho 
2. 配置和测试 Azure AD 单一登录

## <a name="adding-userecho-from-the-gallery"></a>从库添加 UserEcho
若要配置 UserEcho 与 Azure AD 的集成，需要从库中将 UserEcho 添加到托管 SaaS 应用列表。

**若要从库添加 UserEcho，请按以下步骤操作：**

1. 在 **Azure 经典门户**的左侧导航窗格上，单击“Active Directory”。 
   
    ![Active Directory][1]
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序][2]
4. 在页面底部单击“添加”。
   
    ![应用程序][3]
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![应用程序][4]
6. 在搜索框中，键入 **UserEcho**。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_01.png)
7. 在结果窗格中，选择“UserEcho”，然后单击“完成”以添加应用程序。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
本部分旨在说明如何以名为“Britta Simon”的测试用户为基础，配置并测试 UserEcho 的 Azure AD 单一登录。

若要单一登录能正常工作，Azure AD 需要了解 UserEcho 中与 Azure AD 中相对应的用户。 换句话说，需要在 Azure AD 用户与 UserEcho 中的相关用户间建立链接关系。  
通过将 Azure AD 中“用户名”的值指定为 UserEcho 中 **Username** 的值，建立此链接关系。

若要配置和测试 UserEcho 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 UserEcho 测试用户](#creating-a-userecho-test-user)** - 在 UserEcho 中创建与 Britta Simon 相对应的用户，且该用户与 Azure AD 中表示 Britta Simon 的用户相链接。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录
本部分的目的是在 Azure 经典门户中启用 Azure AD 单一登录，并在 UserEcho 应用程序中配置单一登录。 

**若要配置 UserEcho 的 Azure AD 单一登录，请按以下步骤操作：**

1. 在 Azure 经典门户中的 **UserEcho** 应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
    ![配置单一登录][6] 
2. 在“你希望用户如何登录到 UserEcho”页上，选择“Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_03.png) 
3. 在“配置应用设置”对话框页上，执行以下步骤：
   
    ![配置单一登录](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_04.png) 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“登录 URL”文本框中，键入用户用来登录 UserEcho 应用程序的 URL（例如：*https://fabrikam.UserEcho.com/*）。
   
    b. 单击“下一步”。
4. 在“配置 UserEcho 的单一登录”页，执行以下步骤：
   
    ![配置单一登录](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_05.png) 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 单击“下载证书”，然后将文件保存在计算机上。
   
    b. 单击“下一步”。
5. 在另一浏览器窗口中，以管理员身份登录到 UserEcho 公司站点。
6. 在顶部工具栏中，单击用户名展开菜单，然后单击“设置”。
   
    ![配置单一登录](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_06.png) 
7. 单击“集成”。
   
    ![配置单一登录](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_07.png) 
8. 单击“网站”，然后单击“单一登录 (SAML2)”。
   
    ![配置单一登录](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_08.png) 
9. 在“单一登录 (SAML)”页上，执行以下步骤：
   
    ![配置单一登录](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_09.png) 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 至于“SAML 启用”，请选择“是”。 
   
    b. 在 Azure 经典门户的“配置 UserEcho 的单一登录”对话框页上，复制“单一登录服务 URL”值，然后将其粘贴到“SAML SSO URL”文本框中。
   
    c. 在 Azure 经典门户的“配置 UserEcho 的单一登录”对话框页上，复制“远程注销 URL”值，然后将其粘贴到“远程注销 URL”文本框中。 
   
    d.单击“下一步”。 在记事本中打开下载的证书，复制其内容，然后将其粘贴到“X.509 证书”文本框中。    
   
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 单击“保存” 。
10. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“下一步”。 
    
     ![Azure AD 单一登录][10]
11. 在“单一登录确认”页上，单击“完成”。  
    
     ![Azure AD 单一登录][11]

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 经典门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][20]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-userecho-tutorial/create_aaduser_09.png)  
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要显示用户列表，请在顶部菜单中，单击“用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-userecho-tutorial/create_aaduser_03.png) 
4. 若要打开“添加用户”对话框，请在底部工具栏中单击“添加用户”。 
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-userecho-tutorial/create_aaduser_04.png) 
5. 在“告诉我们有关此用户的信息”对话框页上，执行以下步骤： 
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-userecho-tutorial/create_aaduser_05.png)  
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 对于“用户类型”，选择“组织中的新用户”。
   
    b. 在“用户名”文本框中，键入“BrittaSimon”。
   
    c. 单击“资源组名称” 的 Azure 数据工厂。
6. 在“用户配置文件”对话框页上，执行以下步骤： 
   
   ![创建 Azure AD 测试用户](./media/active-directory-saas-userecho-tutorial/create_aaduser_06.png) 
   
   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名字”文本框中，键入“Britta”。  
   
   b. 在“姓氏”文本框中，键入“Simon”。
   
   c. 在“显示名称”文本框中，键入“Britta Simon”。
   
   d.单击“下一步”。 在“角色”列表中，选择“用户”。
   e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 单击“资源组名称” 的 Azure 数据工厂。
7. 在“获取临时密码”对话框页上，单击“创建”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-userecho-tutorial/create_aaduser_07.png) 
8. 在“获取临时密码”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-userecho-tutorial/create_aaduser_08.png) 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 写下“新密码”的值。
   
    b. 单击“完成”。   

### <a name="creating-a-userecho-test-user"></a>创建 UserEcho 测试用户
本部分的目的是在 UserEcho 中创建名为 Britta Simon 的用户。

**若要在 UserEcho 中创建名为 Britta Simon 的用户，请执行以下步骤：**

1. 以管理员身份登录到 UserEcho 公司站点。
2. 在顶部工具栏中，单击用户名展开菜单，然后单击“设置”。
   
    ![配置单一登录](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_06.png) 
3. 单击“用户”，展开“用户”部分。
   
    ![配置单一登录](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_10.png) 
4. 单击“用户”。
   
    ![配置单一登录](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_11.png) 
5. 单击“邀请新用户”。
   
    ![配置单一登录](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_12.png)
6. 在“邀请新用户”对话框中，执行以下步骤：
   
    ![配置单一登录](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_13.png) 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名称”文本框中，键入 **Britta Simon**。
   
    b. 在“电子邮件”文本框中，键入 Britta 在 Azure 经典门户中的电子邮件地址。
   
    c. 单击“邀请”。

将向 Britta 发送邀请，通过此邀请她可开始使用 UserEcho。 

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户
本部分旨在通过授予 Britta Simon 访问 UserEcho 的权限，允许她使用 Azure 单一登录。

![分配用户][200] 

**若要将 Britta Simon 分配到 UserEcho，请执行以下步骤：**

1. 在 Azure 经典门户中，若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![分配用户][201] 
2. 在应用程序列表中，选择“UserEcho”。
   
    ![配置单一登录](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_50.png) 
3. 在顶部菜单中，单击“用户”。
   
    ![分配用户][203] 
4. 在“用户”列表中，选择“Britta Simon”。
5. 在底部工具栏中，单击“分配”。
   
    ![分配用户][205]

### <a name="testing-single-sign-on"></a>测试单一登录
本部分的目的是使用“访问面板”测试 Azure AD 单一登录配置。  
单击“访问面板”中的 UserEcho 磁贴时，应会自动登录到 UserEcho 应用程序。

## <a name="additional-resources"></a>其他资源
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_205.png









<!--HONumber=Nov16_HO3-->


