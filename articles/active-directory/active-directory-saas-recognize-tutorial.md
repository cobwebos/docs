---
title: "教程：Azure Active Directory 与 Recognize 集成 | Microsoft 文档"
description: "了解如何在 Azure Active Directory 和 Recognize 之间配置单一登录。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: cfad939e-c8f4-45a0-bd25-c4eb9701acaa
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 43e9febd4bc230db69b55ae3f5bb13ff17b2e3f6


---
# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>教程：Azure Active Directory 与 Recognize 集成
本教程的目的是说明如何将 Recognize 与 Azure Active Directory (Azure AD) 集成。

将 Recognize 与 Azure AD 集成提供以下优势：

* 可在 Azure AD 中控制谁有权访问 Recognize
* 可以让用户使用其 Azure AD 帐户自动登录到 Recognize（单一登录）
* 可在一个中心位置（即 Azure 经典门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件
若要配置 Azure AD 与 Recognize 的集成，需要以下项：

* Azure AD 订阅
* 已启用 Recognize 单一登录的订阅

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

1. 从库中添加 Recognize
2. 配置和测试 Azure AD 单一登录

## <a name="adding-recognize-from-the-gallery"></a>从库中添加 Recognize
若要配置 Recognize 与 Azure AD 的集成，需要从库中将 Recognize 添加到托管 SaaS 应用列表。

**若要从库中添加 Recognize，请执行以下步骤：**

1. 在 **Azure 经典门户**的左侧导航窗格上，单击“Active Directory”。 
   
    ![Active Directory][1]
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序][2]
4. 在页面底部单击“添加”。
   
    ![应用程序][3]
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![应用程序][4]
6. 在搜索框中，键入“Recognize”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_01.png)
7. 在结果窗格中，选择“Recognize”，然后单击“完成”以添加该应用程序。
   
    ![在库中选择应用](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_0001.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
本部分的目的是说明如何基于名为“Britta Simon”的测试用户配置和测试 Recognize 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Recognize 用户。 换句话说，需要在 Azure AD 用户与 Recognize 中相关用户之间建立链接关系。

通过将 Azure AD 中“用户名”的值分配为 Recognize 中“用户名”的值来建立此链接关系。

若要配置和测试 Recognize 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-single-sign-on)** - 让用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Recognize 测试用户](#creating-a-recognize-test-user)** - 在 Recognize 中创建 Britta Simon 的对应用户，将其链接到她的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录
在本部分中，将在经典门户中启用 Azure AD 单一登录并在 Recognize 应用程序中配置单一登录。

**若要配置 Recognize 的 Azure AD 单一登录，请执行以下步骤：**

1. 在经典门户中的“Recognize”应用程序集成页上，单击“配置单一登录”打开“配置单一登录”对话框。
   
    ![配置单一登录][6] 
2. 在“你希望用户如何登录 Recognize”页上，选择“Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_03.png)
3. 在“配置应用设置”对话框页上，执行以下步骤，然后单击“下一步”：
   
    ![配置单一登录](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_04.png)
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“登录 URL”文本框中，使用以下模式键入 URL：`https://recognizeapp.com/<your-domain>/saml/sso`。
   
    b.保留“数据库类型”设置，即设置为“共享”。 在“标识符”文本框中，使用以下模式键入 URL：`https://recognizeapp.com/<your-domain>/saml/metadata`。
   
    c. 单击“下一步”
   
   > [!NOTE]
   > 如果不知道这些 URL，请使用示例模式键入示例 URL。 若要获取这些值，可以参考步骤 9 以了解更多详细信息或通过 <mailto:support@recognizeapp.com> 联系 Recognize 支持团队。
   > 
   > 
4. 在“配置 Recognize 的单一登录”页上，请单击“下载证书”，然后在计算机上保存该文件：
   
    ![配置单一登录](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_05.png)
5. 在另一个 Web 浏览器窗口中，以管理员身份登录 Recognize 租户。
6. 在右上角，单击“菜单”。 转到“公司管理员”。
   
    ![在应用端配置单一登录](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_000.png)
7. 在左侧导航窗格上，单击“设置”。
   
    ![在应用端配置单一登录](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_001.png)
8. 在“SSO 设置”部分中执行以下步骤。
   
    ![在应用端配置单一登录](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_002.png)
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 对于“启用 SSO”，选择“启用”。
   
    b.保留“数据库类型”设置，即设置为“共享”。 在“IDP 实体 ID”文本框中放置 Azure AD 应用程序配置向导中**颁发者 URL** 的值。
   
    c. 在“Sso 目标 URL”文本框中放置 Azure AD 应用程序配置向导中**单一登录服务 URL** 的值。
   
    d.单击“下一步”。 在“Slo 目标 URL”文本框中放置 Azure AD 应用程序配置向导中**单一注销服务 URL** 的值。
   
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 在记事本中打开下载的证书，将其内容复制到剪贴板，然后将其粘贴到“证书”文本框中。 
   
    f. 单击“保存设置”按钮。 
