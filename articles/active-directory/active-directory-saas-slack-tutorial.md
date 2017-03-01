---
title: "教程：Azure Active Directory 与 Slack 集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 Slack 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: ffc5e73f-6c38-4bbb-876a-a7dd269d4e1c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 14972d3e1212fb0cf6653bd4a693470425294d2c
ms.openlocfilehash: c975231ea18c7c1853d9b20fc49542c10ef9abcc


---
# <a name="tutorial-azure-active-directory-integration-with-slack"></a>教程：Azure Active Directory 与 Slack 集成

在本教程中，了解如何将 Slack 与 Azure Active Directory (Azure AD) 集成。

将 Slack 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Slack
- 可以让用户使用其 Azure AD 帐户自动登录到 Slack（单一登录）
- 可在一个中心位置（即 Azure 管理门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Slack 的集成，需要具有以下项：

- Azure AD 订阅
- 启用了 Slack 单一登录的订阅


> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。


测试本教程中的步骤应遵循以下建议：

- 不应使用生产环境，除非有此必要。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。


## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Slack
2. 配置和测试 Azure AD 单一登录


## <a name="adding-slack-from-the-gallery"></a>从库中添加 Slack
若要配置 Slack 与 Azure AD 的集成，需要从库中将 Slack 添加到托管 SaaS 应用列表。

**若要从库中添加 Slack，请执行以下步骤：**

1. 在 **[Azure 管理门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 单击对话框顶部的“添加”按钮。

    ![应用程序][3]

4. 在搜索框中，键入“Slack”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-slack-tutorial/tutorial_slack_000.png)

5. 在结果窗格中，选择“Slack”，然后单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-slack-tutorial/tutorial_slack_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，将基于名为“Britta Simon”的测试用户配置和测试 Slack 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Slack 用户。 换句话说，需要建立 Azure AD 用户与 Slack 中相关用户之间的链接关系。

通过将 Azure AD 中“用户名”的值分配为 Slack 中“用户名”的值来建立此链接关系。

若要配置并测试 Slack 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Slack 测试用户](#creating-a-slack-test-user)** - 在 Slack 中创建 Britta Simon 的对应用户，将其链接到其 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 管理门户中启用 Azure AD 单一登录并在 Slack 应用程序中配置单一登录。

**若要配置 Slack 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 管理门户的“Slack”应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-slack-tutorial/tutorial_slack_01.png)

3. 在“Slack 域和 URL”部分中，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-slack-tutorial/tutorial_slack_02.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<company name>.slack.com`

    b.保留“数据库类型”设置，即设置为“共享”。 在“标识符”文本框中，键入 `https://slack.com`

    > [!NOTE] 
    > 请注意，这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 此处我们建议在“标识符”中使用 URL 的唯一值。 若要获取这些值，请联系 [Slack 支持团队](https://slack.com/help/contact)。 

4. Slack 应用程序需要特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。 以下屏幕截图显示一个示例。
    
    ![配置单一登录](./media/active-directory-saas-slack-tutorial/tutorial_slack_03.png)

5. 在“单一登录”对话框上的“用户属性”部分中，选择“user.mail”作为**用户标识符**，并针对下表中所示的每一行，执行以下步骤：
    
    | 属性名称 | 属性值 |
    | --- | --- |    
    | User.Email | user.userprincipalname |
    | first_name | user.givenname |
    | last_name | user.surname |
    | User.Username | extractmailprefix([userprincipalname]) |

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 单击“添加属性”，打开“添加属性”对话框。

    ![配置单一登录](./media/active-directory-saas-slack-tutorial/tutorial_slack_04.png)

    ![配置单一登录](./media/active-directory-saas-slack-tutorial/tutorial_slack_05.png)
    
    b.保留“数据库类型”设置，即设置为“共享”。 在“名称”文本框中，键入为该行显示的属性名称。
    
    c. 在“值”列表中，选择为该行显示的属性值。
    
    d.单击“下一步”。 单击 **“确定”**

6. 在“SAML 签名证书”部分中，单击“创建新证书”。

    ![配置单一登录](./media/active-directory-saas-slack-tutorial/tutorial_slack_06.png)     

7. 在“创建新证书”对话框中，单击日历图标，然后选择“到期日期”。 然后单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-slack-tutorial/tutorial_general_300.png)

