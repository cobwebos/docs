---
title: "教程：Azure Active Directory 与 Image Relay 集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 Image Relay 之间配置单一登录。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 65bb5990-07ef-4244-9f41-cd28fc2cb5a2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: a14be44b66c5e61350785ddfaf5b96125a196103
ms.openlocfilehash: 94fdae2992e69f4330d325968a9e66b3f6f38ce0


---
# <a name="tutorial-azure-active-directory-integration-with-image-relay"></a>教程：Azure Active Directory 与 Image Relay 集成
本教程的目的是演示如何将 Image Relay 与 Azure Active Directory (Azure AD) 集成。  
将 Image Relay 与 Azure AD 集成提供以下优势：

* 可在 Azure AD 中控制谁有权访问 Image Relay
* 可以让用户使用其 Azure AD 帐户自动登录到 Image Relay（单一登录）
* 可在一个中心位置（即 Azure 经典门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件
若要配置 Azure AD 与 Image Relay 的集成，需要以下项：

* Azure AD 订阅
* 启用了Image Relay 单一登录的订阅

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

1. 从库中添加 Image Relay
2. 配置和测试 Azure AD 单一登录

## <a name="adding-image-relay-from-the-gallery"></a>从库中添加 Image Relay
若要配置 Image Relay 与 Azure AD 的集成，需要从库中将 Image Relay 添加到托管 SaaS 应用列表。

**若要从库添加 Image Relay，请按以下步骤操作：**

1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。 
   
    ![Active Directory][1]
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序][2]
4. 在页面底部单击“添加”。
   
    ![应用程序][3]
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![应用程序][4]
6. 在搜索框中，键入“Image Relay”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_01.png)
7. 在结果窗格中，选择“Image Relay”，然后单击“完成”以添加该应用程序。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
本部分的目的是说明如何基于一个名为“Britta Simon”的测试用户使用 ImageRelay 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要用于表示 Image Relay 中相关用户的用户帐户。  换句话说，需要建立 Azure AD 用户与 Image Relay 中相关用户之间的链接关系。  
通过将 Azure AD 中“用户名”的值分配为 Image Relay 中“用户名”的值来建立此链接关系。

若要配置和测试 Image Relay 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Image Relay 测试用户](#creating-a-userecho-test-user)** - 在 Image Relay 中创建 Britta Simon 的对应用户，将其链接到其 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录
本部分的目的是在 Azure 经典门户中启用 Azure AD 单一登录并在 Image Relay 应用程序中配置单一登录。

**若要使用 Image Relay 配置 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 经典门户中的“Image Relay”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
     ![配置单一登录][6] 
2. 在“你希望用户如何登录 Image Relay”页上，选择“Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_03.png) 
3. 在“配置应用设置”对话框页上，执行以下步骤：
   
     ![配置单一登录](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_04.png) 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“登录 URL”文本框中，键入用户用于登录 ImageRelay 应用程序的 URL（例如：*https://fabrikam.ImageRelay.com/*）。
   
    b. 单击“下一步”。
4. 在“配置 Image Relay 的单一登录”页，执行以下步骤：
   
    ![配置单一登录](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_05.png) 
   
    a. 单击“下载证书”，然后将文件保存在计算机上。
   
    b. 单击“下一步”。
5. 在另一个浏览器窗口中，以管理员身份登录到 Image Relay 公司站点。
6. 在顶部工具栏中，单击“用户和权限”工作负荷。
   
    ![配置单一登录](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_06.png) 
7. 单击“创建新权限”。
   
    ![配置单一登录](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_08.png) 
8. 在“单一登录设置”工作负荷中，选中“此组只能通过单一登录进行登录”复选框，然后单击“保存”。
   
    ![配置单一登录](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_09.png) 
9. 转到“帐户设置”。
   
    ![配置单一登录](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_10.png) 
10. 转到“单一登录设置”工作负荷。
    
     ![配置单一登录](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_11.png)
