---
title: "教程：Azure Active Directory 与 Promapp 集成 | Microsoft 文档"
description: "了解如何在 Azure Active Directory 和 Promapp 之间配置单一登录。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 418d0601-6e7a-4997-a683-73fa30a2cfb5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/20/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 04a045f41965b093aab71e59cd9b5f328b44de84
ms.openlocfilehash: ac73b5c2ece8044d9f75e017428c43259f8a9357


---
# <a name="tutorial-azure-active-directory-integration-with-promapp"></a>教程：Azure Active Directory 与 Promapp 的集成
本教程的目的是介绍如何将 Promapp 与 Azure Active Directory (Azure AD) 集成。  
将 Promapp 与 Azure AD 集成具有以下优势： 

* 可在 Azure AD 中控制谁有权访问 Promapp 
* 可以让用户通过其 Azure AD 帐户自动登录到 Promapp（单一登录）
* 可以在一个中心位置（即 Azure Active Directory 经典门户）管理帐户

如果想要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [What is application access and single sign-on with Azure Active Directory](active-directory-appssoaccess-whatis.md)（什么是使用 Azure Active Directory 的应用程序访问和单一登录）。

## <a name="prerequisites"></a>先决条件
若要配置 Azure AD 与 Promapp 的集成，需备齐以下项目：

* Azure AD 订阅
* 启用 Promapp 单一登录的订阅

> [!NOTE]
> 测试本教程中的步骤时，建议不要使用生产环境。
> 
> 

测试本教程中的步骤应遵循以下建议：

* 不应使用生产环境，除非有此必要。
* 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。 

## <a name="scenario-description"></a>方案描述
本教程的目的是介绍如何在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Promapp 
2. 配置并测试 Azure AD 单一登录

## <a name="adding-promapp-from-the-gallery"></a>从库中添加 Promapp
若要配置 Promapp 与 Azure AD 的集成，需要从库中将 Promapp 添加到托管 SaaS 应用列表。

**若要从库中添加 Promapp，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。 
   
    ![Active Directory][1]
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序][2]
4. 在页面底部单击“添加”。
   
    ![应用程序][3]
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![应用程序][4]
6. 在搜索框中，键入“Promapp”。
   
    ![应用程序][5]
7. 在结果窗格中，选择“Promapp”，然后单击“完成”添加该应用程序。
   
    ![应用程序][500]

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置并测试 Azure AD 单一登录
本部分的目的是介绍如何使用 Promapp 根据名为“Britta Simon”的测试用户的情况配置和测试 Azure AD 单一登录。

若要使用单一登录，Azure AD 需要了解与 Azure AD 中的用户相对应的 Promapp 中的用户是谁。 换句话说，需要建立 Azure AD 用户与 Promapp 中相关用户之间的关联关系。  
将 Azure AD 中“用户名”的值指定为 Promapp 中“用户名”的值，即可建立此关联关系。

