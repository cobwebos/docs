---
title: "教程：Azure Active Directory 与 HackerOne 集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 HackerOne 之间配置单一登录。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 229d1efb-b6a5-4df8-9839-5d551487db4e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2fa331e94425673481689aefeb5393618f2471b9
ms.openlocfilehash: ced5f0fc2d9afa22f2d3ef3a81bb07482f69ec0e
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-hackerone"></a>教程：Azure Active Directory 与 HackerOne 集成
本教程将 HackerOne 与 Azure Active Directory (Azure AD) 集成。

将 HackerOne 与 Azure AD 集成具有以下优势：

* 可在 Azure AD 中控制谁有权访问 HackerOne
* 可以让用户通过其 Azure AD 帐户自动登录到 HackerOne 单一登录 (SSO)
* 可以在一个中心位置（即 Azure 经典门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件
若要配置 Azure AD 与 HackerOne 的集成，需要以下项目：

* Azure 订阅
* 启用了 HackerOne SSO 的订阅

>[!NOTE]
>不建议使用生产环境测试本教程中的步骤。 
> 

测试本教程中的步骤应遵循以下建议：

* 不应使用生产环境，除非有此必要。
* 如果没有 Azure AD 试用环境，可以获取[一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
本教程将在测试环境中配置和测试 Azure AD 单一登录。  

本教程中概述的方案包括两个主要构建基块：

*  从库中添加 HackerOne
*  配置和测试 Azure AD SSO

## <a name="add-hackerone-from-the-gallery"></a>从库中添加 HackerOne
若要将 HackerOne 集成到 Azure AD 中，需要从库中将 HackerOne 添加到托管 SaaS 应用列表。

**若要从库中添加 HackerOne，请执行以下步骤：**

1. 在 **Azure 经典门户**的左侧导航窗格上，单击“Active Directory”。 
   
    ![Active Directory][1]
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序][2]
4. 在页面底部单击“添加”。
   
    ![应用程序][3]
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。

  ![应用程序][4]
6. 在搜索框中，键入“HackerOne”。

  ![创建 Azure AD 测试用户](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_01.png)

7. 在“结果”窗格中，选择“HackerOne”，然后单击“完成”，添加该应用程序。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
接下来，基于一个名为“Britta Simon”的测试用户使用 HackerOne 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 HackerOne 用户。 换句话说，需要建立 Azure AD 用户与 HackerOne 中相关用户之间的关联关系。  
通过将 Azure AD 中“用户名”的值分配为 HackerOne 中“用户名”的值来建立此关联关系。

若要配置和测试 HackerOne 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-single-sign-on)** - 让用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 HackerOne 测试用户](#creating-a-hackerone-test-user)** - 在 HackerOne 中有一个已链接到 Azure AD 中 Britta Simon 表示形式的对应项。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录
接下来，在经典门户中启用 Azure AD 单一登录，并在 HackerOne 应用程序中配置单一登录。

在此过程中，需要创建 base-64 编码的证书文件。 如果不熟悉此过程，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)。

**若要配置 HackerOne 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 经典门户中的“HackerOne”应用程序集成页上，单击“配置单一登录”，以打开“配置单一登录”对话框。
   
    ![配置单一登录][6] 
2. 在“你希望用户如何登录 HackerOne”页上，选择“Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_03.png) 
3. 在“配置应用设置”对话框页上，执行以下步骤，然后单击“下一步”：
   
    ![配置单一登录](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_04.png) 
    1. 在“登录 URL”文本框中，使用以下模式键入用户用于登录 HackerOne 应用程序的 URL：**“https://hackerone.com/\<公司名称\>/authentication”**。 
    2. 请通过 [support@hackerone.com](mailto:support@hackerone.com) 联系 HackerOne 支持团队以获取租户 URL（如果你不知道）。
    3. 在“标识符”文本框中，键入租户 URL。 
    4. 单击“资源组名称” 的 Azure 数据工厂。

4. 在“在 HackerOne 中配置单一登录”页中，执行以下步骤，然后单击“下一步”：
   
    ![配置单一登录](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_05.png) 
    1. 单击“下载证书”，然后将文件保存在计算机上。
    2. 单击“资源组名称” 的 Azure 数据工厂。
5. 以管理员身份登录到 HackerOne 租户。
6. 在顶部菜单中，单击“设置”。
   
    ![配置单一登录](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_001.png) 
