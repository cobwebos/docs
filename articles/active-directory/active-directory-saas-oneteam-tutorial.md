---
title: "教程：Azure Active Directory 与 Oneteam 的集成 | Microsoft 文档"
description: "了解如何在 Azure Active Directory 和 Oneteam 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 2e94916c-64ae-4e1a-a8b5-bc6ef7d28c29
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: d3327faa5864b721fff5d3a34389d214b0ae29a9
ms.openlocfilehash: dab95c2b63e18c2cd21ffb33370ce16404237762
ms.lasthandoff: 12/16/2016


---
# <a name="tutorial-azure-active-directory-integration-with-oneteam"></a>教程：Azure Active Directory 与 Oneteam 的集成

本教程介绍了如何将 Oneteam 与 Azure Active Directory (Azure AD) 进行集成。

将 Oneteam 与 Azure AD 集成可提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Oneteam
- 可以让用户使用其 Azure AD 帐户自动登录到 Oneteam（单一登录）
- 可在一个中心位置（即 Azure 经典门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Oneteam 的集成，需要具有以下项：

- Azure AD 订阅
- 启用了 Oneteam 单一登录的订阅


> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。


测试本教程中的步骤应遵循以下建议：

- 不应使用生产环境，除非有此必要。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。


## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Oneteam
2. 配置和测试 Azure AD 单一登录


## <a name="adding-oneteam-from-the-gallery"></a>从库中添加 Oneteam
若要配置 Oneteam 与 Azure AD 的集成，需要从库中将 Oneteam 添加到托管 SaaS 应用列表。

**若要从库中添加 Oneteam，请执行以下步骤：**

1. 在 **Azure 经典门户**的左侧导航窗格上，单击“Active Directory”。 

    ![Active Directory][1]

2. 从“目录”列表中，选择要为其启用目录集成的目录。

3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。

    ![应用程序][2]

4. 在页面底部单击“添加”。

    ![应用程序][3]

5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。

    ![应用程序][4]

6. 在搜索框中，键入“Oneteam”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-oneteam-tutorial/tutorial_oneteam_001.png)

7. 在结果窗格中，选择“Oneteam”，然后单击“完成”以添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-oneteam-tutorial/tutorial_oneteam_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，将基于名为“Britta Simon”的测试用户配置并测试 Oneteam 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Oneteam 用户。 换句话说，需要在 Azure AD 用户与 Oneteam 中的相关用户之间建立链接关系。

可以通过将 Azure AD 中“用户名”的值分配为 Oneteam 中“用户名”的值来建立此链接关系。

