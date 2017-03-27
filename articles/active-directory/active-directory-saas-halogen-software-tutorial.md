---
title: "教程：Azure Active Directory 与 Halogen Software 集成"
description: "了解如何在 Azure Active Directory 和 Halogen Software 之间配置单一登录。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 2ca2298d-9a0c-4f14-925c-fa23f2659d28
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 06d4b7495f4201387500944758ba2a0916b619d2
ms.openlocfilehash: cbe20975792ab0f5fe8fac64110ede3a964d443a
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-halogen-software"></a>教程：Azure Active Directory 与 Halogen Software 集成
本教程的目的是展示如何将 Halogen Software 与 Azure Active Directory (Azure AD) 进行集成。

将 Halogen Software 与 Azure AD 集成具有以下优势： 

* 可以在 Azure AD 中控制谁有权访问 Halogen Software 
* 可以让用户使用其 Azure AD 帐户自动登录到 Halogen Software 单一登录 (SSO)
* 可以在一个中心位置（即 Azure 经典门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件
若要配置 Azure AD 与 Halogen Software 的集成，需要具有以下项：

* Azure AD 订阅
* 已启用 Halogen Software SSO 的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。
> 
> 

测试本教程中的步骤应遵循以下建议：

* 不应使用生产环境，除非有此必要。
* 如果没有 Azure AD 试用环境，可以获取[一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。 

## <a name="scenario-description"></a>方案描述
本教程旨在介绍如何在测试环境中测试 Azure AD SSO。 

本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Halogen Software 
2. 配置和测试 Azure AD SSO

## <a name="adding-halogen-software-from-the-gallery"></a>从库中添加 Halogen Software
若要配置 Halogen Software 与 Azure AD 的集成，需要从库中将 Halogen Software 添加到托管 SaaS 应用列表。

**若要从库中添加 Halogen Software，请执行以下步骤：**

1. 在 **Azure 经典门户**的左侧导航窗格上，单击“Active Directory”。 
   
    ![Active Directory][1]
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序][2]
4. 在页面底部单击“添加”。    
   
    ![应用程序][3]
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![应用程序][4]
6. 在搜索框中，键入“halogen software”。
   
    ![应用程序][5]
7. 在结果窗格中，选择“Halogen Software”，然后单击“完成”以添加该应用程序。

## <a name="configure-and-test-azure-ad-sso"></a>配置和测试 Azure AD SSO
本部分旨在说明如何基于名为“Britta Simon”的测试用户配置和测试 Halogen Software 的 Azure AD SSO。

若要运行 SSO，Azure AD 需要知道与 Azure AD 用户相对应的 Halogen Software 用户。 换句话说，需要在 Azure AD 用户与 Halogen Software 中的相关用户之间建立关联关系。

可以通过将 Azure AD 中“用户名”的值分配为 Halogen Software 中“用户名”的值来建立此关联关系。