7. 导航到“身份验证”并单击“添加 SAML 设置”。
   
    ![配置单一登录](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_003.png) 
8. 在“SAML 设置”对话框中，执行以下步骤：
   
    ![配置单一登录](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_004.png) 
    1. 在“电子邮件域”文本框中，键入已注册域。
    2. 在 Azure 经典门户中，复制“单一登录服务 URL”，然后将其粘贴到“单一登录 URL”文本框中。
    3. 基于下载的证书创建一个 **base-64 编码**的文件。  
    
       >[!TIP] 
       >有关详细信息，请参阅 [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o)（如何将二进制证书转换为文本文件）。
       >
    4. 在记事本中打开 base-64 编码的证书，将其内容复制到剪贴板，然后将其粘贴到“X509 证书”文本框中。
    5. 单击“保存” 。
9. 在“身份验证设置”对话框中，执行以下步骤：
   
    ![配置单一登录](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_005.png) 
    1. 单击“运行测试”。
    2. 如果“状态”字段的值为“上次测试状态: 已创建”，则请通过 [support@hackerone.com](mailto:support@hackerone.com) 联系 HackerOne 支持团队，请求其查看你的配置。
10. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“下一步”。
   
    ![Azure AD 单一登录][10]
11. 在“单一登录确认”页上，单击“完成”。  
   
    ![Azure AD 单一登录][11]

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
接下来，在经典门户中创建名为“Britta Simon”的测试用户。  

![创建 Azure AD 用户][20]

**若要在 Azure AD 中创建 SECURE DELIVER 测试用户，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-hackerone-tutorial/create_aaduser_09.png) 
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要显示用户列表，请在顶部菜单中，单击“用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-hackerone-tutorial/create_aaduser_03.png) 
4. 若要打开“添加用户”对话框，请在底部工具栏中单击“添加用户”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-hackerone-tutorial/create_aaduser_04.png) 
5. 在“告诉我们有关此用户的信息”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-hackerone-tutorial/create_aaduser_05.png) 
    1. 在“用户类型”中，选择“你的组织中的新用户”。
    2. 在“用户名”文本框中，键入“BrittaSimon”。
    3. 单击“资源组名称” 的 Azure 数据工厂。
6. 在“用户配置文件”对话框页上，执行以下步骤：
   
   ![创建 Azure AD 测试用户](./media/active-directory-saas-hackerone-tutorial/create_aaduser_06.png) 
   1. 在“名字”文本框中，键入“Britta”。  
   2. 在“姓氏”文本框中，键入“Simon”。
   3. 在“显示名称”文本框中，键入“Britta Simon”。
   4. 在“角色”列表中，选择“用户”。
   5. 单击“资源组名称” 的 Azure 数据工厂。
7. 在“获取临时密码”对话框页上，单击“创建”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-hackerone-tutorial/create_aaduser_07.png) 
8. 在“获取临时密码”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-hackerone-tutorial/create_aaduser_08.png) 
    1. 写下“新密码”的值。
    2. 单击“完成”。   

### <a name="create-a-hackerone-test-user"></a>创建 HackerOne 测试用户
接下来，在 HackerOne 中创建名为“Britta Simon”的用户。 HackerOne 支持在默认情况下启用的实时预配。

本部分不存在任何操作项。 访问 HackerOne 时，如果还没有新用户，则会创建新用户。 [配置 Azure AD 单一登录](#configuring-azure-ad-single-single-sign-on)。

>[!NOTE]
>如果需要手动创建用户，则需联系 HackerOne 支持团队。 
> 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户
接下来，通过授予 Britta Simon 访问 HackerOne 的权限，允许她使用 Azure 单一登录。

![分配用户][200] 

**若要将 Britta Simon 分配到 HackerOne，请执行以下步骤：**

1. 在 Azure 经典门户中，若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![分配用户][201] 
2. 在应用程序列表中，选择“HackerOne”。
   
    ![配置单一登录](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_50.png) 
3. 在顶部菜单中，单击“用户”。
   
    ![分配用户][203] 
4. 在“用户”列表中，选择“Britta Simon”。
5. 在底部工具栏中，单击“分配”。
   
    ![分配用户][205]

### <a name="test-single-sign-on"></a>测试单一登录
最后，使用访问面板测试 Azure AD 单一登录配置。  

当在访问面板中单击“HackerOne”磁贴时，应该会自动登录“HackerOne”应用程序。

## <a name="additional-resources"></a>其他资源
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_205.png

