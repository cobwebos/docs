---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Clever 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Clever 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 069ff13a-310e-4366-a147-d6ec5cca12a5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/26/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6884445b49b518654ccb1484b024728c4feb2b51
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/07/2020
ms.locfileid: "75689642"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-clever"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Clever 集成

本教程介绍了如何将 Azure Active Directory (Azure AD) 与 Clever 进行集成。 将 Clever 与 Azure AD 集成后，可以执行以下操作：

* 在 Azure AD 中控制谁有权访问 Clever。
* 让用户使用其 Azure AD 帐户自动登录到 Clever。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Clever 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Clever 支持 **SP** 发起的 SSO

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="adding-clever-from-the-gallery"></a>从库中添加 Clever

若要配置 Clever 与 Azure AD 的集成，需要从库中将 Clever 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Clever”   。
1. 在结果面板中选择“Clever”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-single-sign-on-for-clever"></a>配置并测试 Clever 的 Azure AD 单一登录

使用名为 B.Simon 的测试用户配置并测试 Clever 的 Azure AD SSO  。 若要运行 SSO，需要在 Azure AD 用户与 Clever 相关用户之间建立链接关系。

若要配置和测试 Clever 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Clever SSO](#configure-clever-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Clever 测试用户](#create-clever-test-user)** - 在 Clever 中创建 B.Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“Clever”应用程序集成页上，找到“管理”部分，选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值  ：

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://clever.com/in/<companyname>` 

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://clever.com/oauth/saml/metadata.xml` 

    > [!NOTE]
    > “登录 URL”值不是实际值。 请使用实际的登录 URL 更新此值。 请联系 [Clever 客户端支持团队](https://clever.com/about/contact/)来获取此值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. 在“使用 SAML 设置单一登录”  页的“SAML 签名证书”  部分中，单击“复制”按钮，以复制“应用联合元数据 URL”  ，并将它保存在计算机上。

    ![证书下载链接](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”    。
1. 选择屏幕顶部的“新建用户”  。
1. 在“用户”属性中执行以下步骤  ：
   1. 在“名称”  字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension  。 例如，`B.Simon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。  
   1. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，将通过授予 B.Simon 访问 Clever 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Clever”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-clever-sso"></a>配置 Clever SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Clever 公司站点。

1. 在工具栏中，单击“即时登录”。 

    ![即时登录](./media/clever-tutorial/ic798984.png "即时登录")

    > [!NOTE]
    > 在可以测试单一登录之前，你必须联系 [Clever 客户支持团队](https://clever.com/about/contact/)，以在后端启用 Office 365 SSO。

1. 在“即时登录”  页上，执行以下步骤：
 
    ![即时登录](./media/clever-tutorial/ic798985.png "即时登录")

    a. 键入“登录 URL”。 

    >[!NOTE]
    >“登录 URL”是一个自定义值。  请联系 [Clever 客户端支持团队](https://clever.com/about/contact/)来获取此值。

    b. 对于“标识系统”，选择“ADFS”。  

    c. 在“元数据 URL”文本框中，粘贴从 Azure 门户复制的**应用联合元数据 URL**值。 

    d. 单击“ **保存**”。

### <a name="create-clever-test-user"></a>创建 Clever 测试用户

若要使 Azure AD 用户能够登录到 Clever，必须将其预配到 Clever 中。

对于 Clever，请与 [Clever 客户端支持团队](https://clever.com/about/contact/)协作来在 Clever 平台中添加用户。 使用单一登录前，必须先创建并激活用户。

> [!NOTE]
> 可以使用 Clever 提供的任何其他 Clever 用户帐户创建工具或 API 来预配 Azure AD 用户帐户。

## <a name="test-sso"></a>测试 SSO 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Clever 磁贴时，应当会自动登录到为其设置了 SSO 的 Clever。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [通过 Azure AD 试用 Clever](https://aad.portal.azure.com/)