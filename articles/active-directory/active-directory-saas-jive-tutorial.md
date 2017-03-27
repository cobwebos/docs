---
title: "教程：Azure Active Directory 与 Jive 集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 Jive 之间配置单一登录。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 9fc5659a-c116-4a1b-a601-333325a26b46
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 6a01f53ce05aa8084f0a18e56714b1790cfce912
ms.openlocfilehash: 304ee87e82ce5acc4479fd16d3ac1aa340e42815


---
# <a name="tutorial-azure-active-directory-integration-with-jive"></a>教程：Azure Active Directory 与 Jive 集成
在本教程中，了解如何将 Jive 与 Azure Active Directory (Azure AD) 集成。

将 Jive 与 Azure AD 集成提供以下优势：

* 可在 Azure AD 中控制谁有权访问 Jive
* 可以让用户使用其 Azure AD 帐户自动登录到 Jive（单一登录）
* 可在一个中心位置（即 Azure 经典门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件
若要配置 Azure AD 与 Jive 的集成，需要以下项：

* Azure AD 订阅
* 已启用 Jive 单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。
> 
> 

测试本教程中的步骤应遵循以下建议：

* 不应使用生产环境，除非有此必要。
* 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，在测试环境中测试 Azure AD 单一登录。

本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Jive
2. 配置和测试 Azure AD 单一登录

## <a name="adding-jive-from-the-gallery"></a>从库中添加 Jive
若要配置 Jive 与 Azure AD 的集成，需要从库中将 Jive 添加到托管 SaaS 应用列表。

**若要从库中添加 Jive，请执行以下步骤：**

1. 在 **Azure 经典门户**的左侧导航窗格上，单击“Active Directory”。
   
    ![Active Directory][1]
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序][2]
4. 在页面底部单击“添加”。
   
    ![应用程序][3]
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![应用程序][4]
6. 在搜索框中，键入“Jive”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-jive-tutorial/tutorial_jive_01.png)
7. 在结果窗格中，选择“Jive”，然后单击“完成”以添加该应用程序。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-jive-tutorial/tutorial_jive_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，基于一个名为“Britta Simon”的测试用户使用 Jive 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Jive 用户。 换句话说，需要在 Azure AD 用户与 Jive 中相关用户之间建立链接关系。

通过将 Azure AD 中“用户名”的值分配为 Jive 中“用户名”的值来建立此链接关系。

若要使用 Jive 配置和测试 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Jive 测试用户](#creating-a-jive-test-user)** - 在 Jive 中创建 Britta Simon 的对应者，链接到她的 Azure AD 表示形式。
4. **[配置用户预配](#configuring-user-provisioning)** - 概述如何对 Jive 启用 Active Directory 用户帐户的用户预配。
5. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
6. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录
本部分的目的是在 Azure 经典门户中启用 Azure AD 单一登录并在 Jive 应用程序中配置单一登录。

**若要使用 Jive 配置 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 经典门户中的“Jive”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
    ![配置单一登录][6] 
2. 在“你希望用户如何登录 Jive”页上，选择“Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-jive-tutorial/tutorial_jive_03.png) 
3. 在“配置应用设置”对话框页上，执行以下步骤：
   
    ![配置单一登录](./media/active-directory-saas-jive-tutorial/tutorial_jive_04.png) 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“登录 URL”文本框中，使用以下模式键入用户用于登录 Jive 应用程序的 URL：**https://\<customer name\>.jivecustom.com**。
   
    b. 单击“下一步”
4. 在“配置 Jive 的单一登录”页上，执行以下步骤：
   
    ![配置单一登录](./media/active-directory-saas-jive-tutorial/tutorial_jive_05.png)
   
    a. 单击“下载证书”，然后将文件保存在计算机上。
   
    b. 单击“下一步”。
5. 以管理员身份登录到 Jive 租户。
6. 在顶部菜单中，单击“Saml”。
   
    ![在应用端配置单一登录](./media/active-directory-saas-jive-tutorial/tutorial_jive_002.png)
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“常规”选项卡下选择“启用”。
   
    b. 单击“保存所有 saml 设置”按钮。
7. 导航到“Idp 元数据”选项卡。
   
    ![在应用端配置单一登录](./media/active-directory-saas-jive-tutorial/tutorial_jive_003.png)
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 复制下载的元数据 XML 文件的内容，然后将其粘贴到“标识提供者(IDP)元数据”文本框中。
   
    b. 单击“保存所有 saml 设置”按钮。 
8. 转到“用户属性映射”选项卡。
   
    ![在应用端配置单一登录](./media/active-directory-saas-jive-tutorial/tutorial_jive_004.png)
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“电子邮件”文本框中，复制并粘贴“mail”值的属性名称。
   
    b. 在“名字”文本框中，复制并粘贴“givenname”值的属性名称。
   
    c. 在“姓氏”文本框中，复制并粘贴“surname”值的属性名称。
9. 在 Azure AD 门户中，选择“单一登录配置确认”，然后单击“下一步”。
   ![Azure AD 单一登录][10]
10. 在“单一登录确认”页上，单击“完成”。  
    ![Azure AD 单一登录][11]

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
在本部分中，在经典门户中创建名为“Britta Simon”的测试用户。

![创建 Azure AD 用户][20]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-jive-tutorial/create_aaduser_09.png) 
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要显示用户列表，请在顶部菜单中，单击“用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-jive-tutorial/create_aaduser_03.png) 
4. 若要打开“添加用户”对话框，请在底部工具栏中单击“添加用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-jive-tutorial/create_aaduser_04.png) 
5. 在“告诉我们有关此用户的信息”对话框页中，执行以下步骤： ![创建 Azure AD 测试用户](./media/active-directory-saas-jive-tutorial/create_aaduser_05.png) 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 对于“用户类型”，选择“组织中的新用户”。
   
    b. 在“用户名”文本框中，键入“BrittaSimon”。
   
    c. 单击“下一步”。