11. 在“SAML 设置”对话框中，执行以下步骤：
    
    ![配置单一登录](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_12.png)
    
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在 Azure 经典门户中，复制“单一登录服务 URL”，然后将其粘贴到“登录 URL”文本框中。

    b. 在 Azure 经典门户中，复制“单一注销服务 URL”，然后将其粘贴到“注销 URL”文本框中。

    c. 对于“名称 ID 格式”，选择“urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress”。

    d.单击“下一步”。 对于“来自服务提供商(Image Relay)的请求的绑定选项”，选择“POST 绑定”。

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 在“x.509 证书”下，单击“更新证书”。

    ![配置单一登录](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_17.png)

    f. 在记事本中打开下载的证书，复制其内容，然后将其粘贴到“x.509 证书”文本框中。

    ![配置单一登录](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_18.png)

    g. 在“实时用户预配”部分中，选择“启用实时用户预配”。

    ![配置单一登录](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_19.png)

    h. 选择仅允许通过单一登录进行登录的权限组（例如，**SSO 基本**）。

    ![配置单一登录](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_20.png)

    i. 单击“保存” 。

1. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“下一步”。
   
    ![Azure AD 单一登录][10]
2. 在“单一登录确认”页上，单击“完成”。
   
    ![Azure AD 单一登录][11]

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 经典门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][20]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_09.png) 
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要显示用户列表，请在顶部菜单中，单击“用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_03.png) 
4. 若要打开“添加用户”对话框，请在底部工具栏中单击“添加用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_04.png) 
5. 在“告诉我们有关此用户的信息”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_05.png) 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 对于“用户类型”，选择“组织中的新用户”。
   
    b. 在“用户名”文本框中，键入“BrittaSimon”。
   
    c. 单击“下一步”。
6. 在“用户配置文件”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_06.png) 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名字”文本框中，键入“Britta”。  
   
    b. 在“姓氏”文本框中，键入“Simon”。
   
    c. 在“显示名称”文本框中，键入“Britta Simon”。
   
    d.单击“下一步”。 在“角色”列表中，选择“用户”。
   
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 单击“下一步”。

7. 在“获取临时密码”对话框页上，单击“创建”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_07.png) 
8. 在“获取临时密码”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_08.png) 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 写下“新密码”的值。
   
    b. 单击“完成”。   

### <a name="creating-an-image-relay-test-user"></a>创建 Image Relay 测试用户
本部分的目的是在 Image Relay 中创建名为“Britta Simon”的用户。

**若要在 Image Relay 中创建名为“Britta Simon”的用户，请执行以下步骤：**

1. 以管理员身份登录 Image Relay 公司站点。
2. 转到“用户和权限”，然后选择“创建 SSO 用户”。
   
    ![配置单一登录](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_21.png) 
3. 输入要预配的用户的**电子邮件**、**名字**、**姓氏**和**公司**，并选择只能通过单一登录进行登录的权限组（例如，SSO 基本）。
   
    ![配置单一登录](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_22.png) 
4. 单击“创建” 。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户
本部分的目的是通过授予 Britta Simon 访问 Image Relay 的权限，允许她使用 Azure 单一登录。

![分配用户][200] 

**若要将 Britta Simon 分配到 Image Relay，请执行以下步骤：**

1. 在 Azure 经典门户中，打开应用程序视图，在目录视图的顶部菜单中，单击“应用程序”。
   
    ![分配用户][201] 
2. 在应用程序列表中，选择“Image Relay”。
   
    ![配置单一登录](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_23.png) 
3. 在顶部菜单中，单击“用户”。
   
    ![分配用户][203] 
4. 在“用户”列表中，选择“Britta Simon”。
5. 在底部工具栏中，单击“分配”。
   
    ![分配用户][205]

### <a name="testing-single-sign-on"></a>测试单一登录
本部分的目的是使用“访问面板”测试 Azure AD 单一登录配置。  
单击访问面板中的 Image Relay 磁贴时，应当会自动登录到 Image Relay 应用程序。

## <a name="additional-resources"></a>其他资源
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_205.png



<!--HONumber=Feb17_HO3-->