若要配置并测试 Oneteam 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Oneteam 测试用户](#creating-a-oneteam-test-user)** - 在 Oneteam 中创建 Britta Simon 的对应用户，将其链接到其 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在经典门户中启用 Azure AD 单一登录并在 Oneteam 应用程序中配置单一登录。


**若要配置 Oneteam 的 Azure AD 单一登录，请执行以下步骤：**

1. 在经典门户中，在 **Oneteam** 应用程序集成页上，单击“配置单一登录”，以打开“配置单一登录”对话框。

    ![配置单一登录][6]

2. 在“你希望用户如何登录到 Oneteam”页上，选择“Azure AD 单一登录”，然后单击“下一步”。
 
    ![配置单一登录](./media/active-directory-saas-oneteam-tutorial/tutorial_oneteam_02.png)

3. 在“配置应用设置”对话框页上，如果要在“IDP 发起的模式”下配置应用程序，请执行以下步骤，然后单击“下一步”：

    ![配置单一登录](./media/active-directory-saas-oneteam-tutorial/tutorial_oneteam_03.png)

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://api.one-team.io/teams/<team name>/auth/saml/issuer`。

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://api.one-team.io/teams/<team name>/auth/saml/callback`。

    c. 单击“资源组名称” 的 Azure 数据工厂。

4. 如果要在“配置应用设置”对话框页上以“SP 发起的模式”配置应用程序，则单击“显示高级设置(可选)”，然后输入“登录 URL”，并单击“下一步”。

    ![配置单一登录](./media/active-directory-saas-oneteam-tutorial/tutorial_oneteam_04.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<team name>.one-team.io/`。

    b. 单击“资源组名称” 的 Azure 数据工厂。

    > [!NOTE]
    > 请注意，必须使用实际“登录 URL”、标识符和“回复 URL”更新这些值。 可以在<a href="https://support.one-team.com/hc/en-us/requests/new">此处</a>向 Oneteam 提交支持票证以获取这些值。

5. 在“在 Oneteam 处配置单一登录”页上，单击“下载元数据”，然后将该文件保存在计算机上：

    ![配置单一登录](./media/active-directory-saas-oneteam-tutorial/tutorial_oneteam_05.png) 

6. 若要为应用程序配置 SSO，可以在<a href="https://support.one-team.com/hc/en-us/requests/new">此处</a>向 Oneteam 支持团队提交支持票证，并为其提供下载的**元数据**。

7. 在经典门户中，选择“单一登录配置确认”，然后单击“下一步”。

    ![Azure AD 单一登录][10]

8. 在“单一登录确认”页上，单击“完成”。  
  
    ![Azure AD 单一登录][11]


### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在经典门户中创建名为“Britta Simon”的测试用户。

![创建 Azure AD 用户][20]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-oneteam-tutorial/create_aaduser_09.png) 

2. 在“目录”列表中，选择要启用目录集成的目录。

3. 若要显示用户列表，请在顶部菜单中，单击“用户”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-oneteam-tutorial/create_aaduser_03.png) 

4. 若要打开“添加用户”对话框，请在底部工具栏中单击“添加用户”。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-oneteam-tutorial/create_aaduser_04.png) 

5. 在“告诉我们有关此用户的信息”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-oneteam-tutorial/create_aaduser_05.png) 

    a. 对于“用户类型”，选择“组织中的新用户”。

    b. 在“用户名”文本框中，键入“BrittaSimon”。

    c. 单击“下一步”。

6.  在“用户配置文件”对话框页上，执行以下步骤：

    ![创建 Azure AD 测试用户](./media/active-directory-saas-oneteam-tutorial/create_aaduser_06.png) 

    a. 在“名字”文本框中，键入“Britta”。  

    b. 在“姓氏”文本框中，键入“Simon”。

    c. 在“显示名称”文本框中，键入“Britta Simon”。

    d. 在“角色”列表中，选择“用户”。

    e. 单击“下一步”。

7. 在“获取临时密码”对话框页上，单击“创建”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-oneteam-tutorial/create_aaduser_07.png) 

8. 在“获取临时密码”对话框页上，执行以下步骤：

    ![创建 Azure AD 测试用户](./media/active-directory-saas-oneteam-tutorial/create_aaduser_08.png) 

    a. 写下“新密码”的值。

    b. 单击“完成”。   



### <a name="creating-a-oneteam-test-user"></a>创建 Oneteam 测试用户

本部分的目的是在 Oneteam 中创建名为 Britta Simon 的用户。 Oneteam 支持在默认情况下启用的实时预配。

此部分不存在任何操作项。 如果尚不存在用户，则在尝试访问 Oneteam 期间会创建一个新用户。

> [!NOTE]
> 如果需要手动创建用户，请从<a href="https://support.one-team.com/hc/en-us/requests/new">此处</a>向 Oneteam 支持团队提交支持票证。


### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，将通过向 Britta Simon 授予对 Oneteam 的访问权限使她能够使用 Azure 单一登录。

![分配用户][200] 

**若要将 Britta Simon 分配到 Oneteam，请执行以下步骤：**

1. 在经典门户中，若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Oneteam”。

    ![配置单一登录](./media/active-directory-saas-oneteam-tutorial/tutorial_oneteam_50.png) 

3. 在顶部菜单中，单击“用户”。

    ![分配用户][203] 

4. 在“用户”列表中，选择“Britta Simon”。

5. 在底部工具栏中，单击“分配”。
    
    ![分配用户][205]



### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

当在访问面板中单击 Oneteam 磁贴时，应当会自动登录到 Oneteam 应用程序。


## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-oneteam-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-oneteam-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-oneteam-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-oneteam-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-oneteam-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-oneteam-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-oneteam-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-oneteam-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-oneteam-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-oneteam-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-oneteam-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-oneteam-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-oneteam-tutorial/tutorial_general_205.png

