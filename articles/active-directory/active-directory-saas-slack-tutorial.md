---
title: "教程：Azure Active Directory 与 Slack 集成 | Microsoft Docs"
description: "了解如何将 Slack 与 Azure Active Directory 配合使用以启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: c7a0b761-75b7-4e6b-9980-71d645643a78
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/21/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 4d5144c6a690c2338dec45b27dcb308328b6fecb
ms.openlocfilehash: 37440a8ba397c4dc227a448dfa574cebd14be49c


---

# <a name="tutorial-azure-active-directory-integration-with-slack"></a>教程：Azure Active Directory 与 Slack 集成

本教程的目的是演示如何将 Slack 与 Azure Active Directory (Azure AD) 集成。

将 Slack 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Slack
- 可以让用户使用其 Azure AD 帐户自动登录到 Slack（单一登录）
- 可在一个中心位置（即 Azure 经典门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Slack 的集成，需要具有以下项：

- Azure AD 订阅
- 启用了 Slack 单一登录的订阅


> [!NOTE]
>  不建议使用生产环境测试本教程中的步骤。


测试本教程中的步骤应遵循以下建议：

- 不应使用生产环境，除非有此必要。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。


## <a name="scenario-description"></a>方案描述
本教程的目的是介绍如何在测试环境中测试 Azure AD 单一登录。

本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Slack
2. 配置和测试 Azure AD 单一登录


## <a name="adding-slack-from-the-gallery"></a>从库中添加 Slack
若要配置 Slack 与 Azure AD 的集成，需要从库中将 Slack 添加到托管 SaaS 应用列表。

**若要从库中添加 Slack，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。 

    ![Active Directory][1]

2. 从“目录”列表中，选择要为其启用目录集成的目录。

3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
    
    ![应用程序][2]

4. 在页面底部单击“添加”。
    
    ![应用程序][3]

5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。

    ![应用程序][4]

6. 在搜索框中，键入“Slack”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-slack-tutorial/tutorial_slack_01.png)