若要配置和测试 Halogen Software 的 Azure AD SSO，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Halogen Software 测试用户](#creating-a-halogen-software-test-user)** - 在 Halogen Software 中创建 Britta Simon 的对应用户，将其链接到其 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO
本部分旨在介绍如何在 Azure 经典门户中启用 Azure AD SSO 并在 Halogen Software 应用程序中配置 SSO。

**若要配置 Halogen Software 的 Azure AD SSO，请执行以下步骤：**

1. 在 Azure 经典门户中的“Halogen Software”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
    ![配置单一登录][8]
2. 在“你希望用户如何登录到 Halogen Software”页上，选择“Azure AD 单一登录”，然后单击“下一步”。
   
    ![Azure AD 单一登录][9]
3. 在“配置应用设置”对话框页上，执行以下步骤：

    ![配置应用设置][10]
   1. 在“登录 URL”文本框中，使用以下模式键入用户用于登录 Halogen Software 应用程序的 URL：*https://global.hgncloud.com/fabrikam/welcome.jsp*
   2. 单击“资源组名称” 的 Azure 数据工厂。
4. 在“配置 Halogen Software 的单一登录”**页面上，单击“下载元数据”**，然后将元数据文件本地保存在计算机上。
   
    ![什么是 Azure AD Connect][11]
5. 在另一个浏览器窗口中，以管理员身份登录到 **Halogen Software** 应用程序。
6. 单击“**选项**”选项卡。 
   
    ![什么是 Azure AD Connect][12]
7. 在左侧导航窗格中，单击“SAML 配置”。 
   
    ![什么是 Azure AD Connect][13]
8. 在“SAML 配置”页上，执行以下步骤： 

    ![什么是 Azure AD Connect][14]
  1. 选择 **NameID** 作为**唯一标识符**。
  2. 对于“唯一标识符映射到”，请选择“用户名”。
  3. 若要上载已下载的元数据文件，请单击“浏览”选择该文件，然后单击“上载文件”。
  4. 若要测试配置，请单击“运行测试”。 
    >[!NOTE]
    >需要等待消息“*SAML 测试已完成。请关闭此窗口*”。 然后，关闭打开的浏览器窗口。 仅在测试完成的情况下，才会启用“启用 SAML”复选框。 
    >
  5. 选择“启用 SAML”。
  6. 单击“保存更改”。 
9. 在 Azure 经典门户中，选择单一登录配置确认，然后单击“完成”，关闭“配置单一登录”对话框。 
   
    ![什么是 Azure AD Connect][15]
10. 在“单一登录确认”页上，单击“完成”。  
    
    ![什么是 Azure AD Connect][16]

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 经典门户中创建名为 Britta Simon 的测试用户。

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。
   
    ![什么是 Azure AD Connect][100] 
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要显示用户列表，请在顶部菜单中单击“用户”。
   
    ![什么是 Azure AD Connect][101] 
4. 若要打开“添加用户”对话框，请在底部工具栏中单击“添加用户”。 
   
    ![什么是 Azure AD Connect][102] 
5. 在“告诉我们有关此用户的信息”对话框页上，执行以下步骤：
   
    ![什么是 Azure AD Connect][103] 
  1. 对于“用户类型”，选择“组织中的新用户”。
  2. 在“用户名”文本框中，键入“BrittaSimon”。
  3. 单击“下一步”。
6. 在“用户配置文件”对话框页上，执行以下步骤： 
   
   ![什么是 Azure AD Connect][104] 
  1. 在“名字”文本框中，键入“Britta”。  
  2. 在“姓氏”文本框中，键入 **Simon**。
  3. 在“显示名称”文本框中，键入“Britta Simon”。
  4. 在“角色”列表中，选择“用户”。
  5. 单击“资源组名称” 的 Azure 数据工厂。
7. 在“获取临时密码”对话框页上，单击“创建”。
   
    ![什么是 Azure AD Connect][105]  
8. 在“获取临时密码”对话框页上，执行以下步骤：
   
    ![什么是 Azure AD Connect][106]   
  1. 写下“新密码”的值。
  2. 单击“完成”。   

### <a name="create-a-halogen-software-test-user"></a>创建 Halogen Software 测试用户
本部分的目的是在 Halogen Software 中创建名为“Britta Simon”的用户。

**若要在 Halogen Software 中创建名为“Britta Simon”的用户，请执行以下步骤：**

1. 以管理员身份登录到 **Halogen Software** 应用程序。
2. 单击“用户中心”选项卡，然后单击“创建用户”。
   
    ![什么是 Azure AD Connect][300]  
3. 在“新建用户”对话框页上，执行以下步骤：
   
    ![什么是 Azure AD Connect][301]
  1. 在“名字”文本框中，键入“Britta”。 
  2. 在“姓氏”文本框中，键入“Simon”。 
  3. 在 Azure 经典门户的“用户名”文本框中，键入 Britta Simon 的用户名。
  4. 在“密码”文本框中，键入 Britta 的密码。
  5. 单击“保存” 。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户
本部分旨在通过授予 Britta Simon 访问 Halogen Software 的权限，允许她使用 Azure SSO。

![什么是 Azure AD Connect][200]

**若要将 Britta Simon 分配到 Halogen Software，请执行以下步骤：**

1. 在 Azure 经典门户中，若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![什么是 Azure AD Connect][201]
2. 在应用程序列表中，选择“Halogen Software”。
   
    ![什么是 Azure AD Connect][202]
3. 在顶部菜单中，单击“用户”。
   
    ![什么是 Azure AD Connect][203]
4. 在“用户”列表中，选择“Britta Simon”。
   
    ![什么是 Azure AD Connect][204]
5. 在底部工具栏中，单击“分配”。
   
    ![什么是 Azure AD Connect][205]

### <a name="test-single-sign-on"></a>测试单一登录
本部分旨在使用“访问面板”测试 Azure AD SSO 配置。

当在访问面板中单击 Halogen Software 磁贴时，应当会自动登录到 Halogen Software 应用程序。

## <a name="additional-resources"></a>其他资源
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_01.png
[2]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_02.png
[3]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_03.png
[4]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_04.png
[5]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_05.png
[6]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_06.png
[7]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_07.png
[8]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_08.png
[9]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_09.png
[10]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_10.png
[11]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_11.png
[12]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_12.png
[13]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_13.png
[14]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_14.png
[15]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_15.png
[16]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_16.png
[100]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_100.png 
[101]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_101.png 
[102]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_102.png 
[103]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_103.png 
[104]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_104.png 
[105]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_105.png 
[106]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_106.png 
[200]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_200.png 
[201]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_201.png 
[202]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_202.png
[203]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_203.png
[204]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_204.png
[205]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_205.png
[300]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_300.png
[301]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_301.png

