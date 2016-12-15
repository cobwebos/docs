---
title: "教程：Azure Active Directory 与 Hightail 集成| Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 Hightail 之间配置单一登录。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: e15206ac-74b0-46e4-9329-892c7d242ec0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: d56e03d45969f80e8f1121741c0342268efb3ca0


---
# <a name="tutorial-azure-active-directory-integration-with-hightail"></a>教程：Azure Active Directory 与 Hightail 集成
本教程的目的是说明如何将 Hightail 与 Azure Active Directory (Azure AD) 集成。

将 Hightail 与 Azure AD 集成提供以下优势：

* 可在 Azure AD 中控制谁有权访问 Hightail
* 可以让用户使用其 Azure AD 帐户自动登录到 Hightail（单一登录）
* 可在一个中心位置（即 Azure 经典门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件
若要配置 Azure AD 与 Hightail 的集成，需要以下项：

* Azure AD 订阅
* 已启用 Hightail 单一登录的订阅

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

1. 从库中添加 Hightail
2. 配置和测试 Azure AD 单一登录

## <a name="adding-hightail-from-the-gallery"></a>从库中添加 Hightail
若要配置 Hightail 与 Azure AD 的集成，需要从库中将 Hightail 添加到托管 SaaS 应用列表。

**若要从库中添加 Hightail，请执行以下步骤：**

1. 在 **Azure 经典门户**的左侧导航窗格上，单击“Active Directory”。 
   
    ![Active Directory][1]
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序][2]
4. 在页面底部单击“添加”。
   
    ![应用程序][3]
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![应用程序][4]
6. 在搜索框中，键入“Hightail”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_01.png)
7. 在结果窗格中，选择“Hightail”，然后单击“完成”以添加该应用程序。
   
    ![在库中选择应用](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
本部分的目的是说明如何基于一个名为“Britta Simon”的测试用户使用 Hightail 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Hightail 用户。 换句话说，需要在 Azure AD 用户与 Hightail 中相关用户之间建立链接关系。

通过将 Azure AD 中“用户名”的值分配为 Hightail 中“用户名”的值来建立此链接关系。

若要使用 Hightail 配置和测试 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Hightail 测试用户](#creating-a-hightail-test-user)** - 在 Hightail 中创建 Britta Simon 的对应者，链接到她的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录
本部分的目的是在 Azure 经典门户中启用 Azure AD 单一登录并在 Hightail 应用程序中配置单一登录。

Hightail 应用程序需要采用特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可从应用程序的“属性”选项卡管理这些属性的值。 以下屏幕截图显示一个示例。 

![配置单一登录](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_51.png) 

**若要使用 Hightail 配置 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 经典门户中的“Hightail”应用程序集成页上，单击顶部菜单中的“属性”。
   
    ![配置单一登录](./media/active-directory-saas-hightail-tutorial/tutorial_general_81.png) 
2. 在“SAML 令牌属性”对话框中，对于下表中显示的每个行，执行以下步骤：
   
   | 属性名称 | 属性值 |
   | --- | --- |
   | FirstName |user.givenname |
   | LastName |user.surname |
   | 电子邮件 |user.mail |
   | UserIdentity |user.mail |
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 单击“添加用户属性”，打开“添加用户属性”对话框。
   
    ![配置单一登录](./media/active-directory-saas-hightail-tutorial/tutorial_general_82.png) 

    b. 在“属性名称”文本框中，键入为该行显示的属性名称。

    c. 在“属性值”列表中，选择为该行显示的属性值。

    d.单击“下一步”。 单击“完成”。    




1. 在顶部菜单中，单击“快速启动”。
   
    ![配置单一登录](./media/active-directory-saas-hightail-tutorial/tutorial_general_83.png)  
2. 在“你希望用户如何登录 Hightail”页上，选择“Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_03.png) 
3. 在“配置应用设置”对话框页上，如果要在“IDP 发起的模式”下配置应用程序，请执行以下步骤，然后单击“下一步”：
   
    ![配置单一登录](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_04.png) 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“回复 URL”文本框中，使用以下模式键入 URL：**"https://www.hightail.com/samlLogin?phi_action=app/samlLogin&subAction=handleSamlResponse"**

    b. 单击“下一步”

1. 如果要在“配置应用设置”对话框页上以“SP 发起的模式”配置应用程序，则单击“显示高级设置(可选)”，然后输入“登录 URL”，并单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_06.png) 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“登录 URL”文本框中，使用以下模式键入用户用于登录 Hightail 应用程序的 URL：**https://www.hightail.com/loginSSO**。 这是要使用 SSO 的所有客户的常见登录页。
   
    b. 单击“下一步”
