---
title: "教程：Azure Active Directory 与 Deputy 的集成 | Microsoft 文档"
description: "了解如何在 Azure Active Directory 和 Deputy 之间配置单一登录。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 5665c3ac-5689-4201-80fe-fcc677d4430d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 04a045f41965b093aab71e59cd9b5f328b44de84
ms.openlocfilehash: c853d61257493d73cd0f8a51a15f0389e1e83cf4


---
# <a name="tutorial-azure-active-directory-integration-with-deputy"></a>教程：Azure Active Directory 与 Deputy 的集成
本教程的目的是展示如何将 Deputy 与 Azure Active Directory (Azure AD) 进行集成。

将 Deputy 与 Azure AD 集成可提供以下优势：

* 可以在 Azure AD 中控制谁有权访问 Deputy
* 可以让用户使用其 Azure AD 帐户自动登录到 Deputy（单一登录）
* 可以在一个中心位置（即 Azure 经典门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件
若要配置 Azure AD 与 Deputy 的集成，需要具有以下项：

* Azure AD 订阅
* 已启用 Deputy 单一登录 (SSO) 的订阅

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

1. 从库中添加 Deputy
2. 配置和测试 Azure AD 单一登录

## <a name="adding-deputy-from-the-gallery"></a>从库中添加 Deputy
若要配置 Deputy 与 Azure AD 的集成，需要从库中将 Deputy 添加到托管 SaaS 应用列表。

**若要从库中添加 Deputy，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。 
   
    ![Active Directory][1]
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序][2]
4. 在页面底部单击“添加”。
   
    ![应用程序][3]
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![应用程序][4]
6. 在搜索框中，键入“Deputy”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_01.png)
7. 在结果面板中，选择“Deputy”，然后单击“完成”以添加该应用程序。
   
    ![在库中选择应用](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_0001.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
本部分的目的是基于名为“Britta Simon”的测试用户展示如何配置并测试 Deputy 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Deputy 用户。 换句话说，需要在 Azure AD 用户与 Deputy 中的相关用户之间建立链接关系。

可以通过将 Azure AD 中“用户名”的值分配为 Deputy 中“用户名”的值来建立此链接关系。

若要配置并测试 Deputy 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-single-sign-on)** - 让用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Deputy 测试用户](#creating-a-deputy-test-user)** - 在 Deputy 中创建 Britta Simon 的对应用户，将其链接到其 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录
在本部分中，将在经典门户中启用 Azure AD 单一登录并在 Deputy 应用程序中配置单一登录。

**若要配置 Deputy 的 Azure AD 单一登录，请执行以下步骤：**

1. 在经典门户中，在 **Deputy** 应用程序集成页上，单击“配置单一登录”，以打开“配置单一登录”对话框。
   
    ![配置单一登录][6] 
2. 在“你希望用户如何登录到 Deputy”页上，选择“Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_03.png)
3. 在“配置应用设置”对话框页上，如果要在“IDP 发起的模式”下配置应用程序，请执行以下步骤，然后单击“下一步”：
   
    ![配置单一登录](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_04.png)
  1. 在“标识符”文本框中，使用以下模式键入 URL：`https://<your-subdomain>.<region>.deputy.com`。
  2. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<your-subdomain>.<region>.deputy.com/exec/devapp/samlacs`。
  3. 单击“资源组名称” 的 Azure 数据工厂。
4. 如果要在“配置应用设置”对话框页上以“SP 发起的模式”配置应用程序，则单击“显示高级设置(可选)”，然后输入“登录 URL”，并单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_05.png)
   1. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<your-subdomain>.<region>.deputy.com`。
   2. 单击“下一步”。
   
   > [!NOTE]
   > Deputy 区域后缀是可选的，或者应当使用下列项之一：au | na | eu |as |la |af |an |ent-au |ent-na |ent-eu |ent-as | ent-la | ent-af | ent-an
   > 
   > 
5. 在“在 Deputy 处配置单一登录”页上，执行以下步骤并单击“下一步”：
   
    ![配置单一登录](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_06.png)
   1. 单击“下载证书”，然后将文件保存在计算机上。
6. 导航到以下 URL：https://(your-subdomain).deputy.com/exec/config/system_config。 转到“安全设置”并单击“编辑”。
   
    ![配置单一登录](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_004.png)
7. 在 Azure 经典门户中，在“在 Deputy 处配置单一登录”页上，复制 SAML SSO URL。 
8. 在此“安全设置”页上，执行以下步骤。
   
