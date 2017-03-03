---
title: "教程：Azure Active Directory 与 SD Elements 集成 | Microsoft 文档"
description: "了解如何在 Azure Active Directory 和 SD Elements 之间配置单一登录。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: f0386307-bb3b-4810-8d4b-d0bfebda04f4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: b2eb87a88721a3fd1ecdfa573c6a6d49ed02d344
ms.openlocfilehash: 7166f4d3046f573d2f60d747edf2eefbbd0439c2
ms.lasthandoff: 02/16/2017


---
# <a name="tutorial-azure-active-directory-integration-with-sd-elements"></a>教程：Azure Active Directory 与 SD Elements 集成
本教程的目的是说明如何将 SD Elements 与 Azure Active Directory (Azure AD) 集成。  
将 SD Elements 与 Azure AD 集成提供以下优势：

* 可在 Azure AD 中控制谁有权访问 SD Elements
* 可以让用户使用其 Azure AD 帐户自动登录到 SD Elements（单一登录）
* 可以在一个中心位置（即 Azure Active Directory）管理帐户 

如果想要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [What is application access and single sign-on with Azure Active Directory](active-directory-appssoaccess-whatis.md)（什么是使用 Azure Active Directory 的应用程序访问和单一登录）。

## <a name="prerequisites"></a>先决条件
若要配置 Azure AD 与 SD Elements 的集成，需备齐以下项目：

* Azure AD 订阅
* 已启用 SD Elements 单一登录的订阅

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

1. 从库中添加 SD Elements
2. 配置并测试 Azure AD 单一登录

## <a name="adding-sd-elements-from-the-gallery"></a>从库中添加 SD Elements
若要配置 SD Elements 与 Azure AD 的集成，需要从库中将 SD Elements 添加到托管 SaaS 应用列表。

**若要从库中添加 SD Elements，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。 
   
    ![Active Directory][1]

2. 从“目录”列表中，选择要为其启用目录集成的目录。

3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序][2]

4. 在页面底部单击“添加”。
   
    ![应用程序][3]

5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![应用程序][4]

6. 在搜索框中，键入“SD Elements”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_01.png)

7. 在结果窗格中，选择“SD Elements”，然后单击“完成”以添加该应用程序。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置并测试 Azure AD 单一登录
本部分的目的是说明如何基于名为“Britta Simon”的测试用户配置和测试 SD Elements 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 SD Elements 用户。 换句话说，需要在 Azure AD 用户与 SD Elements 中相关用户之间建立链接关系。  
通过将 Azure AD 中“用户名”的值分配为 SD Elements 中“用户名”的值来建立此链接关系。

若要配置和测试 SD Elements 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-single-sign-on)** - 让用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 SD Elements 测试用户](#creating-a-sd-elements-test-user)** - 在 SD Elements 中创建 Britta Simon 的对应用户，将其链接到她的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录
本部分的目的是在 Azure 经典门户中启用 Azure AD 单一登录并在 SD Elements 应用程序中配置单一登录。

SD Elements 应用程序需要特定格式的 SAML 断言，这要求向 **SAML 令牌属性**配置添加自定义属性映射。 以下屏幕截图显示了一个示例：

![配置单一登录](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_14.png) 

**若要配置 SD Elements 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 经典门户中的“SD Elements”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
    ![配置单一登录][6] 

2. 在“你希望用户如何登录 SD Elements”页上，选择“Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_03.png) 

3. 在“配置应用设置”对话框页上，执行以下步骤：
   
    ![配置单一登录](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_04.png) 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“颁发者”文本框中，使用以下模式键入租户的颁发者 URL：*https://\<租户名称\>.sdelements.com/sso/saml2/metadata*

    b.保留“数据库类型”设置，即设置为“共享”。 在“回复 URL”文本框中，使用以下模式键入租户的回复 URL：*https://\<租户名称\>.sdelements.com/sso/saml2/acs/*       

    > [!NOTE] 
    > 如果需要租户的实际颁发者 URL 和回复 URL，请联系 [SD Elements 支持团队](mailto:support@sdelements.com)。

    c. 单击“资源组名称” 的 Azure 数据工厂。


1. 在“配置 SD Elements 的单一登录”页上，执行以下步骤：
   
    ![配置单一登录](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_05.png) 
   
    a. 单击“下载证书”，然后将文件保存在计算机上。
   
    b. 单击“资源组名称” 的 Azure 数据工厂。
2. 若要启用单一登录，请联系 [SD Elements 支持团队](mailto:support@sdelements.com)并向其提供下载的证书文件。
3. 在另一个 Web 浏览器窗口中，以管理员身份登录 SD Elements 租户。
4. 在顶部菜单中，单击“系统”，然后单击“单一登录”。 
   
    ![配置单一登录](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_09.png) 
