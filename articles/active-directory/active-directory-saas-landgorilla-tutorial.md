---
title: "教程：Azure Active Directory 与 Land Gorilla 客户端的集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 Land Gorilla 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: jeedes
ms.openlocfilehash: 30c34deff9e8ec4a7df90a947d3950e4f84c9c43
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-land-gorilla-client"></a>教程：Azure Active Directory 与 Land Gorilla 客户端的集成

在本教程中，了解如何将 Land Gorilla 客户端与 Azure Active Directory (Azure AD) 集成。

将 Land Gorilla 客户端与 Azure AD 集成具有以下优势：

- 可在 Azure AD 中控制谁有权访问 Land Gorilla 客户端
- 可以让用户通过其 Azure AD 帐户自动登录到 Land Gorilla 客户端（单一登录）
- 可在一个中心位置（即 Azure 管理门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。


## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Land Gorilla 客户端的集成，需要以下项：

- Azure AD 订阅
- 启用的订阅上的 Land Gorilla 客户端单一登录


> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。


测试本教程中的步骤应遵循以下建议：

- 不应使用生产环境，除非有此必要。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。


## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库添加 Land Gorilla 客户端
2. 配置和测试 Azure AD 单一登录


## <a name="adding-land-gorilla-client-from-the-gallery"></a>从库添加 Land Gorilla 客户端
要将 Land Gorilla 客户端集成配置到到 Azure AD 中，需从库中将 Land Gorilla 客户端添加到托管式 SaaS 应用的列表中。

**若要从库中添加 Land Gorilla 客户端，请执行以下步骤：**

1. 在 **[Azure 管理门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 单击对话框顶部的“添加”按钮。

    ![应用程序][3]

4. 在搜索框中，键入 **Land Gorilla 客户端**。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-landgorilla-tutorial/tutorial_landgorilla_search.png)

5. 在结果窗格中，选择“Land Gorilla 客户端”，并单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-landgorilla-tutorial/tutorial_landgorilla_addfromgallery.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，将基于名为“Britta Simon”的测试用户配置并测试 Land Gorilla 客户端的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Land Gorilla 客户端用户。 换句话说，需要在 Azure AD 用户与 Land Gorilla 客户端中相关用户之间建立链接关系。

可以通过将 Azure AD 中“用户名”的值分配为 Land Gorilla 客户端中“用户名”的值来建立此链接关系。

若要配置和测试 Land Gorilla 客户端的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用有限组测试 Azure AD 单一登录。
3. **[创建 Land Gorilla 测试用户](#creating-a-land-gorilla-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，会在 Azure 管理门户中启用 Azure AD 单一登录并在 Land Gorilla 客户端应用程序中配置单一登录。

**若要配置 Land Gorilla 客户端的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 管理门户的“Land Gorilla 客户端”应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-landgorilla-tutorial/tutorial_landgorilla_samlbase.png)

3. 在“Land Gorilla 客户端和 URL”部分中，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-landgorilla-tutorial/tutorial_landgorilla_url_02.png)

    a. 在“标识符”文本框中，使用下面的一种模式键入值： 
    
    `https://<customer domain>.landgorilla.com/` 
    
    `https://www.<customer domain>.landgorilla.com`

    b. 在“回复 URL”文本框中，使用下面的一种模式键入 URL：

    `https://<customer domain>.landgorilla.com/simplesaml/module.php/core/authenticate.php`

    `https://www.<customer domain>.landgorilla.com/simplesaml/module.php/core/authenticate.php`

    `https://<customer domain>.landgorilla.com/simplesaml/module.php/saml/sp/saml2-acs.php/default-sp`
    
    `https://www.<customer domain>.landgorilla.com/simplesaml/module.php/saml/sp/saml2-acs.php/default-sp`

    > [!NOTE] 
    > 请注意，这些不是实际值。 必须使用实际标识符和回复 URL 更新这些值。 此处我们建议在“标识符”中使用字符串的唯一值。 联系 [Land Gorilla 客户端团队](https://www.landgorilla.com/support/)获取这些值。 

4. 在“SAML 签名证书”部分中，单击“元数据 XML”，并在计算机上保存 XML 文件。

    ![配置单一登录](./media/active-directory-saas-landgorilla-tutorial/tutorial_landgorilla_certificate.png) 

5. 单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-landgorilla-tutorial/tutorial_general_400.png) 

6. 若要在 Land Gorilla 端完成应用程序的 SSO 配置，请联系 [Land Gorilla 客户端支持团队](https://www.landgorilla.com/support/)并向他们提供下载的**元数据 XML** 文件。


### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 管理门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 管理门户的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-landgorilla-tutorial/create_aaduser_01.png) 

2. 转到“用户和组”，单击“所有用户”显示用户列表。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-landgorilla-tutorial/create_aaduser_02.png) 

3. 在对话框顶部单击“添加”，打开“用户”对话框。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-landgorilla-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-landgorilla-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d.单击“下一步”。 单击“创建”。 

### <a name="creating-a-land-gorilla-test-user"></a>创建 Land Gorilla 测试用户

请与 [Land Gorilla 支持团队](https://www.landgorilla.com/support/)协作，将用户添加到 Land Gorilla 平台中。
    
### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，将通过向 Britta Simon 授予对 Land Gorilla 客户端的访问权限使她能够使用 Azure 单一登录。

![分配用户][200] 

**要将 Britta Simon 分配到 Land Gorilla 客户端，请执行以下步骤：**

1. 在 Azure 管理门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Land Gorilla 客户端”。

    ![配置单一登录](./media/active-directory-saas-landgorilla-tutorial/tutorial_landgorilla_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    


### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

当在访问面板中单击 Land Gorilla 客户端磁贴时，应当会自动登录到 Land Gorilla 客户端应用程序。


## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-landgorilla-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-landgorilla-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-landgorilla-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-landgorilla-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-landgorilla-tutorial/tutorial_general_100.png
[200]: ./media/active-directory-saas-landgorilla-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-landgorilla-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-landgorilla-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-landgorilla-tutorial/tutorial_general_203.png
