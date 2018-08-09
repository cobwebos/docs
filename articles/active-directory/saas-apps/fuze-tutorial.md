---
title: 教程：Azure Active Directory 与 Fuze 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Fuze 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 9780b4bf-1fd1-48c1-9ceb-f750225ae08a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: jeedes
ms.openlocfilehash: 0b50e317fc8a94a5cd8ddd19dbe24d4f9125fedc
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39432987"
---
# <a name="tutorial-azure-active-directory-integration-with-fuze"></a>教程：Azure Active Directory 与 Fuze 集成

在本教程中，了解如何将 Fuze 与 Azure Active Directory (Azure AD) 集成。

将 Fuze与 Azure AD 集成具有以下优势：

- 可在 Azure AD 中控制谁有权访问 Fuze
- 可以让用户使用其 Azure AD 帐户自动登录到 Fuze（单一登录）
- 可在一个中心位置（即 Azure 管理门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Fuze 的集成，需要具有以下项：

- Azure AD 订阅
- 启用了 Fuze 单一登录的订阅


> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。


测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。


## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Fuze
1. 配置和测试 Azure AD 单一登录


## <a name="adding-fuze-from-the-gallery"></a>从库中添加 Fuze
要配置 Fuze 与 Azure AD 的集成，需要从库中将 Fuze 添加到托管 SaaS 应用列表。

**若要从库中添加 Fuze，请执行以下步骤：**

1. 在 **[Azure 管理门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

1. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
1. 单击对话框顶部的“添加”按钮。

    ![应用程序][3]

1. 在搜索框中，键入“Fuze”。

    ![创建 Azure AD 测试用户](./media/fuze-tutorial/tutorial_fuze_000.png)

1. 在结果面板中，选择“Fuze”，并单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/fuze-tutorial/tutorial_fuze_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，根据名为“Britta Simon”的测试用户的指示配置和测试 Fuze 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Fuze 用户。 换句话说，需要建立 Azure AD 用户与 Fuze 中相关用户之间的关联关系。

可以通过将 Azure AD 中“用户名”的值分配为 Fuze 中“用户名”的值来建立此关联关系。

若要配置和测试 Fuze 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
1. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
1. **[创建 Fuze 测试用户](#creating-a-fuze-test-user)** - 在 Fuze 中创建 Britta Simon 的对应用户，并将其链接到她的 Azure AD 表示形式。
1. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
1. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，会在 Azure 管理门户中启用 Azure AD 单一登录并在 Fuze 应用程序中配置单一登录。

**若要配置 Fuze 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 管理门户的“Fuze”应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

1. 在“单一登录”对话框中，选择“基于 SAML 的登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/fuze-tutorial/tutorial_fuze_01.png)

1. 在“Fuze 域和 URL”部分中，执行以下步骤：

    ![配置单一登录](./media/fuze-tutorial/tutorial_fuze_020.png)
    
    在“登录 URL”文本框中，键入登录 URL：`https://www.thinkingphones.com/jetspeed/portal/`

1.  单击“保存”按钮。

    ![配置单一登录](./media/fuze-tutorial/tutorial_general_400.png)

1. 在“SAML 签名证书”部分中，单击“元数据 XML”，然后将 xml 文件保存在计算机上。

    ![配置单一登录](./media/fuze-tutorial/tutorial_fuze_05.png) 

1. 要在“Fuze”端配置单一登录，需要将下载的**元数据 XML** 发送给 [Fuze 支持团队](https://www.fuze.com/support)。 他们会进行此设置，以便同时在两端正确设置 SAML SSO 连接。


### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 管理门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 管理门户的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/fuze-tutorial/create_aaduser_01.png) 

1. 转到“用户和组”，单击“所有用户”显示用户列表。
    
    ![创建 Azure AD 测试用户](./media/fuze-tutorial/create_aaduser_02.png) 

1. 在对话框顶部单击“添加”，打开“用户”对话框。
 
    ![创建 Azure AD 测试用户](./media/fuze-tutorial/create_aaduser_03.png) 

1. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/fuze-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b. 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d. 单击“创建”。 


### <a name="creating-a-fuze-test-user"></a>创建 Fuze 测试用户

Fuze 应用程序支持完整的实时用户预配，因此，在用户登录时会自动创建该用户。 有关任何其他说明，请与 Fuze [支持](https://www.fuze.com/support)联系。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Fuze 的权限，允许她使用 Azure 单一登录。

![分配用户][200] 

**要将 Britta Simon 分配到 Fuze，请执行以下步骤：**

1. 在 Azure 管理门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

1. 在应用程序列表中，选择“Fuze”。

    ![配置单一登录](./media/fuze-tutorial/tutorial_fuze_50.png) 

1. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

1. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

1. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

1. 在“用户和组”对话框中单击“选择”按钮。

1. 在“添加分配”对话框中单击“分配”按钮。
    

### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

当在访问面板中单击 Fuze 磁贴时，应该会自动登录 Fuze 应用程序。


## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/fuze-tutorial/tutorial_general_01.png
[2]: ./media/fuze-tutorial/tutorial_general_02.png
[3]: ./media/fuze-tutorial/tutorial_general_03.png
[4]: ./media/fuze-tutorial/tutorial_general_04.png

[100]: ./media/fuze-tutorial/tutorial_general_100.png

[200]: ./media/fuze-tutorial/tutorial_general_200.png
[201]: ./media/fuze-tutorial/tutorial_general_201.png
[202]: ./media/fuze-tutorial/tutorial_general_202.png
[203]: ./media/fuze-tutorial/tutorial_general_203.png