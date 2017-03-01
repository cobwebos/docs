---
title: "教程：Azure Active Directory 与 Boomi 集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 Boomi 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
editor: na
ms.assetid: 8e05afa9-2eda-4975-a0cc-6d408065860f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 6a60db808388bf1fdf9441518920f2eb4e5fcc4d
ms.openlocfilehash: bafbfb710d8cdb370d21d9299c447a0dfed3c468


---
# <a name="tutorial-azure-active-directory-integration-with-boomi"></a>教程：Azure Active Directory 与 Boomi 集成

在本教程中，了解如何将 Boomi 与 Azure Active Directory (Azure AD) 集成。

将 Boomi 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Boomi
- 可以让用户使用其 Azure AD 帐户自动登录到 Boomi（单一登录）
- 可在一个中心位置（即 Azure 经典门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Boomi 的集成，需要以下项：

- Azure AD 订阅
- 已启用 Boomi 单一登录的订阅


> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。


测试本教程中的步骤应遵循以下建议：

- 不应使用生产环境，除非有此必要。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。


## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Boomi
2. 配置和测试 Azure AD 单一登录


## <a name="adding-boomi-from-the-gallery"></a>从库中添加 Boomi
若要配置 Boomi 与 Azure AD 的集成，需要从库中将 Boomi 添加到托管 SaaS 应用列表。

**若要从库中添加 Boomi，请执行以下步骤：**

1. 在 **Azure 经典门户**的左侧导航窗格上，单击“Active Directory”。 

    ![Active Directory][1]

2. 从“目录”列表中，选择要为其启用目录集成的目录。

3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。

    ![应用程序][2]

4. 在页面底部单击“添加”。

    ![应用程序][3]

5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。

    ![应用程序][4]

6. 在搜索框中，键入“Boomi”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_01.png)

7. 在“结果”窗格中，选择“Boomi”，然后单击“完成”，添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，将基于名为“Britta Simon”的测试用户配置和测试 Boomi 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Boomi 用户。 换句话说，需要在 Azure AD 用户与 Boomi 中相关用户之间建立链接关系。

通过将 Azure AD 中“用户名”的值分配为 Boomi 中“用户名”的值来建立此链接关系。

若要配置和测试 Boomi 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Boomi 测试用户](#creating-a-boomi-test-user)** - 在 Boomi 中创建 Britta Simon 的对应用户，将其链接到她的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

本部分的目的是在 Azure 经典门户中启用 Azure AD 单一登录并在 Boomi 应用程序中配置单一登录。

Boomi 应用程序需要特定格式的 SAML 断言，这就需要使用用户的联合 ID 设置 NameIdentifier 属性值。 默认情况下，Azure AD 将 UserPrincipalName 用于 NameIdentifier 属性。 但为了成功集成，需要在 Boomi 中将此值调整为与用户的联合 ID 匹配。 可以在“属性”选项卡中更改此值，如下面的屏幕截图中所示。 只有在完成相应的映射后，集成才有效。

![配置单一登录](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_51.png)

**若要配置 Boomi 的 Azure AD 单一登录，请执行以下步骤：**

1. 在经典门户中的“Boomi”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。

    ![配置单一登录][6] 

2. 在“你希望用户如何登录 Boomi”页上，选择“Azure AD 单一登录”，然后单击“下一步”。
 
    ![配置单一登录](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_03.png)

3. 在“配置应用设置”对话框页上，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_04.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“回复 URL”文本框中，使用以下模式键入 URL：`https://platform.boomi.com/sso/<account name>/saml`

    b.保留“数据库类型”设置，即设置为“共享”。 单击“下一步”。

    > [!NOTE] 
    > 请注意，这不是实际值。 必须使用实际回复 URL 更新此值。 若要获取此值，请与 Boomi 支持团队联系。

4. 在“配置 Boomi 的单一登录”页上，单击“下载证书”，然后在计算机上保存该文件：

    ![配置单一登录](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_05.png) 

    > [!NOTE]
    > 请注意，响应中的 NameID 声明值必须与 Boomi 系统中配置的联合 ID 匹配。 因此，请协助 Boomi 支持团队将你组织中的相应用户标识符映射为联合 ID。 默认情况下，Azure AD 将 NameIdentifier 设置为 UPN 值。 可以在“属性”选项卡中更改此设置，如下面的屏幕截图中所示。 只有在完成相应的映射后，集成才有效。 
    
    ![配置单一登录](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_51.png)

5. 在另一个 Web 浏览器窗口中，以管理员身份登录 Boomi 公司站点。 

6. 导航到“公司名称”，然后转到“设置”。

7. 单击“SSO 选项”选项卡，并执行以下步骤。

    ![在应用端配置单一登录](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_06.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 选中“启用 SAML 单一登录”复选框。

    b.保留“数据库类型”设置，即设置为“共享”。 单击“导入”，将已下载的证书从 Azure AD 上载到“标识提供者证书”。
    
    c. 在“标识提供者登录 URL”文本框中，放置 Azure AD 应用程序配置向导中**远程登录 URL** 的值。

    d.单击“下一步”。 对于“联合 ID 位置”，选择“联合 ID 位于使用者的 NameID 元素中”单选按钮。 

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 单击“保存”按钮。

8. 在经典门户中，选择“单一登录配置确认”，然后单击“下一步”。

    ![Azure AD 单一登录][10]

9. 在“单一登录确认”页上，单击“完成”。  
  
    ![Azure AD 单一登录][11]


### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在经典门户中创建名为“Britta Simon”的测试用户。

![创建 Azure AD 用户][20]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-boomi-tutorial/create_aaduser_09.png) 

2. 在“目录”列表中，选择要启用目录集成的目录。

3. 若要显示用户列表，请在顶部菜单中，单击“用户”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-boomi-tutorial/create_aaduser_03.png) 