9. 在“SSO 设置”部分旁边，复制“服务提供程序元数据 url”下的 URL。
   
    ![在应用端配置单一登录](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_003.png)
10. 在空白浏览器下打开“元数据 URL 链接”以下载元数据文档。 然后，在“配置应用设置”对话框上，将 Recognize 提供的 EntityDescriptor 值用于**标识符**。
    
    ![在应用端配置单一登录](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_004.png)
11. 在经典门户中，选择“单一登录配置确认”，然后单击“下一步”。
    
    ![Azure AD 单一登录][10]
12. 在“单一登录确认”页上，单击“完成”。  
    
    ![Azure AD 单一登录][11]

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在经典门户中创建名为“Britta Simon”的测试用户。

![创建 Azure AD 用户][20]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-recognize-tutorial/create_aaduser_09.png)
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要显示用户列表，请在顶部菜单中，单击“用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-recognize-tutorial/create_aaduser_03.png)
4. 若要打开“添加用户”对话框，请在底部工具栏中单击“添加用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-recognize-tutorial/create_aaduser_04.png)
5. 在“告诉我们有关此用户的信息”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-recognize-tutorial/create_aaduser_05.png)
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 对于“用户类型”，选择“组织中的新用户”。
   
    b. 在“用户名”文本框中，键入“BrittaSimon”。
   
    c. 单击“资源组名称” 的 Azure 数据工厂。
6. 在“用户配置文件”对话框页上，执行以下步骤：
   
   ![创建 Azure AD 测试用户](./media/active-directory-saas-recognize-tutorial/create_aaduser_06.png)
   
   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名字”文本框中，键入“Britta”。  
   
   b. 在“姓氏”文本框中，键入“Simon”。
   
   c. 在“显示名称”文本框中，键入“Britta Simon”。
   
   d.单击“下一步”。 在“角色”列表中，选择“用户”。
   
   e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 单击“资源组名称” 的 Azure 数据工厂。
7. 在“获取临时密码”对话框页上，单击“创建”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-recognize-tutorial/create_aaduser_07.png)
8. 在“获取临时密码”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-recognize-tutorial/create_aaduser_08.png)
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 写下“新密码”的值。
   
    b. 单击“完成”。   

### <a name="creating-a-recognize-test-user"></a>创建 Recognize 测试用户
要使 Azure AD 用户能够登录 Recognize，必须将这些用户预配到 Recognize 中。 对于 Recognize，预配是一项手动任务。

此应用不支持 SCIM 预配，但具有替代的可预配用户的用户同步。 

#### <a name="to-provision-a-user-account-perform-the-following-steps"></a>若要预配用户帐户，请执行以下步骤：
1. 以管理员身份登录 Recognize 公司站点。
2. 在右上角，单击“菜单”。 转到“公司管理员”。
3. 在左侧导航窗格上，单击“设置”。
4. 在“用户同步”部分中，执行以下步骤。
   
   ![新建用户](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_005.png "New User")
   
   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 对于“启用同步”，选择“启用”。
   
   b.保留“数据库类型”设置，即设置为“共享”。 对于“选择同步提供程序”，选择“Microsoft/Office 365”。
   
   c. 单击“运行用户同步”

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户
本部分的目的是通过授予 Britta Simon 访问 Recognize 的权限，允许她使用 Azure 单一登录。

![分配用户][200]

**若要将 Britta Simon 分配到 Recognize，请执行以下步骤：**

1. 在经典门户中，若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![分配用户][201]
2. 在应用程序列表中，选择“Recognize”。
   
    ![配置单一登录](./media/active-directory-saas-recognize-tutorial/tutorial_recognize_50.png)
3. 在顶部菜单中，单击“用户”。
   
    ![分配用户][203]
4. 在“用户”列表中，选择“Britta Simon”。
5. 在底部工具栏中，单击“分配”。
   
    ![分配用户][205]

### <a name="testing-single-sign-on"></a>测试单一登录
本部分的目的是使用“访问面板”测试 Azure AD 单一登录配置。

单击访问面板中的“Recognize”磁贴时，用户应自动登录到 Recognize 应用程序。

## <a name="additional-resources"></a>其他资源
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-recognize-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->


