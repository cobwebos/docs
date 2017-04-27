---
title: "教程：Azure Active Directory 与 PerformanceCentre 集成 | Microsoft 文档"
description: "了解如何在 Azure Active Directory 和 PerformanceCentre 之间配置单一登录。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 65288c32-f7e6-4eb3-a6dc-523c3d748d1c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 3b1a347322e2307aadbd287cb2235e39e11f1e79
ms.lasthandoff: 04/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-performancecentre"></a>教程：Azure Active Directory 与 PerformanceCentre 的集成
本教程的目的是介绍如何将 PerformanceCentre 与 Azure Active Directory (Azure AD) 集成。  

将 PerformanceCentre 与 Azure AD 集成具有以下优势： 

* 可在 Azure AD 中控制谁有权访问 PerformanceCentre 
* 可以让用户通过其 Azure AD 帐户自动登录到 PerformanceCentre 单一登录 (SSO)
* 可以在一个中心位置（即 Azure Active Directory 经典门户）管理帐户

如果想要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [What is application access and single sign-on with Azure Active Directory](active-directory-appssoaccess-whatis.md)（什么是使用 Azure Active Directory 的应用程序访问和单一登录）。

## <a name="prerequisites"></a>先决条件
若要配置 Azure AD 与 PerformanceCentre 的集成，需备齐以下项目：

* Azure AD 订阅
* 启用了 PerformanceCentre 单一登录 (SSO) 的订阅

>[!NOTE]
>不建议使用生产环境测试本教程中的步骤。
> 
> 

测试本教程中的步骤应遵循以下建议：

* 不应使用生产环境，除非有此必要。
* 如果没有 Azure AD 试用环境，可以获取[一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。 

## <a name="scenario-description"></a>方案描述
本教程旨在介绍如何在测试环境中测试 Azure AD SSO。  

本教程中概述的方案包括以下主要构建基块：

*  从库中添加 PerformanceCentre 
*  配置和测试 Azure AD SSO

## <a name="add-performancecentre-from-the-gallery"></a>从库中添加 PerformanceCentre
若要通过配置将 PerformanceCentre 集成到 Azure AD 中，需从库将 PerformanceCentre 添加到托管式 SaaS 应用的列表中。

**若要从库添加 PerformanceCentre，请执行以下步骤：**

1. 在 **Azure 经典门户**的左侧导航窗格上，单击“Active Directory”。 
   
    ![Active Directory][1]
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序][2]
4. 在页面底部单击“添加”。
   
    ![应用程序][3]
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![应用程序][4]
6. 在搜索框中，键入“PerformanceCentre”。
   
    ![应用程序][5]
7. 在结果窗格中，选择“PerformanceCentre”，然后单击“完成”添加该应用程序。
   
    ![应用程序][500]

## <a name="configure-and-test-azure-ad-sso"></a>配置和测试 Azure AD SSO
本部分的目的是介绍如何根据名为“Britta Simon”的测试用户的情况，配置和测试 PerformanceCentre 的 Azure AD SSO。

若要运行 SSO，Azure AD 需要知道与 Azure AD 用户相对应的 PerformanceCentre 中的用户。 换句话说，需要建立 Azure AD 用户与 PerformanceCentre 中相关用户之间的关联关系。  

将 Azure AD 中“用户名”的值指定为 PerformanceCentre 中“用户名”的值，即可建立此关联关系。

