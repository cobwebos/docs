---
title: "教程：Azure Active Directory 与 Convercent 的集成 | Microsoft 文档"
description: "了解如何在 Azure Active Directory 和 Convercent 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: f9c9d290-0e13-490b-b559-0be772d6a690
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/28/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 6bbfc7e22e5cb70ed1466bda14831412aead76a6
ms.openlocfilehash: bacabf7fe51e3c417a93a54f043c14aa5d65b31d
ms.lasthandoff: 01/05/2017


---
# <a name="tutorial-azure-active-directory-integration-with-convercent"></a>教程：Azure Active Directory 与 Convercent 的集成

本教程介绍了如何将 Convercent 与 Azure Active Directory (Azure AD) 进行集成。

将 Convercent 与 Azure AD 集成可提供以下优势：

- 可以在 Azure AD 中控制谁有权访问 Convercent
- 可以让用户使用其 Azure AD 帐户自动登录到 Convercent（单一登录）
- 可在一个中心位置（即 Azure 新门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Convercent 的集成，需要具有以下项：

- Azure AD 订阅
- 启用了 Convercent 单一登录的订阅


> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。


测试本教程中的步骤应遵循以下建议：

- 不应使用生产环境，除非有此必要。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。


## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Convercent
2. 配置和测试 Azure AD 单一登录


## <a name="adding-convercent-from-the-gallery"></a>从库中添加 Convercent
若要配置 Convercent 与 Azure AD 的集成，需要从库中将 Convercent 添加到托管 SaaS 应用列表。

**若要从库中添加 Convercent，请执行以下步骤：**

1. 在 Azure 管理门户的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 单击对话框顶部的“添加”按钮。

    ![应用程序][3]

4. 在搜索框中，键入“Convercent”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_001.png)

5. 在结果窗格中，选择“Convercent”，然后单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，将基于名为“Britta Simon”的测试用户配置并测试 Convercent 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Convercent 用户。 换句话说，需要在 Azure AD 用户与 Convercent 中的相关用户之间建立链接关系。

可以通过将 Azure AD 中“用户名”的值分配为 Convercent 中“用户名”的值来建立此链接关系。

若要配置并测试 Convercent 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Convercent 测试用户](#creating-a-works-mobile-test-user)** - 在 Convercent 中创建 Britta Simon 的对应用户，将其链接到其 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 新门户中启用 Azure AD 单一登录并在 Convercent 应用程序中配置单一登录。

**若要配置 Convercent 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 新门户的“Convercent”应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框页中，选择“基于 SAML 的登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_01.png)

3. 在“Convercent 域和 URL”部分，如果要在“IDP 发起的模式”下配置应用程序，请执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_02.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“标识符”文本框中，键入 `https://sts.convercent.com/`

    b.保留“数据库类型”设置，即设置为“共享”。 单击“显示高级 URL 设置”

    c. 在“中继状态”文本框中，键入：`https://app.convercent.com/`
    
4. 在“Convercent 域和 URL”部分，如果要在“SP 发起的模式”下配置应用程序，请执行以下步骤：
    
    ![配置单一登录](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_03.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“登录 URL”文本框中，键入 `https://app.convercent.com/`

    > [!NOTE] 
    > 在这里，建议你使用指定的唯一标识符。 若要获取此值，请与 [Convercent 支持团队](mailTo:support@convercent.com)联系。

5. 在“Convercent 配置”部分，单击“配置 Convercent”打开“配置单一登录”对话框。 然后单击“SAML XML 元数据”，在计算机上保存该元数据文件。

    ![配置单一登录](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_04.png) 

    ![配置单一登录](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_05.png)

6. 若要为应用程序配置 SSO，请联系 [Convercent 支持团队](mailTo:support@convercent.com)，并向他们提供下载的**元数据**。

7. 在 Azure 新门户中，单击“保存”按钮。  
  

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在新门户中创建名为“Britta Simon”的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 管理门户的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-convercent-tutorial/create_aaduser_01.png) 

2. 转到“用户和组”，单击“所有用户”显示用户列表。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-convercent-tutorial/create_aaduser_02.png) 

3. 在对话框顶部单击“添加”，打开“用户”对话框。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-convercent-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-convercent-tutorial/create_aaduser_04.png) 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名称”文本框中，键入 **BrittaSimon**。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d.单击“下一步”。 单击“创建” 。 



### <a name="creating-a-convercent-test-user"></a>创建 Convercent 测试用户

在本部分中，将在 Convercent 中创建一个名为 Britta Simon 的用户。 请与 [Convercent 支持团队](emailto:support@convercent.com)协作，将用户添加到 Convercent 平台中。


### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，将通过向 Britta Simon 授予对 Convercent 的访问权限使她能够使用 Azure 单一登录。

![分配用户][200] 

**若要将 Britta Simon 分配到 Convercent，请执行以下步骤：**

1. 在 Azure 管理门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，然后单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Convercent”。

    ![配置单一登录](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_50.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    


### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

当在访问面板中单击 Convercent 磁贴时，应当会自动登录到 Convercent 应用程序。


## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_203.png
