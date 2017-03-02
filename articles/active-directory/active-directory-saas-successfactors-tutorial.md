---
title: "教程：Azure Active Directory 与 SuccessFactors 集成 | Microsoft Docs"
description: "了解如何使用 SuccessFactors 与 Azure Active Directory 以启用单一登录、自动化预配及更多功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 0837cb33bf438fb7fd9665d21d411f0170cdd393
ms.openlocfilehash: a0a101c34ffd4adb6816d9db8313d10b0ffbb3ae
ms.lasthandoff: 02/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-successfactors"></a>教程：Azure Active Directory 与 SuccessFactors 集成
本教程的目的是说明如何将 SuccessFactors 与 Azure Active Directory (Azure AD) 集成。

将 SuccessFactors 与 Azure AD 集成提供以下优势：

* 可在 Azure AD 中控制谁有权访问 SuccessFactors
* 可以让用户使用其 Azure AD 帐户自动登录到 SuccessFactors（单一登录）
* 可以在一个中心位置（即 Azure 经典门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件
若要配置 Azure AD 与 SuccessFactors 的集成，需备齐以下项目：

* 一个有效的 Azure 订阅
* SuccessFactors 中的租户

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

1. 从库添加 SuccessFactors
2. 配置并测试 Azure AD 单一登录

## <a name="adding-successfactors-from-the-gallery"></a>从库添加 SuccessFactors
若要配置 SuccessFactors 与 Azure AD 的集成，需要从库中将 SuccessFactors 添加到托管 SaaS 应用列表。

**若要从库中添加 SuccessFactors，请执行以下步骤：**

1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
    ![配置单一登录][1]
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![配置单一登录][2]
4. 在页面底部单击“添加”。
   
    ![应用程序][3]
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![配置单一登录][4]
6. 在“搜索”框中，键入“SuccessFactors”。
   
    ![配置单一登录][5]
7. 在结果面板中，选择“SuccessFactors”，然后单击“完成”以添加该应用程序。
   
    ![配置单一登录][6]

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置并测试 Azure AD 单一登录
本部分的目的是说明如何根据名为“Britta Simon”的测试用户的情况，配置和测试 SuccessFactors 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 SuccessFactors 用户。 换句话说，需要建立 Azure AD 用户与 SuccessFactors 中相关用户之间的链接关系。

通过将 Azure AD 中“用户名”的值分配为 SuccessFactors 中“用户名”的值来建立此链接关系。