6. 在“用户配置文件”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-jive-tutorial/create_aaduser_06.png) 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名字”文本框中，键入“Britta”。  
   
    b. 在“姓氏”文本框中，键入“Simon”。
   
    c. 在“显示名称”文本框中，键入“Britta Simon”。
   
    d.单击“下一步”。 在“角色”列表中，选择“用户”。
   
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 单击“下一步”。
7. 在“获取临时密码”对话框页上，单击“创建”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-jive-tutorial/create_aaduser_07.png) 
8. 在“获取临时密码”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-jive-tutorial/create_aaduser_08.png) 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 写下“新密码”的值。
   
    b. 单击“完成”。   

### <a name="creating-a-jive-test-user"></a>创建 Jive 测试用户
在本部分中，将在 Jive 中创建一个名为“Britta Simon”的用户。 请与 Jive 支持团队协作，将用户添加到 Jive 平台中。

### <a name="configuring-user-provisioning"></a>配置用户设置
本部分的目的是概述如何对 Jive 启用 Active Directory 用户帐户的用户预配。  
在此过程中，你将需要提供从 Jive.com 发出请求所需的用户安全令牌。

下面的屏幕截图显示了 Azure AD 中相关对话框的示例：

![配置用户设置](./media/active-directory-saas-jive-tutorial/IC698794.png "配置用户设置")

#### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要配置用户设置，请执行以下步骤：
1. 在 Azure 管理门户中的“Jive”应用程序集成页上，单击“配置用户预配”，打开“配置用户预配”对话框。
2. 在“输入用于启用自动用户预配的 Jive 凭据”页上，提供以下配置设置：
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“Jive 管理员用户名”文本框中，键入在 Jive.com 中已分配**系统管理员**配置文件的 Jive 帐户名称。
   
    b.保留“数据库类型”设置，即设置为“共享”。 在“Jive 管理员密码”文本框中，键入此帐户的密码。
   
    c. 在“Jive 租户 URL”文本框中，键入 Jive 租户 URL。
      
      > [!NOTE]
      > Jive 租户 URL 是组织用于登录到 Jive 的 URL。  
      > 通常，该 URL 具有以下格式：**www.\<organization\>.jive.com**。
      > 
      > 
   
    d.单击“下一步”。 单击“验证”以验证配置。

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 单击“下一步”按钮以打开“确认”页。

3. 在“确认”页上，单击复选标记以保存配置。

现在可以创建一个测试帐户，等待 10 分钟，然后验证该帐户是否已同步到 Jive.com。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户
在本部分中，通过授予 Britta Simon 访问 Jive 的权限，允许她使用 Azure 单一登录。

![分配用户][200] 

**若要将 Britta Simon 分配到 Jive，请执行以下步骤：**

1. 在经典门户中，若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![分配用户][201] 
2. 在应用程序列表中，选择“Jive”。
   
    ![配置单一登录](./media/active-directory-saas-jive-tutorial/tutorial_jive_50.png) 
3. 在顶部菜单中，单击“用户”。
   
    ![分配用户][203]
4. 在“用户”列表中，选择“Britta Simon”。
5. 在底部工具栏中，单击“分配”。
   
    ![分配用户][205]

### <a name="testing-single-sign-on"></a>测试单一登录
在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Jive 磁贴时，你应自动登录到 Jive 应用程序。

## <a name="additional-resources"></a>其他资源
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-jive-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-jive-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-jive-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-jive-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-jive-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-jive-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-jive-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-jive-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-jive-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-jive-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-jive-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-jive-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-jive-tutorial/tutorial_general_205.png



<!--HONumber=Feb17_HO3-->