5. 在“单一登录设置”对话框上，执行以下步骤：
   
    ![配置单一登录](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_10.png) 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 对于“SSO 类型”，选择“SAML”。
   
    b.保留“数据库类型”设置，即设置为“共享”。 在 Azure 经典门户的“配置 SD Elements 的单一登录”对话框页面上，复制“颁发者 URL”值，然后将其粘贴到“标识提供者实体 ID”文本框。
   
    c. 在 Azure 经典门户的“配置 SD Elements 的单一登录”对话框上，复制“单一登录服务 URL”值，然后将其粘贴到“标识提供程序单一登录服务”文本框。
   
    d.单击“下一步”。 单击“保存”。
6. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“下一步”。
   
    ![Azure AD 单一登录][10]
7. 在“单一登录确认”页上，单击“完成”。  
   
    ![Azure AD 单一登录][11]
8. 在顶部菜单中，单击“属性”以打开“SAML 令牌属性”对话框。 
   
    ![配置单一登录][21]
9. 对于下表中的每个行，执行以下步骤：
   
   | 属性名称 | 属性值 |
   | --- | --- |
   | email |user.mail |
   | 名 |user.givenname |
   | 姓 |user.surname |

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 单击“添加用户属性”。 

    ![配置单一登录][23]

    b.保留“数据库类型”设置，即设置为“共享”。 在“属性名称”文本框中，键入**属性名称**，并选择该行显示的属性值作为**属性值**。

    ![配置单一登录][22]

    c. 单击“添加用户属性”。 

    ![配置单一登录][23]

1. 单击“应用更改”。 
   
    ![配置单一登录][24]

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 经典门户中创建名为 Britta Simon 的测试用户。  

![创建 Azure AD 用户][20]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_09.png) 
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要显示用户列表，请在顶部菜单中，单击“用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_03.png) 
4. 若要打开“添加用户”对话框，请在底部工具栏中单击“添加用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_04.png) 
5. 在“告诉我们有关此用户的信息”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_05.png) 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 对于“用户类型”，选择“组织中的新用户”。
   
    b. 在“用户名”文本框中，键入“BrittaSimon”。
   
    c. 单击“下一步”。
6. 在“用户配置文件”对话框页上，执行以下步骤：
   
   ![创建 Azure AD 测试用户](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_06.png) 
   
   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名字”文本框中，键入“Britta”。  
   
   b. 在“姓氏”文本框中，键入“Simon”。
   
   c. 在“显示名称”文本框中，键入“Britta Simon”。
   
   d.单击“下一步”。 在“角色”列表中，选择“用户”。
   
   e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 单击“下一步”。
7. 在“获取临时密码”对话框页上，单击“创建”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_07.png) 
8. 在“获取临时密码”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-sd-elements-tutorial/create_aaduser_08.png) 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 写下“新密码”的值。
   
    b. 单击“完成”。   

### <a name="creating-a-sd-elements-test-user"></a>创建 SD Elements 测试用户
本部分的目的是在 SD Elements 中创建名为“Britta Simon”的用户。 对于 SD Elements，创建 SD Elements 用户是一项手动任务。

**若要在 SD Elements 中创建 Britta Simon，请执行以下步骤：**

1. 在 Web 浏览器窗口中，以管理员身份登录 SD Elements 公司站点。
2. 在顶部菜单中，单击“用户管理”，然后单击“用户”。
   
   ![创建 SD Elements 测试用户](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_11.png) 
3. 单击“添加新用户”。
   
   ![创建 SD Elements 测试用户](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_12.png) 
4. 在“添加新用户”对话框中，执行以下步骤：
   
   ![创建 SD Elements 测试用户](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_13.png) 
   
   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“电子邮件”文本框中，键入 Britta 在 Azure AD 中的电子邮件地址。
   
   b.保留“数据库类型”设置，即设置为“共享”。 在“名字”文本框中，键入“Britta”。
   
   c. 在“姓氏”文本框中，键入“Simon”。
   
   d.单击“下一步”。 选择“用户”作为“角色”。 
   
   e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 单击“创建用户”。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户
本部分的目的是通过授予 Britta Simon 访问 SD Elements 的权限，允许她使用 Azure 单一登录。

![分配用户][200] 

**若要将 Britta Simon 分配到 SD Elements，请执行以下步骤：**

1. 在 Azure 经典门户中，若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![分配用户][201] 
2. 在应用程序列表中，选择“SD Elements”。
   
    ![配置单一登录](./media/active-directory-saas-sd-elements-tutorial/tutorial_sd-elements_50.png) 
3. 在顶部菜单中，单击“用户”。
   
    ![分配用户][203] 
4. 在“用户”列表中，选择“Britta Simon”。
5. 在底部工具栏中，单击“分配”。
   
    ![分配用户][205]

### <a name="testing-single-sign-on"></a>测试单一登录
本部分的目的是使用访问面板测试 Azure AD 单一登录配置。  
单击访问面板中的“SD Elements”磁贴时，用户应自动登录到 SD Elements 应用程序。

## <a name="additional-resources"></a>其他资源
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_100.png

[21]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_80.png
[22]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_82.png
[23]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_81.png
[24]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_83.png


[200]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-sd-elements-tutorial/tutorial_general_205.png

