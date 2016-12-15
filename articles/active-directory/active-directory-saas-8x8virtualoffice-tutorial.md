---
title: "教程：Azure Active Directory 与 8x8 Virtual Office 的集成 | Microsoft 文档"
description: "了解如何在 Azure Active Directory 和 8x8 Virtual Office 之间配置单一登录。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: b34a6edf-e745-4aec-b0b2-7337473d64c5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2f8d126b9c2cfe5aaa6ef8f72f23ae7d66d6c524


---
# <a name="tutorial-azure-active-directory-integration-with-8x8-virtual-office"></a>教程：Azure Active Directory 与 8x8 Virtual Office 的集成
本教程用于向你显示如何将 8x8 Virtual Office 与 Azure Active Directory (Azure AD) 集成。

将 8x8 Virtual Office 与 Azure AD 集成可带来以下好处：

* 可在 Azure AD 中控制谁有权访问 8x8 Virtual Office
* 可让你的用户使用其 Azure AD 帐户自动登录到 8x8 Virtual Office（单一登录）
* 可在一个中心位置（即 Azure 经典门户）管理你的帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件
若要配置 Azure AD 与 8x8 Virtual Office 的集成，需要以下各项：

* 一个 Azure AD 订阅
* 启用了 8x8 Virtual Office 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。
> 
> 

测试本教程中的步骤应遵循以下建议：

* 不应使用生产环境，除非有此必要。
* 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
本教程旨在使你能够在测试环境中测试 Microsoft Azure AD 单一登录。

本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 8x8 Virtual Office
2. 配置和测试 Microsoft Azure AD 单一登录

## <a name="adding-8x8-virtual-office-from-the-gallery"></a>从库中添加 8x8 Virtual Office
若要配置 8x8 Virtual Office 到 Azure AD 的集成，需要将 8x8 Virtual Office 从库中添加到托管的 SaaS 应用列表。

**若要从库中添加 8x8 Virtual Office，请执行以下步骤：**

1. 在 **Azure 经典门户**的左侧导航窗格上，单击“Active Directory”。
   
    ![Active Directory][1]
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序][2]
4. 在页面底部单击“添加”。
   
    ![应用程序][3]
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![应用程序][4]
6. 在搜索框中，键入 **8x8 Virtual Office**。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_01.png)
7. 在结果窗格中，选择“8x8 Virtual Office”，然后单击“完成”以添加应用程序。
   
    ![在库中选择应用](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_0001.png)

## <a name="configuring-and-testing-microsoft-azure-ad-single-sign-on"></a>配置和测试 Microsoft Azure AD 单一登录
本部分用于向你显示如何根据名为“Britta Simon”的测试用户使用 8x8 Virtual Office 配置和测试 Microsoft Azure AD 单一登录。

若要使单一登录生效，Azure AD 需要了解 Azure AD 中的用户对应于 8x8 Virtual Office 中的哪个用户。 换言之，需要建立 Azure AD 用户与 8x8 Virtual Office 中的相关用户之间的链接关系。

通过将 Azure AD 中的 **user name** 值分配为 8x8 Virtual Office 中的 **Username** 值建立此链接关系。

