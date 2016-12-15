---
title: "教程：Azure Active Directory 与 eTouches 的集成 | Microsoft 文档"
description: "了解如何在 Azure Active Directory 和 eTouches 之间配置单一登录。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 76cccaa8-859c-4c16-9d1d-8a6496fc7520
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: de15edc017cb49db23cd0138fc56d383cf409aa2


---
# <a name="tutorial-azure-active-directory-integration-with-etouches"></a>教程：Azure Active Directory 与 eTouches 的集成
本教程介绍了如何将 eTouches 与 Azure Active Directory (Azure AD) 进行集成。

将 eTouches 与 Azure AD 集成可提供以下优势：

* 可以在 Azure AD 中控制谁有权访问 eTouches
* 可以让用户使用其 Azure AD 帐户自动登录到 eTouches（单一登录）
* 可在一个中心位置（即 Azure 经典门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅[什么是使用 Azure Active Directory 的应用程序访问和单一登录](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件
若要配置 Azure AD 与 eTouches 的集成，需要具有以下项：

* Azure AD 订阅
* 启用了 eTouches 单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。
> 
> 

测试本教程中的步骤应遵循以下建议：

* 不应使用生产环境，除非有此必要。
* 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。

本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 eTouches
2. 配置和测试 Azure AD 单一登录

## <a name="adding-etouches-from-the-gallery"></a>从库中添加 eTouches
若要配置 eTouches 与 Azure AD 的集成，需要从库中将 eTouches 添加到托管 SaaS 应用列表。

**若要从库中添加 eTouches，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。
   
    ![Active Directory][1]
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序][2]
4. 在页面底部单击“添加”。
   
    ![应用程序][3]
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![应用程序][4]
6. 在搜索框中，键入“eTouches”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_01.png)
7. 在结果窗格中，选择“eTouches”，然后单击“完成”以添加该应用程序。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，将基于名为“Britta Simon”的测试用户配置并测试 eTouches 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 eTouches 用户。 换句话说，需要在 Azure AD 用户与 eTouches 中的相关用户之间建立链接关系。

可以通过将 Azure AD 中“用户名”的值分配为 eTouches 中“用户名”的值来建立此链接关系。

若要配置并测试 eTouches 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 eTouches 测试用户](#creating-a-predictix-price-reporting-test-user)** - 在 eTouches 中创建 Britta Simon 的对应用户，将其链接到其 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录
在本部分中，将在经典门户中启用 Azure AD 单一登录并在 eTouches 应用程序中配置单一登录。

eTouches 应用程序需要特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可从应用程序的“属性”选项卡管理这些属性的值。 以下屏幕截图显示一个示例。 

![配置单一登录](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_07.png) 

**若要配置 eTocuhes 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 经典门户中，在 **eTouches** 应用程序集成页上，在顶部菜单中单击“属性”。
   
    ![配置单一登录](./media/active-directory-saas-etouches-tutorial/tutorial_general_80.png) 
2. 在“SAML 令牌属性”对话框中，对于下表中显示的每个行，执行以下步骤：
   
   | 属性名称 | 属性值 |
   | --- | --- |
   | Email |user.mail |
   
    a. 单击“添加用户属性”，打开“添加用户属性”对话框。
   
    ![配置单一登录](./media/active-directory-saas-etouches-tutorial/tutorial_general_81.png) 

    b. 在“属性名称”文本框中，键入为该行显示的属性名称。

    c. 在“属性值”列表中，选择为该行显示的属性值。

    d. 单击“完成”。    


1. 在经典门户中，在 **eTouches** 应用程序集成页上，单击“配置单一登录”，以打开“配置单一登录”对话框。
   
    ![配置单一登录][6] 
2. 在“你希望用户如何登录到 eTouches”页上，选择“Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_03.png) 
3. 在“配置应用设置”对话框页上，执行以下步骤：
   
    ![配置单一登录](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_04.png) 
   
    a. 在“登录 URL”文本框中，使用以下模式键入用户用来登录 eTouches 应用程序的 URL：**https://www.eiseverywhere.com/saml/accounts/?sso&accountid=\<accountid\>**。
   
    b. 单击“下一步”