4. 若要打开“添加用户”对话框，请在底部工具栏中单击“添加用户”。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-boomi-tutorial/create_aaduser_04.png) 

5. 在“告诉我们有关此用户的信息”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-boomi-tutorial/create_aaduser_05.png) 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 对于“用户类型”，选择“组织中的新用户”。

    b. 在“用户名”文本框中，键入“BrittaSimon”。

    c. 单击“下一步”。

6.  在“用户配置文件”对话框页上，执行以下步骤：

    ![创建 Azure AD 测试用户](./media/active-directory-saas-boomi-tutorial/create_aaduser_06.png) 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名字”文本框中，键入“Britta”。  

    b. 在“姓氏”文本框中，键入“Simon”。

    c. 在“显示名称”文本框中，键入“Britta Simon”。

    d.单击“下一步”。 在“角色”列表中，选择“用户”。

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 单击“下一步”。

7. 在“获取临时密码”对话框页上，单击“创建”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-boomi-tutorial/create_aaduser_07.png) 

8. 在“获取临时密码”对话框页上，执行以下步骤：

    ![创建 Azure AD 测试用户](./media/active-directory-saas-boomi-tutorial/create_aaduser_08.png) 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 写下“新密码”的值。

    b. 单击“完成”。   



### <a name="creating-a-boomi-test-user"></a>创建 Boomi 测试用户

为了使 Azure AD 用户能够登录到 Boomi，必须将其预配到 Boomi 中。 就 Boomi 来说，预配任务需要手动完成。

####<a name="to-provision-a-user-account-perform-the-following-steps"></a>若要预配用户帐户，请执行以下步骤：

1. 以管理员身份登录 Boomi 公司站点。

2. 登录后，导航到“用户管理”并转到“用户”。

    ![用户](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_001.png "Users")

3. 单击 **+** 图标，此时将打开“添加/维护用户角色”对话框。

    ![用户](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_002.png "Users")

    ![用户](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_003.png "Users")

4. 输入用户的**用户电子邮件地址**。

5. 输入用户的**名字**和**姓氏**。

6. 输入用户的**联合 ID**。 每个用户都必须具有在帐户中唯一标识用户的联合 ID。 

7. 向用户分配“标准用户”角色。 不要分配管理员角色，因为这会向用户提供标准 Atmosphere 访问权限以及单一登录访问权限。

8. 单击 **“确定”**。

    > [!NOTE]
    > 用户不会收到包含可用于登录 AtomSphere 帐户的密码的欢迎通知电子邮件，因为其密码将通过标识提供者进行管理。 可以使用任何其他 Boomi 用户帐户创建工具或 Boomi 提供的 API 来预配 AAD 用户帐户。 

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Boomi 的权限，允许她使用 Azure 单一登录。

![分配用户][200] 

**若要将 Britta Simon 分配到 Boomi，请执行以下步骤：**

1. 在经典门户中，若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Boomi”。

    ![配置单一登录](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_50.png) 

3. 在顶部菜单中，单击“用户”。

    ![分配用户][203] 

4. 在“用户”列表中，选择“Britta Simon”。

5. 在底部工具栏中，单击“分配”。
    
    ![分配用户][205]



### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的“Boomi”磁贴时，用户应自动登录到 Boomi 应用程序。


## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO1-->