若要配置和测试 SuccessFactors 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-single-sign-on)** - 让用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 SuccessFactors 测试用户](#creating-a-successfactors-test-user)** - 使链接到 Britta Simon 的 Azure AD 表示形式的 SuccessFactors 中具有 Britta Simon 的对应用户。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录
在本部分中，将在经典门户中启用 Azure AD 单一登录，并在 SuccessFactors 应用程序中配置单一登录。

**若要配置 SuccessFactors 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 经典门户中的“SuccessFactors”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
    ![配置单一登录][7]
2. 在“你希望用户如何登录 SuccessFactors”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录][8]
3. 在“配置应用 URL”页上，执行以下步骤，然后单击“下一步”。
   
    ![配置单一登录][9]
   
    a. 在“登录 URL”文本框中，使用以下一种模式键入 URL： 
   
    |  |
    | --- |
    | `https://<company name>.successfactors.com/<company name>` |
    | `https://<company name>.sapsf.com/<company name>` |
    | `https://<company name>.successfactors.eu/<company name>` |
    | `https://<company name>.sapsf.eu` |
   
    b. 在“回复 URL”文本框中，使用以下一种模式键入 URL： 
   
    |  |
    | --- |
    | `https://<company name>.successfactors.com/<company name>` |
    | `https://<company name>.sapsf.com/<company name>` |
    | `https://<company name>.successfactors.eu/<company name>` |
    | `https://<company name>.sapsf.eu` |
    | `https://<company name>.sapsf.eu/<company name>` |
   
    c. 单击“下一步”。 

    > [!NOTE]
    > 请注意，这些不是实际值。 必须使用实际登录 URL 和回复 URL 更新这些值。 若要获取这些值，请联系 [SuccessFactors 支持团队](https://www.successfactors.com/en_us/support.html)。

1. 在“在 SuccessFactors 处配置单一登录”页上，单击“下载证书”，然后将证书文件本地保存在计算机上。
   
    ![配置单一登录][10]

2. 在另一个 Web 浏览器窗口中，以管理员身份登录 **SuccessFactors 管理门户**。

3. 请访问“应用程序安全性”并从本机转到“单一登录功能”。 

4. 将任何值放在“重置令牌”中，然后单击“保存令牌”以启用 SAML SSO。
   
    ![在应用端配置单一登录][11]

    > [!NOTE] 
    > 此值仅用作打开/关闭切换。 如果保存了任何值，则 SAML SSO 为打开。 如果保存为空值，则 SAML SSO 为关闭。

1. 从本机转到以下屏幕快照并执行以下操作。
   
    ![在应用端配置单一登录][12]
   
    a. 选择“SAML v2 SSO”单选按钮
   
    b. 设置 SAML 声明方名称（例如，SAml 颁发者+公司名称）。
   
    c. 在“SAML 颁发者”文本框中放置 Azure AD 应用程序配置向导中“颁发者 URL”的值。
   
    d.单击“下一步”。 对于“需要强制签名”，选择“响应（客户生成/IdP/AP）”。
   
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 对于“启用 SAML 标志”选择“启用”。
   
    f. 对于“登录请求签名（SF 生成/SP/RP）”选择“否”。
   
    g. 对于“SAML 配置文件”，选择“浏览器/Post 配置文件”。
   
    h. 对于“强制证书有效期限”，选择“否”。
   
    i. 复制已下载的证书文件的内容，然后将其粘贴到“SAML 验证证书”文本框。

    > [!NOTE] 
    > 证书内容必须具有开始证书和结束证书标记。

1. 导航到 SAML V2，然后执行以下步骤：
   
    ![在应用端配置单一登录][13]
   
    a. 对于“支持 SP 启动的全局注销”，选择“是”。
   
    b. 在“全局注销服务 URL（LogoutRequest 目标”文本框中放置 Azure AD 应用程序配置向导中“远程注销 URL”的值。
   
    c. 对于“要求 sp 必须加密所有 NameID 元素”选择“否”。
   
    d.单击“下一步”。 对于“NameID 格式”，选择“未指定”。
   
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 对于“启用 sp 启动的登录 (AuthnRequest)”选择“是”。
   
    f. 在“发送请求作为公司颁发者”文本框中放置 Azure AD 应用程序配置向导中“远程登录 URL”的值。
2. 如果希望登录用户名不区分大小写，请执行这些步骤。
   
    a. 请访问“公司设置”（靠近底部）。
   
    b. 选中“启用不区分大小写的用户名”旁边的复选框。
   
    c.单击“保存”。
   
    ![配置单一登录][29]

    > [!NOTE] 
    > 如果尝试启用此功能，系统将会检查是否会创建重复的 SAML 登录名。 例如，如果客户具有用户名 User1 和 user1。 取消区分大小写会认定它们重复。 系统将提供一条错误消息，且不会启用该功能。 客户需要更改其中一个用户名，以使其实际拼写不同。 

1. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
    ![应用程序][14]
2. 在“单一登录确认”页上，单击“完成”。
   
    ![应用程序][15]

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在经典门户中创建名为“Britta Simon”的测试用户。

![创建 Azure AD 用户][16]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。
   
    ![创建 Azure AD 测试用户][17]
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要显示用户列表，请在顶部菜单中，单击“用户”。
   
    ![创建 Azure AD 测试用户][18]
4. 若要打开“添加用户”对话框，请在底部工具栏中单击“添加用户”。
   
    ![创建 Azure AD 测试用户][19]
5. 在“告诉我们有关此用户的信息”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户][20]
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 对于“用户类型”，选择“组织中的新用户”。
   
    b. 在“用户名”文本框中，键入“BrittaSimon”。
   
    c. 单击“下一步”。
6. 在“用户配置文件”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户][21]
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名字”文本框中，键入“Britta”。  
   
    b. 在“姓氏”文本框中，键入“Simon”。
   
    c. 在“显示名称”文本框中，键入“Britta Simon”。
   
    d.单击“下一步”。 在“角色”列表中，选择“用户”。
   
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 单击“下一步”。
7. 在“获取临时密码”对话框页上，单击“创建”。
   
    ![创建 Azure AD 测试用户][22]
8. 在“获取临时密码”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户][23]
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 写下“新密码”的值。
   
    b. 单击“完成”。  

### <a name="creating-a-successfactors-test-user"></a>创建 SuccessFactors 测试用户
要使 Azure AD 用户能够登录 SuccessFactors，必须将这些用户预配到 SuccessFactors 中。  
对于 SuccessFactors，预配任务需要手动完成。

若要在 SuccessFactors 中创建用户，需要联系 [SuccessFactors 支持团队](https://www.successfactors.com/en_us/support.html)。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户
本部分的目的是通过授予 Britta Simon 访问 SuccessFactors 的权限，允许她使用 Azure 单一登录。

![分配用户][24]

**若要将 Britta Simon 分配到 SuccessFactors，请执行以下步骤：**

1. 在经典门户中，若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![分配用户][25]
2. 在应用程序列表中，选择“SuccessFactors”。
   
    ![配置单一登录][26]
3. 在顶部菜单中，单击“用户”。
   
    ![分配用户][27]
4. 在“用户”列表中，选择“Britta Simon”。
5. 在底部工具栏中，单击“分配”。
   
    ![分配用户][28]

### <a name="testing-single-sign-on"></a>测试单一登录
本部分的目的是使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的“SuccessFactors”磁贴时，应会自动登录 SuccessFactors 应用程序。

## <a name="additional-resources"></a>其他资源
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[0]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_00.png
[1]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_01.png
[6]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_02.png
[7]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_03.png
[8]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_04.png
[9]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_05.png
[10]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_06.png

[11]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_09.png
[14]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_05.png
[15]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_06.png

[16]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_00.png
[17]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_01.png
[18]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_02.png
[19]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_03.png
[20]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_04.png
[21]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_05.png
[22]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_06.png
[23]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_07.png

[24]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_07.png
[25]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_08.png
[26]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_11.png
[27]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_09.png
[28]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_10.png
[29]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_10.png