2. 在“配置 Hightail 的单一登录”页上，执行以下步骤，然后单击“下一步”：
   
    ![配置单一登录](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_05.png) 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 单击“下载证书”，然后将 base-64 编码的证书文件保存在计算机上。

    b. 单击“资源组名称” 的 Azure 数据工厂。

    > [AZURE.NOTE] 在 Hightail 应用上配置单一登录之前，请将 Hightail 团队的电子邮件域添加到允许列表，以便使用该域的所有用户可以利用单一登录功能。

1. 若要为应用程序配置 SSO，需要以管理员身份登录到 Hightail 租户。
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在顶部菜单中，单击“帐户”选项卡，然后选择“配置 SAML”。
   
    ![配置单一登录](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_001.png) 

    b. 选中“启用 SAML 身份验证”复选框。

    ![配置单一登录](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_002.png) 

    c. 在记事本中打开 base-64 编码的证书，将其内容复制到剪贴板，然后将其粘贴到“SAML 令牌签名证书”文本框。

    ![配置单一登录](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_003.png) 


    d.单击“下一步”。 将 Azure AD 中的“远程登录 URL”复制到 Hightail 中的“SAML 授权机构(标识提供者)”。

    ![配置单一登录](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_005.png)

    ![配置单一登录](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_004.png)

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 如果要在“IDP 发起的模式”下配置应用程序，请选择“标识提供者(IdP)发起的登录”。 如果使用“SP 发起的模式”，请选择“服务提供程序(SP)发起的登录”。

    ![配置单一登录](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_006.png)

    f. 复制实例的 SAML 使用者 URL 并将其粘贴到“回复 URL”文本框中，如步骤 4 中所示。 

    g. 单击“保存” 。



1. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“下一步”。
   
    ![Azure AD 单一登录][10]
2. 在“单一登录确认”页上，单击“完成”。 
   
    ![Azure AD 单一登录][11]

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 经典门户中创建名为“Britta Simon”的测试用户。

在“用户列表”中，选择“Britta Simon”。

![创建 Azure AD 用户][20]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-hightail-tutorial/create_aaduser_09.png) 
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要显示用户列表，请在顶部菜单中，单击“用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-hightail-tutorial/create_aaduser_03.png) 
4. 若要打开“添加用户”对话框，请在底部工具栏中单击“添加用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-hightail-tutorial/create_aaduser_04.png)
5. 在“告诉我们有关此用户的信息”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-hightail-tutorial/create_aaduser_05.png) 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 对于“用户类型”，选择“组织中的新用户”。
   
    b. 在“用户名”文本框中，键入“BrittaSimon”。
   
    c. 单击“资源组名称” 的 Azure 数据工厂。
6. 在“用户配置文件”对话框页上，执行以下步骤：
   
   ![创建 Azure AD 测试用户](./media/active-directory-saas-hightail-tutorial/create_aaduser_06.png) 
   
   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名字”文本框中，键入“Britta”。  
   
   b. 在“姓氏”文本框中，键入“Simon”。
   
   c. 在“显示名称”文本框中，键入“Britta Simon”。
   
   d.单击“下一步”。 在“角色”列表中，选择“用户”。
   
   e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 单击“资源组名称” 的 Azure 数据工厂。
7. 在“获取临时密码”对话框页上，单击“创建”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-hightail-tutorial/create_aaduser_07.png) 
8. 在“获取临时密码”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-hightail-tutorial/create_aaduser_08.png) 
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 写下“新密码”的值。
   
    b. 单击“完成”。   

### <a name="creating-a-hightail-test-user"></a>创建 Hightail 测试用户
本部分的目的是在 Hightail 中创建名为“Britta Simon”的用户。 

本部分不存在任何操作项。 Hightail 支持基于自定义声明进行实时用户预配。 如果已配置自定义声明（如前面的**[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)**部分中所述），将自动在应用程序中创建尚不存在的用户。 

> [!NOTE]
> 如果需要手动创建用户，则需要通过 [support@hightail.com](mailto:support@hightail.com) 联系 Hightail 支持团队。
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户
本部分的目的是通过授予 Britta Simon 访问 Hightail 的权限，允许她使用 Azure 单一登录。

![分配用户][200] 

**若要将 Britta Simon 分配到 Hightail，请执行以下步骤：**

1. 在 Azure 经典门户中，若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![分配用户][201] 
2. 在应用程序列表中，选择“Hightail”。
   
    ![配置单一登录](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_50.png) 
3. 在顶部菜单中，单击“用户”。
   
    ![分配用户][203]
4. 在“用户”列表中，选择“Britta Simon”。
5. 在底部工具栏中，单击“分配”。

![分配用户][205]

### <a name="testing-single-sign-on"></a>测试单一登录
本部分的目的是使用“访问面板”测试 Azure AD 单一登录配置。

单击访问面板中的 Hightail 磁贴时，你应自动登录到 Hightail 应用程序。

## <a name="additional-resources"></a>其他资源
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->


