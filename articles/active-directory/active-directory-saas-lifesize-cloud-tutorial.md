---
title: "教程：Azure Active Directory 与 Lifesize Cloud 的集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 Lifesize Cloud 之间配置单一登录。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 75fab335-fdcd-4066-b42c-cc738fcb6513
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 7cc133d6289bffbc3b7fc591104bc51ebfc67ddd
ms.openlocfilehash: 8edbee8e554a7818b97669a4bb64b31ada67b1a5
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-lifesize-cloud"></a>教程：Azure Active Directory 与 Lifesize Cloud 的集成
本教程介绍了如何将 Lifesize Cloud 与 Azure Active Directory (Azure AD) 进行集成。

将 Lifesize Cloud 与 Azure AD 集成可提供以下优势：

* 可以在 Azure AD 中控制谁有权访问 Lifesize Cloud
* 可以让用户使用其 Azure AD 帐户自动登录到 Lifesize Cloud 单一登录 (SSO)
* 可以在一个中心位置（即 Azure 经典门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件
若要配置 Azure AD 与 Lifesize Cloud 的集成，需要具有以下项：

* Azure AD 订阅
* 已启用 Lifesize Cloud 单一登录 (SSO) 的订阅

>[!NOTE]
>不建议使用生产环境测试本教程中的步骤。 
> 

测试本教程中的步骤应遵循以下建议：

* 不应使用生产环境，除非有此必要。
* 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。

本教程中概述的方案包括两个主要构建基块：

* 从库中添加 Lifesize Cloud
* 配置和测试 Azure AD SSO

## <a name="add-lifesize-cloud-from-the-gallery"></a>从库添加 Lifesize Cloud
若要配置 Lifesize Cloud 与 Azure AD 的集成，需要从库中将 Lifesize Cloud 添加到托管 SaaS 应用列表。

**若要从库中添加 Lifesize Cloud，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。
   
    ![Active Directory][1]
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序][2]
4. 在页面底部单击“添加”。
   
    ![应用程序][3]
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![应用程序][4]
6. 在搜索框中，键入“Lifesize Cloud”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_01.png)
7. 在结果窗格中，选择“Lifesize Cloud”，然后单击“完成”以添加该应用程序。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_02.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，将基于名为“Britta Simon”的测试用户配置并测试 Lifesize Cloud 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Lifesize Cloud 用户。 换句话说，需要在 Azure AD 用户与 Lifesize Cloud 中的相关用户之间建立链接关系。

可以通过将 Azure AD 中“用户名”的值分配为 Lifesize Cloud 中“用户名”的值来建立此链接关系。

若要配置并测试 Lifesize Cloud 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Lifesize Cloud 测试用户](#creating-a-lifesize-cloud-test-user)** - 在 Lifesize Cloud 中创建 Britta Simon 的对应用户，将其链接到其 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录
在本部分中，将在经典门户中启用 Azure AD SSO 并在 Lifesize Cloud 应用程序中配置单一登录。

**若要配置 Lifesize Cloud 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 经典门户中，在 **Lifesize Cloud** 应用程序集成页上，单击“配置单一登录”，以打开“配置单一登录”对话框。
   
    ![配置单一登录][6] 
2. 在“你希望用户如何登录到 Lifesize Cloud”页上，选择“Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_03.png) 
3. 在“配置应用设置”对话框页上，执行以下步骤：
   
   ![配置单一登录](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_04.png)   
  1. 在“登录 URL”文本框中，使用以下模式键入用户用来登录 Lifesize Cloud 应用程序的 URL：**https://login.lifesizecloud.com/ls/?acs**。
  2. 单击“下一步”。
4. 在“在 Lifesize Cloud 处配置单一登录”页上，执行以下步骤：
   
   ![配置单一登录](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_05.png)
   1. 单击“下载证书”，然后将文件保存在计算机上。
   2. 单击“下一步”。
5. 若要为应用程序配置 SSO，请使用管理员权限登录到 Lifesize Cloud 应用程序。
6. 在右上角单击你的名字，然后单击“高级设置”。
   
    ![配置单一登录](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_06.png)
7. 现在，在“高级设置”中，单击“SSO 配置”链接。 这将打开你的实例的 SSO 配置页。
   
    ![配置单一登录](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_07.png)
8. 现在，在 SSO 配置 UI 中配置以下值。    
   
    ![配置单一登录](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_08.png)
  1. 从 Azure AD 中复制颁发者 URL 的值并将其复制到“标识提供者颁发者”文本框中。 
  2. 从 Azure AD 中复制远程登录 URL 的值并将其复制到“登录 URL”文本框中。   
  3. 在记事本中打开下载的证书并复制证书的内容（不包括 Begin Certificate 和 End Certificate 行），将其粘贴到“X.509 证书”文本框中。
  4. 在 SAML 属性映射中，对于“名字”文本框，输入 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname** 作为值
  5. 在 SAML 属性映射中，对于“姓氏”文本框，输入 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname** 作为值
  6. 在 SAML 属性映射中，对于“电子邮件”文本框，输入 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** 作为值