8. 在“SAML 签名证书”部分中，选择“激活新证书”，然后单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-slack-tutorial/tutorial_slack_07.png)

9. 在弹出的“滚动更新证书”窗口中，单击“确定”。

    ![配置单一登录](./media/active-directory-saas-slack-tutorial/tutorial_general_400.png)

10. 在“SAML 签名证书”部分中，单击“证书(base64)”，然后在计算机上保存证书文件。

    ![配置单一登录](./media/active-directory-saas-slack-tutorial/tutorial_slack_08.png) 

11. 在“Slack 配置”部分中，单击“配置 Slack ”打开“配置登录”窗口。

    ![配置单一登录](./media/active-directory-saas-slack-tutorial/tutorial_slack_09.png) 

    ![配置单一登录](./media/active-directory-saas-slack-tutorial/tutorial_slack_10.png)

12.  在另一个 Web 浏览器窗口中，以管理员身份登录到 Slack 公司站点。

13.  导航到“Microsoft Azure AD”，然后转到“团队设置”。

    ![在应用端配置单一登录](./media/active-directory-saas-slack-tutorial/tutorial_slack_001.png)

14.  在“团队设置”部分中，单击“身份验证”选项卡，然后单击“更改设置”。

    ![在应用端配置单一登录](./media/active-directory-saas-slack-tutorial/tutorial_slack_002.png)

15. 在“SAML 身份验证设置”对话框中，执行以下步骤：

    ![在应用端配置单一登录](./media/active-directory-saas-slack-tutorial/tutorial_slack_003.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。  在“SAML 2.0 终结点(HTTP)”文本框中，放置 Azure AD 应用程序配置窗口中“SAML 单一登录服务 URL”的值。

    b.保留“数据库类型”设置，即设置为“共享”。  在“标识提供程序颁发者”文本框中，放置 Azure AD 应用程序配置窗口中“SAML 实体 ID”的值。

    c.  在记事本中打开下载的证书，将其内容复制到剪贴板，然后将其粘贴到“公共证书”文本框中。

    d. 根据 Slack 团队的需要配置上述三个设置。 有关设置的详细信息，请在此处查看 **Slack 的 SSO 配置指南**。 `https://get.slack.help/hc/en-us/articles/220403548-Guide-to-single-sign-on-with-Slack`

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。  单击“保存配置”。
     
    <!-- 取消选择“允许用户更改其电子邮件地址”。

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。  选择“允许用户选择自己的用户名”。

    f.  对于“必须对团队进行身份验证”，请选择“可选”。 -->
  

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 管理门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 管理门户的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-slack-tutorial/create_aaduser_01.png) 

2. 转到“用户和组”，单击“所有用户”显示用户列表。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-slack-tutorial/create_aaduser_02.png) 

3. 在对话框顶部单击“添加”，打开“用户”对话框。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-slack-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-slack-tutorial/create_aaduser_04.png) 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名称”文本框中，键入 **BrittaSimon**。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d.单击“下一步”。 单击“创建” 。 



### <a name="creating-a-slack-test-user"></a>创建 Slack 测试用户

本部分的目的是在 Slack 中创建名为 Britta Simon 的用户。 Slack 支持在默认情况下启用的实时预配。

本部分不存在任何操作项。 尝试访问 Slack 期间，如果该用户尚不存在，则将创建一个新用户。

> [!NOTE]
> 如果需要手动创建用户，则需联系 [Slack 支持团队](https://slack.com/help/contact)。


### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Slack 的权限，允许她使用 Azure 单一登录。

![分配用户][200] 

**若要将 Britta Simon 分配到 Slack，请执行以下步骤：**

1. 在 Azure 管理门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，然后单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Slack”。

    ![配置单一登录](./media/active-directory-saas-slack-tutorial/tutorial_slack_50.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    


### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的“Slack”磁贴时，用户应自动登录到 Slack 应用程序。


## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-slack-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-slack-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-slack-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-slack-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-slack-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-slack-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-slack-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-slack-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-slack-tutorial/tutorial_general_203.png


<!--HONumber=Feb17_HO1-->