若要使用 Promapp 配置和测试 Azure AD 单一登录，需完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-single-sign-on)** - 让用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Promapp 测试用户](#creating-a-halogen-software-test-user)** - 目的是在 Promapp 中有一个与 Azure AD 中的 Britta Simon 相对应的关联用户。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录
本部分的目的是在 Azure 经典门户中启用 Azure AD 单一登录并在 Promapp 应用程序中配置单一登录。

**若要使用 Promapp 配置 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure AD 经典门户中的“Promapp”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
    ![配置单一登录][6] 
2. 在“你希望用户如何登录 Promapp”页上，选择“Azure AD 单一登录”，然后单击“下一步”。
   
    ![Azure AD 单一登录][7] 
3. 在“配置应用设置”对话框页上，执行以下步骤：
   
    ![Azure AD 单一登录][8] 
   
   1. 在“登录 URL”文本框中，使用以下模式键入用户用于登录 Promapp 站点的 URL（例如：*https://companyname.promapp.com/instancename*）。
   2. 单击“资源组名称” 的 Azure 数据工厂。

1. 在“配置 Promapp 的单一登录”页上，执行以下步骤：
   
    ![Azure AD 单一登录][9] 
   
   1. 单击“下载证书”，然后将文件保存在计算机上。
   2. 单击“资源组名称” 的 Azure 数据工厂。
   
2. 以管理员身份登录 Promapp 公司站点。 
3. 在顶部菜单中，单击“管理员”。 
   
    ![Azure AD 单一登录][12]
4. 单击 **“配置”**。 
   
    ![Azure AD 单一登录][13]
5. 在“安全”对话框中，执行以下步骤：
   
    ![Azure AD 单一登录][14] 
   
   1. 在 Azure 经典门户的“配置 Promapp 的单一登录”对话框中，复制“远程登录 URL”值并将其粘贴到“SSO-登录 URL”文本框中，然后单击“保存”。
   2. 选择“可选”作为“SSO - 单一登录模式”，然后单击“保存”。
   3. 在记事本中打开下载的证书，复制除第一行 (*-----BEGIN CERTIFICATE-----*) 和最后一行 (*-----END CERTIFICATE-----*) 之外的证书内容，将其粘贴到“SSO-x.509 证书”文本框中，然后单击“保存”。
6. 在 Azure AD 经典门户中，选择“单一登录配置确认”，然后单击“下一步”。 
   
    ![Azure AD 单一登录][10]
7. 在“单一登录确认”页上，单击“完成”。  
   
    ![Azure AD 单一登录][11]

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 经典门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][20]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-promapp-tutorial/create_aaduser_09.png)  
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要显示用户列表，请在顶部菜单中，单击“用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-promapp-tutorial/create_aaduser_03.png) 
4. 若要打开“添加用户”对话框，请在底部工具栏中单击“添加用户”。 
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-promapp-tutorial/create_aaduser_04.png) 
5. 在“告诉我们有关此用户的信息”对话框页上，执行以下步骤： 
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-promapp-tutorial/create_aaduser_05.png)  
   
   1. 在“用户类型”中，选择“你的组织中的新用户”。
   2. 在“用户名”文本框中，键入“BrittaSimon”。
   3. 单击“下一步”。
   
6. 在“用户配置文件”对话框页上，执行以下步骤： 
   
   ![创建 Azure AD 测试用户](./media/active-directory-saas-promapp-tutorial/create_aaduser_06.png) 
   
   1. 在“名字”文本框中，键入“Britta”。   
   2. 在“姓氏”文本框中，键入“Simon”。
   3. 在“显示名称”文本框中，键入“Britta Simon”。
   4. 在“角色”列表中，选择“用户”。
   5. 单击“下一步”。
   
7. 在“获取临时密码”对话框页上，单击“创建”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-promapp-tutorial/create_aaduser_07.png) 
8. 在“获取临时密码”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-promapp-tutorial/create_aaduser_08.png) 
   
   1. 写下“新密码”的值。
   2. 单击“完成”。   

### <a name="creating-a-promapp-test-user"></a>创建 Promapp 测试用户
Promapp 应用程序支持实时预配。
这意味着，在尝试使用访问面板访问应用程序期间，如有必要，将会自动创建一个用户帐户。  

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户
本部分的目的是通过授予 Britta Simon 访问 Promapp 的权限，允许她使用 Azure 单一登录。

![分配用户][200] 

**若要将 Britta Simon 分配到 Promapp，请执行以下步骤：**

1. 在 Azure 经典门户中，若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![分配用户][201] 
2. 在应用程序列表中，选择“Promapp”。
   
    ![分配用户][202] 
3. 在顶部菜单中，单击“用户”。
   
    ![分配用户][203] 
4. 在“用户”列表中，选择“Britta Simon”。
5. 在底部工具栏中，单击“分配”。
   
    ![分配用户][205]

### <a name="testing-single-sign-on"></a>测试单一登录
本部分的目的是使用访问面板测试 Azure AD 单一登录配置。  
单击访问面板中的“Promapp”磁贴时，用户就会自动登录到 Promapp 应用程序。

## <a name="additional-resources"></a>其他资源
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_01.png

[500]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_500.png

[6]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_02.png
[8]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_03.png
[9]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_04.png
[10]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_07.png
[12]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_05.png
[13]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_06.png
[14]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_07.png

[20]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_10.png
[203]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_400.png
[401]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_401.png
[402]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_402.png



<!--HONumber=Feb17_HO1-->


