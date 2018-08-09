---
title: 教程：Azure Active Directory 与 Central Desktop 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Central Desktop 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b805d485-93db-49b4-807a-18d446c7090e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 82a6911c85dd1438aa8f60cb36194a2916bc91e7
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39429040"
---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>教程：Azure Active Directory 与 Central Desktop 集成

本教程介绍了如何将 Central Desktop 与 Azure Active Directory (Azure AD) 集成。

将 Central Desktop 与 Azure AD 集成可提供以下优势：

- 可以在 Azure AD 中控制谁有权访问 Central Desktop。
- 可以让用户使用其 Azure AD 帐户自动登录到 Central Desktop。
- 可在一个中心位置（即 Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅[什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Central Desktop 的集成，需要具有以下项：

- Azure AD 订阅
- 一个启用了单一登录的 Central Desktop 订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

若要测试本教程中的步骤，请遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果还没有 Azure AD 试用环境，请[获取一个月免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，会在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Central Desktop
1. 配置和测试 Azure AD 单一登录

## <a name="add-central-desktop-from-the-gallery"></a>从库中添加 Central Desktop
要配置 Central Desktop 与 Azure AD 的集成，需要从库中将 Central Desktop 添加到托管 SaaS 应用列表。

**若要从库中添加 Central Desktop，请执行以下步骤：**

1. 在 [Azure 门户](https://portal.azure.com)的左窗格中，选择“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

1. 转到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
1. 若要添加新应用程序，请选择对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

1. 在搜索框中，键入“Central Desktop”。 在结果窗格中，选择“Central Desktop”，然后选择“添加”按钮添加该应用程序。

    ![结果列表中的 Central Desktop](./media/central-desktop-tutorial/tutorial_centraldesktop_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

本部分基于名为“Britta Simon”的测试用户配置并测试 Central Desktop 的 Azure AD 单一登录。

要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Central Desktop 用户。 换句话说，需要在 Azure AD 用户与 Central Desktop 中的相关用户之间建立链接。

在 Central Desktop 中，赋予“用户名”与 Azure AD 中的“用户名”相同的值。 现在，已关联这两个用户。

要配置和测试 Central Desktop 的 Azure AD 单一登录，需要完成以下构建基块：

1. [配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)，使用户能够使用此功能。
1. [创建 Azure AD 测试用户](#create-an-azure-ad-test-user)，使用 Britta Simon 测试 Azure AD 单一登录。
1. [创建 Central Desktop 测试用户](#create-a-central-desktop-test-user)，在 Central Desktop 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
1. [分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)，使 Britta Simon 能够使用 Azure AD 单一登录。
1. [测试单一登录](#test-single-sign-on)，验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，会在 Azure 门户中启用 Azure AD 单一登录并在 Central Desktop 应用程序中配置单一登录。

**要为 Central Desktop 配置 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的“Central Desktop”应用程序集成页上，选择“单一登录”。

    ![配置单一登录链接][4]

1. 若要启用单一登录，请在“单一登录”对话框中的“模式”下拉列表中，选择“基于 SAML 的登录”。
 
    ![“单一登录”对话框](./media/central-desktop-tutorial/tutorial_centraldesktop_samlbase.png)

1. 在“Central Desktop 域和 URL”部分中执行以下步骤：

    ![Central Desktop 域和 URL 单一登录信息](./media/central-desktop-tutorial/tutorial_centraldesktop_url.png)

    a. 在“登录 URL”框中，使用以下模式键入 URL：`https://<companyname>.centraldesktop.com`

    b. 在“标识符”框中，使用以下模式键入 URL：
    | |
    |--|
    | `https://<companyname>.centraldesktop.com/saml2-metadata.php`|
    | `https://<companyname>.imeetcentral.com/saml2-metadata.php`|

    c. 在“回复 URL”框中，使用以下模式键入 URL：`https://<companyname>.centraldesktop.com/saml2-assertion.php`    
     
    > [!NOTE] 
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 请联系 [Central Desktop 客户端支持团队](https://imeetcentral.com/contact-us)获取这些值。 

1. 在“SAML 签名证书”部分中，选择“证书”。 然后将证书文件保存在计算机上。

    ![证书下载链接](./media/central-desktop-tutorial/tutorial_centraldesktop_certificate.png) 

1. 选择“保存”按钮。

    ![配置单一登录 -“保存”按钮](./media/central-desktop-tutorial/tutorial_general_400.png)
    
1. 在“Central Desktop 配置”部分中，选择“配置 Central Desktop”，打开“配置登录”窗口。 从“快速参考”部分，复制“注销 URL”、“SAML 实体 ID”和“SAML 单一登录服务 URL”。

    ![Central Desktop 配置](./media/central-desktop-tutorial/tutorial_centraldesktop_configure.png) 

1. 登录到 Central Desktop 租户。

1. 转到“设置”。 选择“高级”，然后选择“单一登录”。

    ![设置 - 高级](./media/central-desktop-tutorial/ic769563.png "Setup - Advanced")

1. 在“单一登录设置”页上，执行以下步骤：

    ![单一登录设置](./media/central-desktop-tutorial/ic769564.png "Single Sign-On Settings")
    
    a. 选择“启用 SAML v2 单一登录”。
    
    b. 在“SSO URL”框中，粘贴从 Azure 门户复制的“SAML 实体 ID”值。
    
    c. 在“SSO 登录 URL”框中，粘贴从 Azure 门户复制的“SAML 单一登录服务 URL”值。
    
    d. 在“SSO 注销 URL”框中，粘贴从 Azure 门户复制的“注销 URL”值。

1. 在“消息签名验证方法”部分执行以下步骤：

    ![消息签名验证方法](./media/central-desktop-tutorial/ic769565.png "Message Signature Verification Method") a. 选择“证书”。
    
    b. 从“SSO 证书”列表中，选择“RSH SHA256”。
    
    c. 在记事本中打开已下载的证书。 然后将证书内容复制并粘贴到“SSO 证书”字段。
        
    d. 选择“显示 SAMLv2 登录页的链接”。
    
    e. 选择“更新”。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了。 从“Active Directory” > “企业应用程序”部分添加该应用后，选择“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在 [Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)中阅读有关嵌入式文档功能的详细信息。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，选择“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/central-desktop-tutorial/create_aaduser_01.png)

1. 若要显示用户列表，请转到“用户和组”。 然后选择“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/central-desktop-tutorial/create_aaduser_02.png)

1. 若要打开“用户”对话框，请在“所有用户”对话框顶部选择“添加”。

    ![“添加”按钮](./media/central-desktop-tutorial/create_aaduser_03.png)

1. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/central-desktop-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 选择**创建**。
 
### <a name="create-a-central-desktop-test-user"></a>创建 Central Desktop 测试用户

要使 Azure AD 用户能够登录，必须将这些用户预配到 Central Desktop 应用程序中。 本部分介绍如何在 Central Desktop 中创建 Azure AD 用户帐户。

> [!NOTE]
> 可以使用 Central Desktop 提供的任何其他 Central Desktop 用户帐户创建工具或 API 来预配 Azure AD 用户帐户。

**为 Central Desktop 预配用户帐户：**

1. 登录到 Central Desktop 租户。

1. 转到“人员” > “内部成员”。

1. 选择“添加内部成员”。

    ![人员](./media/central-desktop-tutorial/ic781051.png "人员")
    
1. 在“新成员的电子邮件地址”框中，键入要预配的 Azure AD 帐户，并选择“下一步”。

    ![新成员的电子邮件地址](./media/central-desktop-tutorial/ic781052.png "Email Addresses of New Members")

1. 选择“添加内部成员”。

    ![添加内部成员](./media/central-desktop-tutorial/ic781053.png "Add Internal Member")
   
   >[!NOTE]
   >所添加的用户会收到一封电子邮件，其中包含用于激活其帐户的确认链接。
   
### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

本部分通过授予用户 Britta Simon 访问 Central Desktop 的权限，使其能够使用 Azure 单一登录。

![分配用户角色][200] 

**要将 Britta Simon 分配到 Central Desktop，请执行以下步骤：**

1. 在 Azure 门户中，打开应用程序视图。 转到目录视图，然后转到“企业应用程序”。

1. 选择“所有应用程序”。

    ![分配用户][201] 

1. 在应用程序列表中，选择“Central Desktop”。

    ![应用程序列表中的 Central Desktop 链接](./media/central-desktop-tutorial/tutorial_centraldesktop_app.png)  

1. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接][202]

1. 选择“添加”按钮。 然后，在“添加分配”对话框中，选择“用户和组”。

    ![“添加分配”窗格][203]

1. 在“用户和组”对话框中，选择“用户”列表中的“Britta Simon”。

1. 在“用户和组”对话框中，单击“选择”按钮。

1. 在“添加分配”对话框中，选择“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中选择“Central Desktop”磁贴时，可自动登录到 Central Desktop 应用程序。
有关访问面板的详细信息，请参阅[访问面板简介](../user-help/active-directory-saas-access-panel-introduction.md)。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/central-desktop-tutorial/tutorial_general_01.png
[2]: ./media/central-desktop-tutorial/tutorial_general_02.png
[3]: ./media/central-desktop-tutorial/tutorial_general_03.png
[4]: ./media/central-desktop-tutorial/tutorial_general_04.png

[100]: ./media/central-desktop-tutorial/tutorial_general_100.png

[200]: ./media/central-desktop-tutorial/tutorial_general_200.png
[201]: ./media/central-desktop-tutorial/tutorial_general_201.png
[202]: ./media/central-desktop-tutorial/tutorial_general_202.png
[203]: ./media/central-desktop-tutorial/tutorial_general_203.png