7. 在结果面板中，选择“Slack”，然后单击“完成”以添加该应用程序。

    ![在库中选择应用](./media/active-directory-saas-slack-tutorial/tutorial_slack_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
本部分旨在展示如何根据名为“Britta Simon”的测试用户的指令配置和测试 Slack 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Slack 用户。 换句话说，需要建立 Azure AD 用户与 Slack 中相关用户之间的链接关系。

通过将 Azure AD 中“用户名”的值分配为 Slack 中“用户名”的值来建立此链接关系。

若要配置并测试 Slack 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Slack 测试用户](#creating-a-slack-test-user)** - 在 Slack 中创建 Britta Simon 的对应用户，将其链接到其 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

本部分的目的是在 Azure 经典门户中启用 Azure AD 单一登录并在 Slack 应用程序中配置单一登录。

Slack 应用程序需要特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可从应用程序的“属性”选项卡管理这些属性的值。 以下屏幕截图显示一个示例。 

![配置单一登录](./media/active-directory-saas-slack-tutorial/tutorial_slack_09.png)

**若要配置 Slack 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 经典门户中的 **Slack** 应用程序集成页上，单击顶部菜单中的“属性”。

    ![配置单一登录](./media/active-directory-saas-slack-tutorial/tutorial_slack_03.png)

2. 在“SAML 令牌属性”对话框中，对于下表中显示的每个行，执行以下步骤：

    | 属性名称 | 属性值 |
    | --- | --- |    
    | User.Email | user.userprincipalname |
    | first_name | user.givenname |
    | last_name | user.surname |
    | User.Username | extractmailprefix([userprincipalname]) |

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 单击“添加用户属性”，打开“添加用户属性”对话框。

    ![配置单一登录](./media/active-directory-saas-slack-tutorial/tutorial_slack_04.png)
    
    b. 在“属性名称”文本框中，键入为该行显示的属性名称。
    
    c. 在“属性值”列表中，键入为该行显示的属性值。
    
    d.单击“下一步”。 单击“完成”

3. 在顶部菜单中，单击“快速启动”。

    ![配置单一登录](./media/active-directory-saas-slack-tutorial/tutorial_slack_05.png) 

4. 在“你希望用户如何登录 Slack”页上，选择“Azure AD 单一登录”，然后单击“下一步”。
    
    ![配置单一登录](./media/active-directory-saas-slack-tutorial/tutorial_slack_06.png)

5. 在“配置应用设置”对话框页上，执行以下步骤，然后单击“下一步”：

    ![配置单一登录](./media/active-directory-saas-slack-tutorial/tutorial_slack_07.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<company name>.slack.com`。

    b.保留“数据库类型”设置，即设置为“共享”。 单击“资源组名称” 的 Azure 数据工厂。

    > [!NOTE] 
    > 请注意，必须使用实际登录 URL 更新这些值。 若要获取此值，请与 Slack 支持团队联系。

6. 在“配置 Slack 的单一登录”页上，请单击“下载证书”，然后在计算机上保存该文件。

    ![配置单一登录](./media/active-directory-saas-slack-tutorial/tutorial_slack_08.png)

7.  在另一个 Web 浏览器窗口中，以管理员身份登录到 Slack 公司站点。

8.  导航到“Microsoft Azure AD”，然后转到“团队设置”。

    ![在应用端配置单一登录](./media/active-directory-saas-slack-tutorial/tutorial_slack_001.png)

9.  在“团队设置”部分中，单击“身份验证”选项卡，然后单击“更改设置”。

    ![在应用端配置单一登录](./media/active-directory-saas-slack-tutorial/tutorial_slack_002.png)

10. 在“SAML 身份验证设置”对话框中，执行以下步骤：

    ![在应用端配置单一登录](./media/active-directory-saas-slack-tutorial/tutorial_slack_003.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。  在“SAML 2.0 终结点”文本框中放置 Azure AD 应用程序配置向导中 **SAML SSO URL** 的值。

    b.保留“数据库类型”设置，即设置为“共享”。  在“标识提供程序颁发者”文本框中放置 Azure AD 应用程序配置向导中“颁发者 URL”的值。

    c.  在记事本中打开下载的证书，将其内容复制到剪贴板，然后将其粘贴到“公共证书”文本框中。

    d.单击“下一步”。  取消选择“允许用户更改其电子邮件地址”。

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。  选择“允许用户选择自己的用户名”。

    f.  对于“必须对团队进行身份验证”，请选择“可选”。

    g.  单击“保存配置”。

11. 在经典门户中，选择“单一登录配置确认”，然后单击“下一步”。
    
    ![Azure AD 单一登录][10]

12. 在“单一登录确认”页上，单击“完成”。  
    
    ![Azure AD 单一登录][11]



### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在经典门户中创建名为“Britta Simon”的测试用户。

![创建 Azure AD 用户][20]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-slack-tutorial/create_aaduser_09.png)

2. 在“目录”列表中，选择要启用目录集成的目录。

3. 若要显示用户列表，请在顶部菜单中，单击“用户”。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-slack-tutorial/create_aaduser_03.png)

4. 若要打开“添加用户”对话框，请在底部工具栏中单击“添加用户”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-slack-tutorial/create_aaduser_04.png)

5. 在“告诉我们有关此用户的信息”对话框页上，执行以下步骤：

    ![创建 Azure AD 测试用户](./media/active-directory-saas-slack-tutorial/create_aaduser_05.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 对于“用户类型”，选择“组织中的新用户”。

    b. 在“用户名”文本框中，键入“BrittaSimon”。

    c. 单击“资源组名称” 的 Azure 数据工厂。

6.  在“用户配置文件”对话框页上，执行以下步骤：
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-slack-tutorial/create_aaduser_06.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名字”文本框中，键入“Britta”。  

    b. 在“姓氏”文本框中，键入“Simon”。

    c. 在“显示名称”文本框中，键入“Britta Simon”。

    d.单击“下一步”。 在“角色”列表中，选择“用户”。

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 单击“资源组名称” 的 Azure 数据工厂。

7. 在“获取临时密码”对话框页上，单击“创建”。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-slack-tutorial/create_aaduser_07.png)

8. 在“获取临时密码”对话框页上，执行以下步骤：
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-slack-tutorial/create_aaduser_08.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 写下“新密码”的值。

    b. 单击“完成”。   



### <a name="creating-a-slack-test-user"></a>创建 Slack 测试用户

本部分的目的是在 Slack 中创建名为 Britta Simon 的用户。 Slack 支持在默认情况下启用的实时预配。

本部分不存在任何操作项。 尝试访问 Slack 期间，如果该用户尚不存在，则将创建一个新用户。

> [!NOTE] 
> 如果需要手动创建用户，则需联系 Slack 支持团队。


### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

本部分的目的是通过授予 Britta Simon 访问 Slack 的权限，允许她使用 Azure 单一登录。
    
![分配用户][200]

**若要将 Britta Simon 分配到 Slack，请执行以下步骤：**

1. 在经典门户中，若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
    
    ![分配用户][201]

2. 在应用程序列表中，选择“Slack”。
    
    ![配置单一登录](./media/active-directory-saas-slack-tutorial/tutorial_slack_50.png)

3. 在顶部菜单中，单击“用户”。
    
    ![分配用户][203]

4. 在“用户”列表中，选择“Britta Simon”。

5. 在底部工具栏中，单击“分配”。
    
    ![分配用户][205]

### <a name="testing-single-sign-on"></a>测试单一登录

本部分的目的是使用“访问面板”测试 Azure AD 单一登录配置。
 
单击访问面板中的“Slack”磁贴时，用户应自动登录到 Slack 应用程序。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-slack-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-slack-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-slack-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-slack-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-slack-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-slack-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-slack-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-slack-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-slack-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-slack-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-slack-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-slack-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-slack-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO4-->