**若要配置和测试 PerformanceCentre 的 Azure AD 单一登录，需要完成以下构建基块：**

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-single-sign-on)** - 让用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 PerformanceCentre 测试用户](#creating-a-halogen-software-test-user)** - 目的是在 PerformanceCentre 中有一个与 Azure AD 中的 Britta Simon 相对应的关联用户。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录
本部分旨在介绍如何在 Azure AD 经典门户中启用 Azure AD SSO 并在 PerformanceCentre 应用程序中配置 SSO。

**若要通过 PerformanceCentre 配置 Azure AD SSO，请执行以下步骤：**

1. 在 Azure AD 经典门户中的“PerformanceCentre”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
    ![配置单一登录][6] 
2. 在“你希望用户如何登录 PerformanceCentre”页上，选择“Azure AD 单一登录”，然后单击“下一步”。
   
    ![Azure AD 单一登录][7] 
3. 在“配置应用设置”对话框页上，执行以下步骤：
   
    ![Azure AD 单一登录][8] 
   
   1. 在“登录 URL”文本框中，使用以下模式键入用户用于登录 PerformanceCentre 站点的 URL（例如：*http://companyname.performancecentre.com/saml/SSO*）。
   2. 单击“下一步”。
4. 在“在 PerformanceCentre 中配置单一登录”页中，执行以下步骤：
   
    ![Azure AD 单一登录][9] 
   
  * 单击“下载元数据”，然后在计算机上保存该文件。
5. 以管理员身份登录 **PerformanceCentre** 公司站点。
6. 在左侧选项卡中，单击“配置”。
   
    ![Azure AD 单一登录][10]
7. 在左侧选项卡中，单击“杂项”，然后单击“单一登录”。
   
    ![Azure AD 单一登录][11]
8. 选择“SAML”作为“协议”。
   
    ![Azure AD 单一登录][12]
9. 在记事本中打开下载的元数据文件，将其内容复制并粘贴到“标识提供者元数据”文本框中，然后单击“保存”。
   
    ![Azure AD 单一登录][13]
10. 确保“实体基 URL”和“实体 ID URL”的值正确。
    
     ![Azure AD 单一登录][14]
11. 在 Azure AD 经典门户中，选择“单一登录配置确认”，然后单击“下一步”。 
    
     ![Azure AD 单一登录][15]
12. 在“单一登录确认”页上，单击“完成”。  
    
     ![Azure AD 单一登录][16]

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 经典门户中创建名为 Britta Simon 的测试用户。  

![创建 Azure AD 用户][20]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_09.png)  
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要显示用户列表，请在顶部菜单中，单击“用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_03.png) 
4. 若要打开“添加用户”对话框，请在底部工具栏中单击“添加用户”。 
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_04.png) 
5. 在“告诉我们有关此用户的信息”对话框页上，执行以下步骤： 
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_05.png)  
   
   1. 在“用户类型”中，选择“你的组织中的新用户”。
   2. 在“用户名”文本框中，键入“BrittaSimon”。
   3. 单击“资源组名称” 的 Azure 数据工厂。
   
6. 在“用户配置文件”对话框页上，执行以下步骤： 
   
   ![创建 Azure AD 测试用户](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_06.png) 
   
   1. 在“名字”文本框中，键入“Britta”。  
   2. 在“姓氏”文本框中，键入“Simon”。
   3. 在“显示名称”文本框中，键入“Britta Simon”。
   4. 在“角色”列表中，选择“用户”。
   5. 单击“资源组名称” 的 Azure 数据工厂。
   
7. 在“获取临时密码”对话框页上，单击“创建”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_07.png) 
8. 在“获取临时密码”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_08.png) 
   
   1. 写下“新密码”的值。
   2. 单击“完成”。   

### <a name="create-a-performancecentre-test-user"></a>创建 PerformanceCentre 测试用户
本部分的目的是在 PerformanceCentre 中创建名为“Britta Simon”的用户。

**若要在 PerformanceCentre 中创建名为“Britta Simon”的用户，请执行以下步骤：**

1. 以管理员身份登录 PerformanceCentre 公司站点。
2. 在左侧菜单中，单击“相互关联”，然后单击“创建参与者”。
   
    ![创建用户][400]
3. 在“相互关联 - 创建参与者”对话框中，执行以下步骤：
   
    ![创建用户][401]
   
   1. 在相关文本框中键入 Britta Simon 的所需属性。

    >[!IMPORTANT]
    >Britta 在 PerformanceCentre 中的“用户名”属性必须与 Azure AD 中的“用户名”相同。
    > 
    > 
 
   2. 对于“选择角色”选项，请选择“客户端管理员”。
   3. 单击“保存” 。   


### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户
本部分旨在通过授予 Britta Simon 访问 PerformanceCentre 的权限，允许她使用 Azure SSO。

![分配用户][200] 

**若要将 Britta Simon 分配到 PerformanceCentre，请执行以下步骤：**

1. 在 Azure 经典门户中，若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![分配用户][201]
2. 在应用程序列表中，选择“PerformanceCentre”。
   
    ![分配用户][202]
3. 在顶部菜单中，单击“用户”。
   
    ![分配用户][203]
4. 在“用户”列表中，选择“Britta Simon”。
5. 在底部工具栏中，单击“分配”。
   
    ![分配用户][205]

### <a name="test-single-sign-on"></a>测试单一登录
本部分旨在使用“访问面板”测试 Azure AD SSO 配置。  

单击访问面板中的“PerformanceCentre”磁贴时，用户就会自动登录到 PerformanceCentre 应用程序。

## <a name="additional-resources"></a>其他资源
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_01.png
[500]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_02.png

[6]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_03.png
[8]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_04.png
[9]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_05.png
[10]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_06.png
[11]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_07.png
[12]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_08.png
[13]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_09.png
[14]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_10.png
[15]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_06.png
[16]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_07.png


[20]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_50.png
[203]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_11.png
[401]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_12.png
[402]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_402.png




