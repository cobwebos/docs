---
title: "教程：Azure Active Directory 与 Cezanne HR Software 的集成 | Microsoft 文档"
description: "了解如何在 Azure Active Directory 和 Cezanne HR Software 之间配置单一登录。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 4743435b-ee73-46b2-86e6-a612a51dfce4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a62536deafe04ac885a308e1a42ad64cd867a9ca


---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>教程：Azure Active Directory 与 Cezanne HR Software 的集成
本教程的目的是展示如何将 Cezanne HR Software 与 Azure Active Directory (Azure AD) 进行集成。

将 Cezanne HR Software 与 Azure AD 集成可提供以下优势：

* 可以在 Azure AD 中控制谁有权访问 Cezanne HR Software
* 可以让用户使用其 Azure AD 帐户自动登录到 Cezanne HR Software（单一登录）
* 可在一个中心位置（即 Azure 经典门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅[什么是使用 Azure Active Directory 的应用程序访问和单一登录](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件
若要配置 Azure AD 与 Cezanne HR Software 的集成，需要具有以下项：

* Azure AD 订阅
* 启用了 Cezanne HR Software 单一登录的订阅

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

1. 从库中添加 Cezanne HR Software
2. 配置和测试 Azure AD 单一登录

## <a name="adding-cezanne-hr-software-from-the-gallery"></a>从库中添加 Cezanne HR Software
若要配置 Cezanne HR Software 与 Azure AD 的集成，需要从库中将 Cezanne HR Software 添加到托管 SaaS 应用列表。

**若要从库中添加 Cezanne HR Software，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。 
   
    ![Active Directory][1]
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序][2]
4. 在页面底部单击“添加”。
   
    ![应用程序][3]
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![应用程序][4]
6. 在搜索框中，键入“Cezanne HR Software”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_01.png)
7. 在结果面板中，选择“Cezanne HR Software”，然后单击“完成”以添加该应用程序。
   
    ![在库中选择应用](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_0001.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
本部分的目的是基于名为“Britta Simon”的测试用户展示如何配置并测试 Cezanne HR Software 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Cezanne HR Software 用户。 换句话说，需要在 Azure AD 用户与 Cezanne HR Software 中的相关用户之间建立链接关系。

可以通过将 Azure AD 中“用户名”的值分配为 Cezanne HR Software 中“用户名”的值来建立此链接关系。

若要配置并测试 Cezanne HR Software 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-single-sign-on)** - 让用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Cezanne HR Software 测试用户](#creating-a-cezanne-hr-software-test-user)** - 在 Cezanne HR Software 中创建 Britta Simon 的对应用户，将其链接到其 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录
在本部分中，将在经典门户中启用 Azure AD 单一登录并在 Cezanne HR Software 应用程序中配置单一登录。

**若要配置 Cezanne HR Software 的 Azure AD 单一登录，请执行以下步骤：**

1. 在经典门户中，在 **Cezanne HR Software** 应用程序集成页上，单击“配置单一登录”，以打开“配置单一登录”对话框。
   
    ![配置单一登录][6] 
2. 在“你希望用户如何登录到 Cezanne HR Software”页上，选择“Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_03.png)
3. 在“配置应用设置”对话框页上，执行以下步骤，然后单击“下一步”：
   
    ![配置单一登录](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_04.png)
   
    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://w3.cezanneondemand.com/cezannehr/-/<tenant id>`。
   
    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://w3.cezanneondemand.com:443/CezanneOnDemand/-/<tenant id>/Saml/samlp`。
   
    c. 单击“下一步”
   
   > [!NOTE]
   > 请注意，必须使用实际“登录 URL”和“回复 URL”更新这些值。 若要获取这些值，请通过 <mailto:info@cezannehr.com> 联系 Cezanne HR Software 支持团队。
   > 
   > 
4. 在“在 Cezanne HR Software 处配置单一登录”页上，执行以下步骤并单击“下一步”：
   
    ![配置单一登录](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_05.png)
   
    a. 单击“下载证书”，然后将文件保存在计算机上。
   
    b. 单击“下一步”。
5. 在另一个 Web 浏览器窗口中，以管理员身份登录到你的 Cezanne HR Software 租户。
6. 在左侧导航窗格中，单击“系统设置”。 转到“安全设置”。 然后导航到“单一登录配置”。
   
    ![在应用端配置单一登录](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)
7. 在“允许用户使用以下单一登录 (SSO) 服务进行登录”面板上，选中“SAML 2.0”框并选择其旁边的“高级配置”选项。
   
    ![在应用端配置单一登录](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)
8. 单击“新增”按钮。
   
    ![在应用端配置单一登录](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)