4. 在“在 eTouches 处配置单一登录”页上，执行以下步骤：
   
    ![配置单一登录](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_05.png)
   
    a. 单击“下载元数据”，然后在计算机上保存该文件。
   
    b. 单击“下一步”。
5. 若要为应用程序配置 SSO，请在 eTouches 应用程序中执行以下步骤：
   
    a. 使用管理权限登录到 **eTouches** 应用程序。
   
    b. 转到 **SAML** 配置
   
    c. 在“常规设置”部分中，将 Azure AD 联合元数据内容粘贴到文本框中。
   
    d. 单击“保存并停留”按钮
   
    e. 在“SAML 元数据”部分中单击“更新元数据”按钮。 
   
    f. 这将打开该页面并执行 SSO。 一旦 SSO 开始工作，便可设置用户名
   
    g. 在“用户名”字段中，选择 **emailaddress**，如下图所示。 
   
    h. 复制“SSO URL / ACS”值，并将其粘贴到 Azure AD 应用程序配置向导中的“登录 URL”文本框中。
   
    ![配置单一登录](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_06.png)
6. 在经典门户中，选择“单一登录配置确认”，然后单击“下一步”。
   
    ![Azure AD 单一登录][10]
7. 在“单一登录确认”页上，单击“完成”。  

    ![Azure AD 单一登录][11]


### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
在本部分中，在经典门户中创建名为“Britta Simon”的测试用户。

![创建 Azure AD 用户][20]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-etouches-tutorial/create_aaduser_09.png) 
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要显示用户列表，请在顶部菜单中，单击“用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-etouches-tutorial/create_aaduser_03.png) 
4. 若要打开“添加用户”对话框，请在底部工具栏中单击“添加用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-etouches-tutorial/create_aaduser_04.png) 
5. 在“告诉我们有关此用户的信息”对话框页中，执行以下步骤： ![创建 Azure AD 测试用户](./media/active-directory-saas-etouches-tutorial/create_aaduser_05.png) 
   
    a. 对于“用户类型”，选择“组织中的新用户”。
   
    b. 在“用户名”文本框中，键入“BrittaSimon”。
   
    c. 单击“下一步”。
6. 在“用户配置文件”对话框页面上，执行以下步骤：![创建 Azure AD 测试用户](./media/active-directory-saas-etouches-tutorial/create_aaduser_06.png) 
   
   a. 在“名字”文本框中，键入“Britta”。  
   
   b. 在“姓氏”文本框中，键入“Simon”。
   
   c. 在“显示名称”文本框中，键入“Britta Simon”。
   
   d. 在“角色”列表中，选择“用户”。
   
   e. 单击“下一步”。
7. 在“获取临时密码”对话框页上，单击“创建”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-etouches-tutorial/create_aaduser_07.png) 
8. 在“获取临时密码”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-etouches-tutorial/create_aaduser_08.png) 
   
    a. 写下“新密码”的值。
   
    b. 单击“完成”。   

### <a name="creating-an-etouches-test-user"></a>创建 eTouches 测试用户
在本部分中，将在 eTouches 中创建一个名为 Britta Simon 的用户。 请与 eTouches 支持团队协作来在 eTouches 平台中添加用户。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户
在本部分中，将通过向 Britta Simon 授予对 eTouches 的访问权限使她能够使用 Azure 单一登录。

![分配用户][200] 

**若要将 Britta Simon 分配到 eTouches，请执行以下步骤：**

1. 在经典门户中，若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![分配用户][201] 
2. 在应用程序列表中，选择“eTouches”。
   
    ![配置单一登录](./media/active-directory-saas-etouches-tutorial/tutorial_etouches_50.png) 
3. 在顶部菜单中，单击“用户”。
   
    ![分配用户][203]
4. 在“用户”列表中，选择“Britta Simon”。
5. 在底部工具栏中，单击“分配”。
   
    ![分配用户][205]

### <a name="testing-single-sign-on"></a>测试单一登录
在本部分中，使用访问面板测试 Azure AD 单一登录配置。

当在访问面板中单击 eTouches 磁贴时，应当会自动登录到 eTouches 应用程序。

## <a name="additional-resources"></a>其他资源
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-etouches-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->