![配置单一登录](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_005.png)

   1. 启用**社交登录**。
   2. 在记事本中打开 Base64 编码的证书，将其内容复制到剪贴板，然后将其粘贴到“OpenSSL 证书”文本框中。
   3. 在“SAM SSO URL”文本框中，键入 `https://<your subdomain>.deputy.com/exec/devapp/samlacs?dpLoginTo=<saml sso url>`
   4. 在“SAM SSO URL”文本框中，将 `<your subdomain>` 替换为你的子域。
   5. 在“SAM SSO URL”文本框中，将 `<saml sso url>` 替换为你从 Azure 经典门户中复制的 SAML SSO URL。
   6. 单击“保存设置”。
9. 在经典门户中，选择“单一登录配置确认”，然后单击“下一步”。
   
    ![Azure AD 单一登录][10]
10. 在“单一登录确认”页上，单击“完成”。  
    
    ![Azure AD 单一登录][11]

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在经典门户中创建名为“Britta Simon”的测试用户。

![创建 Azure AD 用户][20]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-deputy-tutorial/create_aaduser_09.png)
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要显示用户列表，请在顶部菜单中，单击“用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-deputy-tutorial/create_aaduser_03.png)
4. 若要打开“添加用户”对话框，请在底部工具栏中单击“添加用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-deputy-tutorial/create_aaduser_04.png)
5. 在“告诉我们有关此用户的信息”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-deputy-tutorial/create_aaduser_05.png)

   1. 在“用户类型”中，选择“你的组织中的新用户”。
   2. 在“用户名”文本框中，键入“BrittaSimon”。
   3. 单击“下一步”。
6. 在“用户配置文件”对话框页上，执行以下步骤：
   
   ![创建 Azure AD 测试用户](./media/active-directory-saas-deputy-tutorial/create_aaduser_06.png)
   
   1. 在“名字”文本框中，键入“Britta”。  
   2. 在“姓氏”文本框中，键入“Simon”。
   3. 在“显示名称”文本框中，键入“Britta Simon”。
   4. 在“角色”列表中，选择“用户”。
   5. 单击“资源组名称” 的 Azure 数据工厂。
   
7. 在“获取临时密码”对话框页上，单击“创建”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-deputy-tutorial/create_aaduser_07.png)
8. 在“获取临时密码”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-deputy-tutorial/create_aaduser_08.png)
   
   1. 写下“新密码”的值。
   2. 单击“完成”。   

### <a name="creating-a-deputy-test-user"></a>创建 Deputy 测试用户
为了使 Azure AD 用户能够登录到 Deputy，必须将其预配到 Deputy 中。 对于 Deputy，需要手动执行预配。

#### <a name="to-provision-a-user-account-perform-the-following-steps"></a>若要预配用户帐户，请执行以下步骤：
1. 以管理员身份登录到你的 Deputy 公司站点。
2. 在顶部的导航窗格中，单击“人员”。
   
   ![人员](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_001.png "人员")
3. 单击“添加人员”按钮，然后单击“添加单个人员”。
   
   ![添加人员](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_002.png "添加人员")
4. 执行以下步骤并单击“保存并邀请”。
   
   ![新建用户](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_003.png "New User")
   
  1. 在“姓名”文本框中，键入 **Britta** 和 **Simon**。  
  2. 在“电子邮件”文本框中，键入要预配的 Azure AD 帐户的电子邮件地址。
  3. 在“工作单位”文本框中，键入公司名称。
  4. 单击“保存并邀请”按钮。
   
   > [!NOTE]
   > AAD 帐户持有者将收到一封电子邮件，并打开用于在激活帐户前确认其帐户的链接。 可以使用 Deputy 提供的任何其他 Deputy 用户帐户创建工具或 API 来预配 AAD 用户帐户。
   > 
   > 

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户
本部分的目的是通过向 Britta Simon 授予对 Deputy 的访问权限，使她能够使用 Azure 单一登录。

![分配用户][200]

**若要将 Britta Simon 分配到 Deputy，请执行以下步骤：**

1. 在经典门户中，若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![分配用户][201]
2. 在应用程序列表中，选择“Deputy”。
   
    ![配置单一登录](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_50.png)
3. 在顶部菜单中，单击“用户”。
   
    ![分配用户][203]
4. 在“用户”列表中，选择“Britta Simon”。
5. 在底部工具栏中，单击“分配”。
   
    ![分配用户][205]

### <a name="testing-single-sign-on"></a>测试单一登录
本部分的目的是使用“访问面板”测试 Azure AD 单一登录配置。

当在访问面板中单击 Deputy 磁贴时，应当会自动登录到 Deputy 应用程序。

## <a name="additional-resources"></a>其他资源
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_205.png



<!--HONumber=Feb17_HO1-->


