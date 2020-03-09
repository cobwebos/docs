---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Britive 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Britive 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ac1b71fe-0949-402c-8600-1fd6c61f6cd6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/25/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15e4a4ce18c88838afb19785fe0d9ca5a2d9ba98
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/01/2020
ms.locfileid: "78207194"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-britive"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Britive 的集成

本教程介绍如何将 Britive 与 Azure Active Directory (Azure AD) 集成。 将 Britive 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Britive。
* 让用户使用其 Azure AD 帐户自动登录到 Britive。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Britive 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Britive 支持 **SP** 发起的 SSO
* 配置 Britive 后，可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-britive-from-the-gallery"></a>从库中添加 Britive

若要配置 Britive 与 Azure AD 的集成，需要从库中将 Britive 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 **Britive**。 
1. 在结果面板中选择“Britive”，然后添加该应用。  在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-britive"></a>配置并测试 Britive 的 Azure AD 单一登录

使用名为 **B.Simon** 的测试用户配置并测试 Britive 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Britive 中的相关用户之间建立链接关系。

若要配置并测试 Britive 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Britive SSO](#configure-britive-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Britive 测试用户](#create-britive-test-user)** - 在 Britive 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中的“Britive”应用程序集成页上，找到“管理”部分并选择“单一登录”。   
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值  ：

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<TENANTNAME>.britive-app.com/sso` 

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`urn:amazon:cognito:sp:<UNIQUE_ID>` 

    > [!NOTE]
    > 这些不是实际值。 本教程稍后将介绍如何使用实际的登录 URL 和标识符来更新这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

1. 在“设置 Britive”部分，根据要求复制相应的 URL。 

    ![复制配置 URL](common/copy-configuration-urls.png)

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

在本部分，你将通过授予 B.Simon 访问 Britive 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在“应用程序”列表中选择“Britive”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-britive-sso"></a>配置 Britive SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Britive 网站。

1. 单击“管理员设置”图标并选择“安全性”。  

    ![Britive 配置](./media/britive-tutorial/configure1.png)

1. 选择“SSO 配置”并执行以下步骤： 

    ![Britive 配置](./media/britive-tutorial/configure2.png)

    a. 复制“受众/实体 ID”值，并将其粘贴到 Azure 门户上“基本 SAML 配置”部分的“标识符(实体 ID)”文本框中。   

    b. 复制“发起 SSO URL”值，并将其粘贴到 Azure 门户上“基本 SAML 配置”部分的“登录 URL”文本框中。   

    c. 单击“上传 SAML 元数据”，上传从 Azure 门户下载的元数据 XML 文件。  上传元数据文件后，上述值会自动填充。请保存更改。

### <a name="create-britive-test-user"></a>创建 Britive 测试用户

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Britive 网站。

1. 单击“管理员设置”图标并选择“用户管理”。  

    ![Britive 配置](./media/britive-tutorial/user1.png)

1. 单击“添加用户”。 

    ![Britive 配置](./media/britive-tutorial/user2.png)

1. 根据组织要求填写用户的所有必要详细信息，然后单击“添加用户”。 

    ![Britive 配置](./media/britive-tutorial/user3.png)

## <a name="test-sso"></a>测试 SSO

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“Britive”磁贴时，应会自动登录到设置了 SSO 的 Britive。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [在 Azure AD 中试用 Britive](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security 中的会话控制是什么？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)