---
title: "教程：Azure Active Directory 与 MOVEit Transfer 集成 | Microsoft 文档"
description: "了解如何在 Azure Active Directory 和 MOVEit Transfer 之间配置单一登录。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 8ff7102d-be73-4888-ae81-d8e3d01dd534
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2d8d925f80830a0d7047e9567fdd413af2e8c5c3
ms.openlocfilehash: e1b4cda400205611a957c23d9a9b90c918d260f3
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer"></a>教程：Azure Active Directory 与 MOVEit Transfer 的集成
本教程的目的是展示如何将 MOVEit Transfer 与 Azure Active Directory (Azure AD) 进行集成。

将 MOVEit Transfer 与 Azure AD 集成可提供以下优势：

* 可以在 Azure AD 中控制谁有权访问 MOVEit Transfer
* 可以让用户使用其 Azure AD 帐户自动登录到 MOVEit Transfer 单一登录 (SSO)
* 可以在一个中心位置（即 Azure 经典门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件
若要配置 Azure AD 与 MOVEit Transfer 的集成，需要具有以下项：

* Azure AD 订阅
* 启用了 MOVEit Transfer SSO 的订阅

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

1. 从库中添加 MOVEit Transfer
2. 配置和测试 Azure AD SSO

## <a name="adding-moveit-transfer-from-the-gallery"></a>从库中添加 MOVEit Transfer
若要配置 MOVEit Transfer 与 Azure AD 的集成，需要从库中将 MOVEit Transfer 添加到托管 SaaS 应用列表。

**若要从库中添加 MOVEit Transfer，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。 
   
    ![Active Directory][1]
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序][2]
4. 在页面底部单击“添加”。
   
    ![应用程序][3]
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![应用程序][4]
6. 在搜索框中，键入“MOVEit Transfer”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_01.png)
7. 在结果面板中，选择“MOVEit Transfer”，然后单击“完成”以添加应用程序。
   
    ![在库中选择应用](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_0001.png)

## <a name="configure-and-test-azure-ad-sso"></a>配置和测试 Azure AD SSO
本部分旨在说明如何基于名为“Britta Simon”的测试用户配置和测试 MOVEit Transfer 的 Azure AD SSO。

若要运行 SSO，Azure AD 需要知道与 Azure AD 用户相对应的 MOVEit Transfer 用户。 换句话说，需要在 Azure AD 用户与 MOVEit 中的相关用户之间建立链接关系。

可以通过将 Azure AD 中“用户名”的值分配为 MOVEit Transfer 中“用户名”的值来建立此链接关系。

