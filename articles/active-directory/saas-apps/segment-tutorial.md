---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Segment 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Segment 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 20061270-c9d2-4ca7-a913-32d6fc67f209
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 06/18/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ceaba9c71f749a80391ef3288bf37350e44e1f9
ms.sourcegitcommit: 51718f41d36192b9722e278237617f01da1b9b4e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2020
ms.locfileid: "85101632"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-segment"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Segment 集成

在本教程中，了解如何将 Segment 与 Azure Active Directory (Azure AD) 集成。 将 Segment 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Segment。
* 让用户使用其 Azure AD 帐户自动登录到 Segment。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Segment 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Segment 支持 **SP 和 IDP** 发起的 SSO
* Segment 支持**实时**用户预配

* 配置 Segment 后，可强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-segment-from-the-gallery"></a>从库中添加 Segment

要配置 Segment 与 Azure AD 的集成，需要从库中将 Segment 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务。
1. 导航到“企业应用程序”，选择“所有应用程序” 。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Segment”。 
1. 从结果面板中选择“Segment”，然后添加该应用。 在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-single-sign-on-for-segment"></a>配置和测试 Segment 的 Azure AD 单一登录

使用名为“B.Simon”的测试用户来配置并测试 Segment 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Segment 中的相关用户之间建立链接关系。

若要配置并测试 Segment 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Segment SSO](#configure-segment-sso)** - 在应用程序端配置单一登录设置。
    1. [创建 Segment 测试用户](#create-segment-test-user) - 在 Segment 中创建 B.Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“Segment”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在“IDP”发起的模式下配置应用程序，请在“基本 SAML 配置”部分中输入以下字段的值 ：

    a. 在“标识符”文本框中，使用以下模式键入 URL：`urn:auth0:segment-prod:samlp-<CUSTOMER_VALUE>`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://segment-prod.auth0.com/login/callback?connection=<CUSTOMER_VALUE>`

1. 如果要在 SP 发起的模式下配置应用程序，请单击“设置其他 URL”，并执行以下步骤：

    在“登录 URL”文本框中，键入 URL：`https://app.segment.com`

    > [!NOTE]
    > 这些值是占位符。 需要使用实际的标识符、回复 URL 和登录 URL。 获取这些值的步骤稍后在本教程中介绍。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上   。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Segment”部分中，根据要求复制相应 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”  。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com`。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。 
   1. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，将通过授予 Segment 访问权限，允许 B.Simon 使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Segment”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。  

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。  
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 
1. 在“添加分配”对话框中，单击“分配”按钮。 

## <a name="configure-segment-sso"></a>配置 Segment SSO

1. 在新的 Web 浏览器窗口中，以管理员身份登录到 Segment 公司站点。

1. 单击“设置”图标并向下滚动到“身份验证”，然后单击“连接”。  

    ![Segment 配置](./media/segment-tutorial/segment1.PNG)

1. 单击“添加新连接”。

    ![Segment 配置](./media/segment-tutorial/segment2.PNG)

1. 选择“SAML 2.0”作为要配置的连接，然后单击“选择连接”按钮。 

    ![Segment 配置](./media/segment-tutorial/segment3.PNG)

1. 在下面的页上，执行以下步骤：

    ![Segment 配置](./media/segment-tutorial/segment4.PNG)

    a. 复制“单一登录 URL”值，将其粘贴到“回复 URL”框中，位于 Azure 门户的“基本 SAML 配置”对话框中。  

    b. 复制“受众 URL”值，将其粘贴到“标识符 URL”框中，位于 Azure 门户的“基本 SAML 配置”对话框中。 

    c. 单击“下一步”。

    ![Segment 配置](./media/segment-tutorial/segment5.PNG)

1. 在“SAML 2.0 终结点 URL”框中，粘贴从 Azure 门户复制的“登录 URL”值 。

1. 在记事本中打开从 Azure 门户下载的“证书(Base64)”，将内容粘贴到“公共证书”文本框中。 

1. 单击“配置连接”

### <a name="create-segment-test-user"></a>创建 Segment 测试用户

在本部分中，将在 Segment 中创建一个名为 B.Simon 的用户。 Segment 支持默认已启用的实时用户预配。 此部分不存在任何操作项。 如果 Segment 中尚不存在用户，将在身份验证后创建一个新用户。

## <a name="test-sso"></a>测试 SSO 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Segment 磁贴时，会自动登录到要为其设置了 SSO 的 Segment。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [试用 Segment 的 Azure AD](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security 中的会话控制是什么？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [如何通过高级可见性和控制功能保护 Segment](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