9. 在“SAML 2.0 标识提供者”部分中执行以下步骤。
   
    ![在应用端配置单一登录](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)
   
    a. 对于“显示名称”，输入标识提供者的名称。
   
    b. 在“实体标识符”文本框中，输入 Azure AD 应用程序配置向导中的“实体 ID”值。
   
    c. 将“SAML 绑定”更改为“POST”。
   
    d. 在“安全令牌服务终结点”文本框中，输入 Azure AD 应用程序配置向导中的“单一登录服务 URL”值。
   
    e. 在“用户 ID 属性名称”中输入“http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name”。
   
    f. 单击“上载”图标，上载从 Azure AD 下载的证书。
   
    g. 单击“确定”按钮。 
10. 单击“保存”按钮。
    
    ![在应用端配置单一登录](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)
11. 在经典门户中，选择“单一登录配置确认”，然后单击“下一步”。
    
    ![Azure AD 单一登录][10]
12. 在“单一登录确认”页上，单击“完成”。  
    
    ![Azure AD 单一登录][11]

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在经典门户中创建名为“Britta Simon”的测试用户。

![创建 Azure AD 用户][20]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_09.png)
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要显示用户列表，请在顶部菜单中，单击“用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_03.png)
4. 若要打开“添加用户”对话框，请在底部工具栏中单击“添加用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_04.png)
5. 在“告诉我们有关此用户的信息”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_05.png)
   
    a. 对于“用户类型”，选择“组织中的新用户”。
   
    b. 在“用户名”文本框中，键入“BrittaSimon”。
   
    c. 单击“下一步”。
6. 在“用户配置文件”对话框页上，执行以下步骤：
   
   ![创建 Azure AD 测试用户](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_06.png)
   
   a. 在“名字”文本框中，键入“Britta”。  
   
   b. 在“姓氏”文本框中，键入“Simon”。
   
   c. 在“显示名称”文本框中，键入“Britta Simon”。
   
   d. 在“角色”列表中，选择“用户”。
   
   e. 单击“下一步”。
7. 在“获取临时密码”对话框页上，单击“创建”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_07.png)
8. 在“获取临时密码”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_08.png)
   
    a. 写下“新密码”的值。
   
    b. 单击“完成”。   

### <a name="creating-a-cezanne-hr-software-test-user"></a>创建 Cezanne HR Software 测试用户
为了使 Azure AD 用户能够登录到 Cezanne HR Software，必须将其预配到 Cezanne HR Software 中。 对于 Cezanne HR Software，需要手动执行预配。

#### <a name="to-provision-a-user-account-perform-the-following-steps"></a>若要预配用户帐户，请执行以下步骤：
1. 以管理员身份登录到你的 Cezanne HR Software 公司站点。
2. 在左侧导航窗格中，单击“系统设置”。 转到“管理用户”。 然后导航到“添加新用户”。
   
   ![新建用户](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "New User")
3. 在“人员详细信息”部分中，执行以下步骤︰
   
   ![新建用户](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "New User")
   
   a. 将“内部用户”设置为“关闭”。
   
   b. 在“名字”文本框中，键入“Britta”。  
   
   c. 在“姓氏”文本框中，键入“Simon”。
   
   d. 在“电子邮件”文本框中，键入 Britta Simon 帐户的电子邮件地址。
4. 在“帐户信息”部分中，执行以下步骤：
   
   ![新建用户](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "New User")
   
   a. 在“用户名”文本框中，键入 Britta Simon 的电子邮件地址。
   
   b. 在“密码”文本框中，键入 Britta Simon 帐户的密码。
   
   c. 对于“安全角色”，选择“HR 专业人员”。
   
   d. 单击“确定”。
5. 导航到“单一登录”选项卡，并在“SAML 2.0 标识符”区域中选择“新增”。
   
    ![用户](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "User")
6. 对于“标识提供者”，选择你的标识提供者，在“用户标识符”文本框中，输入 Britta Simon 帐户的电子邮件地址。
   
    ![用户](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "User")
7. 单击“保存”按钮。
   
    ![用户](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "User")

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户
本部分的目的是通过向 Britta Simon 授予对 Cezanne HR Software 的访问权限，使她能够使用 Azure 单一登录。

![分配用户][200]

**若要将 Britta Simon 分配到 Cezanne HR Software，请执行以下步骤：**

1. 在经典门户中，若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![分配用户][201]
2. 在应用程序列表中，选择“Cezanne HR Software”。
   
    ![配置单一登录](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_50.png)
3. 在顶部菜单中，单击“用户”。
   
    ![分配用户][203]
4. 在“用户”列表中，选择“Britta Simon”。
5. 在底部工具栏中，单击“分配”。
   
    ![分配用户][205]

### <a name="testing-single-sign-on"></a>测试单一登录
本部分的目的是使用“访问面板”测试 Azure AD 单一登录配置。

当在访问面板中单击 Cezanne HR Software 磁贴时，应当会自动登录到 Cezanne HR Software 应用程序。

## <a name="additional-resources"></a>其他资源
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->