若要配置和测试 MOVEit Transfer 的 Azure AD SSO，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-single-sign-on)** - 让用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 MOVEit Transfer 测试用户](#creating-a-moveit-transfer-test-user)** - 在 MOVEit Transfer 中创建 Britta Simon 的对应用户，将其链接到其 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-sso"></a>配置 Azure AD SSO
在本部分中，将在经典门户中启用 Azure AD 单一登录并在 MOVEit Transfer 应用程序中配置 SSO。

**若要配置 MOVEit Transfer 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 经典门户中，在 **MOVEit Transfer** 应用程序集成页上，单击“配置单一登录”，以打开“配置单一登录”对话框。
   
    ![配置单一登录][6] 
2. 在“你希望用户如何登录到 MOVEit Transfer”页上，选择“Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_03.png)
3. 在“配置应用设置”对话框页上，执行以下步骤，然后单击“下一步”：
   
    ![配置单一登录](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_04.png)
  1. 在“登录 URL”文本框中，键入包含你自己的域的登录 URL。
  2. 在“标识符”文本框中，键入一个实体 ID URL。
  3. 在“回复 URL”文本框中，键入一个已启用的断言使用者界面 URL。
  4. 单击“下一步”。
   
   >[!NOTE]
   >请注意，必须使用实际登录 URL 和标识符更新这些值。 若要获取这些值，可以参考步骤 8 以了解更多详细信息或者联系 [MOVEit Transfer](https://www.ipswitch.com/support/technical-support)。
   >  
4. 在“在 MOVEit Transfer 处配置单一登录”页上，执行以下步骤并单击“下一步”：
   
  ![配置单一登录](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_05.png)  
  1. 单击“下载元数据”，然后在计算机上保存该文件。
  2. 单击“下一步”。
5. 以管理员身份登录到 MOVEit Transfer 租户。
6. 在左侧导航窗格上，单击“设置”。
   
  ![在应用端配置单一登录](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_000.png)
7. 单击位于“安全策略”->“用户身份验证”下的“单一登录”链接。
   
  ![在应用端配置单一登录](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_001.png)
8. 单击元数据 URL 链接以下载元数据文档。
   
  ![在应用端配置单一登录](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_002.png)   
  * 验证 **entityID** 是否与步骤&3; 中的“标识符”匹配。
  * 验证 **AssertionConsumerService** 位置 URL 是否与步骤&3; 中的“回复 URL”匹配。
     
    ![在应用端配置单一登录](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_007.png)
9. 单击“添加标识提供程序”按钮以添加新的联合身份提供程序。
   
  ![在应用端配置单一登录](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_003.png)
10. 单击“浏览...”以选择在步骤 4 中下载的元数据文件，然后单击“添加标识提供程序”以上载所下载的文件。 
    
  ![在应用端配置单一登录](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_004.png)
11. 在“编辑联合身份提供程序设置...”页上，对于“启用”选择“是”，然后单击“保存”。
    
   ![在应用端配置单一登录](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_005.png)
12. 在“编辑联合身份提供程序用户设置”页上，执行以下操作，然后单击“保存”。0   
  1. 对于“登录名”，选择“SAML NameID”。 
  2. 对于“全名”，选择“其他”，并在“属性名称”文本框中输入以下值：http://schemas.microsoft.com/identity/claims/displayname。 
  3. 对于“电子邮件”，选择“其他”，并在“属性名称”文本框中输入以下值：http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress。 
  4. 对于“登录时自动创建帐户”，选择“是”。 
  5. 单击“保存”按钮。
 
   ![在应用端配置单一登录](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_006.png)
13. 在经典门户中，选择“单一登录配置确认”，然后单击“下一步”。
    
    ![Azure AD 单一登录][10]
14. 在“单一登录确认”页上，单击“完成”。  
    
   ![Azure AD 单一登录][11]

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在经典门户中创建名为“Britta Simon”的测试用户。

![创建 Azure AD 用户][20]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_09.png)
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要显示用户列表，请在顶部菜单中，单击“用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_03.png)
4. 若要打开“添加用户”对话框，请在底部工具栏中单击“添加用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_04.png)
5. 在“告诉我们有关此用户的信息”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_05.png)
  1. 在“用户类型”中，选择“你的组织中的新用户”。
  2. 在“用户名”文本框中，键入“BrittaSimon”。
  3. 单击“资源组名称” 的 Azure 数据工厂。
6. 在“用户配置文件”对话框页上，执行以下步骤：
   
   ![创建 Azure AD 测试用户](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_06.png)
  1. 在“名字”文本框中，键入“Britta”。  
  2. 在“姓氏”文本框中，键入“Simon”。
  3. 在“显示名称”文本框中，键入“Britta Simon”。
  4. 在“角色”列表中，选择“用户”。
  5. 单击“资源组名称” 的 Azure 数据工厂。
7. 在“获取临时密码”对话框页上，单击“创建”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_07.png)
8. 在“获取临时密码”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-moveittransfer-tutorial/create_aaduser_08.png)
  1. 写下“新密码”的值。
  2. 单击“完成”。   

### <a name="create-a-moveit-transfer-test-user"></a>创建 MOVEit Transfer 测试用户
本部分的目的是在 MOVEit Transfer 中创建名为 Britta Simon 的用户。 MOVEit Transfer 支持在默认情况下启用的实时预配。

本部分不存在任何操作项。 如果尚不存在用户，则在尝试访问 MOVEit Transfer 期间会创建一个新用户。

>[!NOTE]
>如果需要手动创建用户，则需联系 MOVEit Transfer 支持团队。
> 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户
本部分旨在通过授予 Britta Simon 访问 MOVEit Transfer 的权限，允许她使用 Azure SSO。

![分配用户][200]

**若要将 Britta Simon 分配到 MOVEit Transfer，请执行以下步骤：**

1. 在经典门户中，若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![分配用户][201]
2. 在应用程序列表中，选择“MOVEit Transfer”。
   
    ![配置单一登录](./media/active-directory-saas-moveittransfer-tutorial/tutorial_moveittransfer_50.png)
3. 在顶部菜单中，单击“用户”。
   
    ![分配用户][203]
4. 在“用户”列表中，选择“Britta Simon”。
5. 在底部工具栏中，单击“分配”。
   
    ![分配用户][205]

### <a name="test-single-sign-on"></a>测试单一登录
本部分旨在使用“访问面板”测试 Azure AD SSO 配置。

当在访问面板中单击 MOVEit Transfer 磁贴时，应当会自动登录到 MOVEit Transfer 应用程序。

## <a name="additional-resources"></a>其他资源
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-moveittransfer-tutorial/tutorial_general_205.png