9. 若要检查配置，可以单击“测试”按钮。
   
   >[!NOTE]
   >要想成功测试，需要在 Azure AD 中完成配置向导，还需要向可以执行测试的用户或组提供访问权限。
   >  
10. 通过单击“启用 SSO”按钮启用 SSO。
11. 现在，单击“更新”按钮以便保存所有设置。 这将生成 RelayState 值。 将生成的 RelayState 值复制到文本框中。 在后续步骤中将需要此值。
12. 在经典门户中，选择“单一登录配置确认”，然后单击“下一步”。
    
   ![Azure AD 单一登录][10]
13. 在“单一登录确认”页上，单击“完成”。  
    
    ![Azure AD 单一登录][11]
    
**登录到 Azure 管理门户：**

1. 使用管理员凭据登录到 **https://portal.azure.com**。
2. 单击左侧导航窗格中的“更多服务”。
    
    ![配置单一登录](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_09.png)
3. 搜索 Azure Active Directory 并单击“Azure Active Directory”链接。
    
    ![配置单一登录](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_10.png)
4. 可以在“企业应用程序”按钮下发现你的所有 SaaS 应用程序。
    
    ![配置单一登录](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_11.png)
5. 现在，单击旁边的边栏选项卡中的“所有应用程序”。
    
    ![配置单一登录](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_12.png)
6. 搜索要为其设置 RelayState 的 Lifesize 应用程序。 
    
    ![配置单一登录](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_13.png)
7. 现在，单击边栏选项卡中的“单一登录”链接。
    
    ![配置单一登录](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_14.png)
8. 将会出现“显示高级 URL 设置”复选框。 单击此复选框。
    
    ![配置单一登录](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_15.png)
9. 现在，为应用程序配置 RelayState，这位于 Lifesize 应用程序 SSO 配置页上。 
    
    ![配置单一登录](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_16.png)
10. 保存设置。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
在本部分中，将在经典门户中创建一个名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][20]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_09.png) 
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要显示用户列表，请在顶部菜单中，单击“用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_03.png) 
4. 若要打开“添加用户”对话框，请在底部工具栏中单击“添加用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_04.png) 
5. 在“告诉我们有关此用户的信息”对话框页上，执行以下步骤：

    ![创建 Azure AD 测试用户](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_05.png) 
   1. 在“用户类型”中，选择“你的组织中的新用户”。
   2. 在“用户名”文本框中，键入“BrittaSimon”。
   3. 单击“资源组名称” 的 Azure 数据工厂。
6. 在“用户配置文件”对话框页上，执行以下步骤：

   ![创建 Azure AD 测试用户](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_06.png) 
   1. 在“名字”文本框中，键入“Britta”。   
   2. 在“姓氏”文本框中，键入“Simon”。
   3. 在“显示名称”文本框中，键入“Britta Simon”。
   4. 在“角色”列表中，选择“用户”。
   5. 单击“资源组名称” 的 Azure 数据工厂。
7. 在“获取临时密码”对话框页上，单击“创建”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_07.png) 
8. 在“获取临时密码”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_08.png) 
    1. 写下“新密码”的值。
    2. 单击“完成”。   

### <a name="create-an-lifesize-cloud-test-user"></a>创建 Lifesize Cloud 测试用户
在本部分中，将在 Lifesize Cloud 中创建一个名为 Britta Simon 的用户。 Lifesize cloud 支持自动化用户预配。 在 Azure AD 中成功进行身份验证后，将自动在应用程序中预配用户。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户
在本部分中，将通过向 Britta Simon 授予对 Lifesize Cloud 的访问权限使她能够使用 Azure 单一登录。

![分配用户][200] 

**若要将 Britta Simon 分配到 Lifesize Cloud，请执行以下步骤：**

1. 在经典门户中，若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![分配用户][201] 
2. 在应用程序列表中，选择“Lifesize Cloud”。
   
    ![配置单一登录](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_50.png) 
3. 在顶部菜单中，单击“用户”。
   
    ![分配用户][203]
4. 在“用户”列表中，选择“Britta Simon”。
5. 在底部工具栏中，单击“分配”。
   
    ![分配用户][205]

### <a name="test-single-sign-on"></a>测试单一登录
在本部分中，使用访问面板测试 Azure AD SSO 配置。

当在访问面板中单击 Lifesize Cloud 磁贴时，应当会自动登录到 Lifesize Cloud 应用程序。

## <a name="additional-resources"></a>其他资源
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_205.png

