---
title: "教程：Azure Active Directory 与 ClickTime 的集成 | Microsoft 文档"
description: "了解如何将 ClickTime 与 Azure Active Directory 配合使用来启用单一登录、自动化预配及更多功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: d437b5ab-4d71-4c13-96d0-79018cebbbd4
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 0837cb33bf438fb7fd9665d21d411f0170cdd393
ms.openlocfilehash: d5171cdc9048837385bfb99d553e496a9f56846e
ms.lasthandoff: 02/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-clicktime"></a>教程：Azure Active Directory 与 ClickTime 的集成
本教程介绍了如何将 ClickTime 与 Azure Active Directory (Azure AD) 进行集成。

将 ClickTime 与 Azure AD 集成可提供以下优势：

* 可以在 Azure AD 中控制谁有权访问 ClickTime
* 可以让用户使用其 Azure AD 帐户自动登录到 ClickTime（单一登录）
* 可在一个中心位置（即 Azure 经典门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件
若要配置 Azure AD 与 ClickTime 的集成，需要具有以下项：

* Azure AD 订阅
* 启用了 ClickTime 单一登录的订阅

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

1. 从库中添加 ClickTime
2. 配置和测试 Azure AD 单一登录

## <a name="adding-clicktime-from-the-gallery"></a>从库中添加 ClickTime
本部分的目的是概述如何为 ClickTime 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-clicktime-perform-the-following-steps"></a>若要为 ClickTime 启用应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-clicktime-tutorial/tic700993.png "Active Directory")
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-clicktime-tutorial/tic700994.png "应用程序")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-clicktime-tutorial/tic749321.png "添加应用程序")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库添加应用程序](./media/active-directory-saas-clicktime-tutorial/tic749322.png "从库添加应用程序")
6. 在**搜索框**中，键入“ClickTime”。
   
   ![应用程序库](./media/active-directory-saas-clicktime-tutorial/tic777275.png "应用程序库")
7. 在结果窗格中，选择“ClickTime”，然后单击“完成”以添加该应用程序。
   
   ![ClickTime](./media/active-directory-saas-clicktime-tutorial/tic777276.png "ClickTime")

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，将基于名为“Britta Simon”的测试用户配置并测试 ClickTime 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 ClickTime 用户。 换句话说，需要在 Azure AD 用户与 ClickTime 中的相关用户之间建立链接关系。

可以通过将 Azure AD 中“用户名”的值分配为 ClickTime 中“用户名”的值来建立此链接关系。

若要配置并测试 ClickTime 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 ClickTime 测试用户](#creating-a-clicktime-test-user)** - 在 ClickTime 中创建 Britta Simon 的对应用户，将其链接到其 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录
本部分的目的是概述如何让用户能够使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 ClickTime 证明自己的身份。  

> [!IMPORTANT]
> 为了能够在你的 ClickTime 租户上配置单一登录，请首先联系 ClickTime 技术支持以启用此功能。
> 
> 

**若要配置 ClickTime 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 经典门户中，在 **ClickTime** 应用程序集成页上，单击“配置单一登录”，以打开“配置单一登录”对话框。
   
   ![启用单一登录](./media/active-directory-saas-clicktime-tutorial/tic777277.png "启用单一登录")
2. 在“你希望用户如何登录到 ClickTime”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-clicktime-tutorial/tic777278.png "配置单一登录")
3. 在“配置应用设置”对话框页上，执行以下步骤：
   
    ![配置单一登录](./media/active-directory-saas-clicktime-tutorial/tic777286.png) 
   
    a. 在“标识符 URL”文本框中，使用以下模式键入 URL：**https://app.clicktime.com/sp/**
   
    b. 在“回复 URL”文本框中，使用以下模式键入 URL：**https://app.clicktime.com/Login/**
   
    c. 单击“下一步”
4. 在“在 ClickTime 处配置单一登录”页上，若要下载证书，请单击“下载证书”，然后将证书文件保存在计算机上。
   
   ![配置单一登录](./media/active-directory-saas-clicktime-tutorial/tic777279.png "配置单一登录")
5. 在另一个 Web 浏览器窗口中，以管理员身份登录到你的 ClickTime 公司站点。
6. 在顶部工具栏中，单击“首选项”，然后单击“安全设置”。
7. 在“单一登录首选项”配置部分中，执行以下步骤：
   
   ![安全设置](./media/active-directory-saas-clicktime-tutorial/tic777280.png "安全设置")
   
   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。  选择“允许”通过“Azure AD”使用单一登录 (SSO) 进行登录。
   
   b.  在 Azure 经典门户中，在“在 ClickTime 处配置单一登录”对话框页上，复制“单一登录服务 URL”值，然后将其粘贴到“标识提供者终结点”文本框中。
   
   c.  在**记事本**中打开 base-64 编码的证书，复制内容，然后将其粘贴到“X.509 证书”文本框中。
   
   d.  单击“保存”。
8. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-clicktime-tutorial/tic777281.png "配置单一登录")

## <a name="configuring-user-provisioning"></a>配置用户设置
为了使 Azure AD 用户能够登录到 ClickTime，必须将其预配到 ClickTime 中。  
对于 ClickTime，需要手动执行预配。

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>若要预配用户帐户，请执行以下步骤：
1. 登录到你的 **ClickTime** 租户。
2. 在顶部工具栏中，单击“公司”，然后单击“人员”。
   
   ![人员](./media/active-directory-saas-clicktime-tutorial/tic777282.png "人员")
3. 单击“添加用户”。
   
   ![添加人员](./media/active-directory-saas-clicktime-tutorial/tic777283.png "添加人员")
4. 在“新建人员”部分中，执行以下步骤：
   
   ![人员](./media/active-directory-saas-clicktime-tutorial/tic777284.png "人员")
   
   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。  在“电子邮件地址”文本框中，键入你的 Azure AD 帐户的电子邮件地址。
   
   b.  在“全名”文本框中，键入你的 Azure AD 帐户的名称。  
   
   > [!NOTE]
   > 如果需要，可以设置新人员对象的其他属性。
   > 
   > 
   
   c.  单击“保存”。

> [!NOTE]
> 可以使用 ClickTime 提供的任何其他 ClickTime 用户帐户创建工具或 API 来预配 Azure AD 用户帐户。
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户
在本部分中，将通过向 Britta Simon 授予对 ClickTime 的访问权限使她能够使用 Azure 单一登录。

![分配用户][200]

若要测试配置，需要向希望能够通过应用程序访问配置的 Azure AD 用户分配访问权限，从而向他们授予该权限。

**若要将 Britta Simon 分配到 ClickTime，请执行以下步骤：**

1. 在经典门户中，若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![分配用户][201] 
2. 在应用程序列表中，选择“ClickTime”。
   
    ![配置单一登录](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_50.png) 
3. 在顶部菜单中，单击“用户”。
   
    ![分配用户][203]
4. 在“用户”列表中，选择“Britta Simon”。
5. 在底部工具栏中，单击“分配”。
   
    ![分配用户][205]

## <a name="testing-single-sign-on"></a>测试单一登录
在本部分中，使用访问面板测试 Azure AD 单一登录配置。

当在访问面板中单击 ClickTime 磁贴时，应当会自动登录到 ClickTime 应用程序。

## <a name="additional-resources"></a>其他资源
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[200]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_203.png
[205]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_205.png