若要使用 8x8 Virtual Office 配置和测试 Microsoft Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Microsof Azure AD 单一登录](#configuring-azure-ad-single-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Microsoft Azure AD 单一登录。
3. **[创建 8x8 Virtual Office 测试用户](#creating-a-8x8-virtual-office-test-user)** - 在 8x8 Virtual Office 中有一个已链接到 Azure AD 中 Britta Simon 表示形式的对应项。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Microsoft Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-microsoft-azure-ad-single-sign-on"></a>配置 Microsoft Azure AD 单一登录
在本部分中，你在经典门户中启用 Microsoft Azure AD 单一登录，并在 8x8 Virtual Office 应用程序中配置单一登录。

**若要配置 Microsoft Azure AD 与 8x8 Virtual Office 的集成，请执行以下步骤︰**

1. 在经典门户中，在“8x8 Virtual Office”应用程序集成页上，单击“配置单一登录”打开“配置单一登录”对话框。
   
    ![配置单一登录][6] 
2. 在“希望用户如何登录到 8x8 Virtual Office”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_03.png) 
3. 在“配置应用设置”对话框页上，执行以下步骤，然后单击“下一步”：
   
    ![配置单一登录](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_04.png)
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“回复 URL”文本框中，键入：`https://sso.8x8.com/saml2`
   
    b. 单击“下一步”
4. 在“在 8x8 Virtual Office 上配置单一登录”页上，执行以下步骤，然后单击“下一步”：
   
    ![配置单一登录](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_05.png)
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 单击“下载证书”，然后将文件保存在计算机上。
   
    b. 单击“资源组名称” 的 Azure 数据工厂。
5. 以管理员身份登录到 8x8 Virtual Office 租户。
6. 在“应用程序”面板上选择“Virtual Office 帐户管理器”。
   
    ![在应用端上配置](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)
7. 选择要管理的“企业”帐户，然后单击“登录”按钮。
   
    ![在应用端上配置](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)
8. 在菜单列表中单击“帐户”选项卡。
   
    ![在应用端上配置](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)
9. 在帐户列表中单击“单一登录”。
   
    ![在应用端上配置](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)
10. 在“身份验证”方法下选择“单一登录”，然后单击“SAML”。
    
    ![在应用端上配置](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)
11. 将“SAML SSO URL”、“单一注销服务 URL”和“颁发者 URL”从 Azure AD 复制到 8x8 Virtual Office 中的“登录 URL”、“注销 URL”和“颁发者 URL”。 
    
    ![在应用端上配置](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)
    
    ![在应用端上配置](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_007.png)
12. 单击“浏览器”按钮，上传已从 Azure AD 下载的证书。
13. 单击“保存”按钮  。
14. 在经典门户中，选择单一登录配置确认，然后单击“下一步”。
    
    ![Azure AD 单一登录][10]
15. 在“单一登录确认”页上，单击“完成”。  
    
    ![Azure AD 单一登录][11]

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在经典门户中创建名为“Britta Simon”的测试用户。

![创建 Azure AD 用户][20]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_09.png)
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要显示用户列表，请在顶部菜单中，单击“用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_03.png)
4. 若要打开“添加用户”对话框，请在底部工具栏中单击“添加用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_04.png)
5. 在“告诉我们有关此用户的信息”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_05.png)
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 对于“用户类型”，选择“组织中的新用户”。
   
    b. 在“用户名”文本框中，键入“BrittaSimon”。
   
    c. 单击“资源组名称” 的 Azure 数据工厂。
6. 在“用户配置文件”对话框页上，执行以下步骤：
   
   ![创建 Azure AD 测试用户](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_06.png)
   
   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名字”文本框中，键入“Britta”。  
   
   b. 在“姓氏”文本框中，键入“Simon”。
   
   c. 在“显示名称”文本框中，键入“Britta Simon”。
   
   d.单击“下一步”。 在“角色”列表中，选择“用户”。
   
   e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 单击“资源组名称” 的 Azure 数据工厂。
7. 在“获取临时密码”对话框页上，单击“创建”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_07.png)
8. 在“获取临时密码”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_08.png)
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 写下“新密码”的值。
   
    b. 单击“完成”。   

### <a name="creating-a-8x8-virtual-office-test-user"></a>创建 8x8 Virtual Office 测试用户
本部分要在 8x8 Virtual Office 中创建名为 Britta Simon 的用户。 8x8 Virtual Office 支持在默认情况下启用的实时预配。

此部分不存在任何操作项。 如果尚不存在用户，将在尝试访问 8x8 Virtual Office 期间创建一个新用户。 

> [!NOTE]
> 如果需要手动创建用户，你需要联系 8x8 Virtual Office 支持团队。
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户
本部分用于通过向 Britta Simon 授权访问 8x8 Virtual Office 让她使用 Azure 单一登录。

![分配用户][200]

**若要将 Britta Simon 分配到 8x8 Virtual Office，请执行以下步骤：**

1. 在经典门户中，若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![分配用户][201]
2. 在应用程序列表中，选择“8x8 Virtual Office”。
   
    ![配置单一登录](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_50.png)
3. 在顶部菜单中，单击“用户”。
   
    ![分配用户][203]
4. 在“用户”列表中，选择“Britta Simon”。
5. 在底部工具栏中，单击“分配”。
   
    ![分配用户][205]

### <a name="testing-single-sign-on"></a>测试单一登录
本部分用于使用“访问面板”测试 Microsoft Azure AD 单一登录配置。

在“访问面板”中单击 8x8 Virtual Office 磁贴时，应自动登录到 8x8 Virtual Office 应用程序。

## <a name="additional-resources"></a>其他资源
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->


