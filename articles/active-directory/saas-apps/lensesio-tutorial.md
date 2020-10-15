---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Lenses.io 的集成 | Microsoft Docs
description: 本教程介绍如何在 Azure Active Directory 与 Lenses.io 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/02/2020
ms.author: jeedes
ms.openlocfilehash: 48a1e50d451abb429e9bc33308909b368283644f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88661429"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-the-lensesio-dataops-portal"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Lenses.io DataOps 门户集成

本教程介绍如何将 [Lenses.io](https://lenses.io/) DataOps 门户与 Azure Active Directory (Azure AD) 集成。 将 Lenses.io 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Lenses.io 门户。
* 让用户使用其 Azure AD 帐户自动登录到 Lenses。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解软件即服务 (SaaS) 应用与 Azure AD 集成的详细信息，请参阅 [Azure AD 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* Lenses 门户的实例。 可以从许多[部署选项](https://lenses.io/product/deployment/)中进行选择。
* 支持单一登录 (SSO) 的 Lenses.io [许可证](https://lenses.io/product/pricing/)。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Lenses.io 支持服务提供商 (SP) 发起的 SSO。

* 配置 Lenses.io 后，可以强制实施会话控制。 会话控制可实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="add-lensesio-from-the-gallery"></a>从库中添加 Lenses.io

若要配置 Lenses.io 与 Azure AD 的集成，将 Lenses.io 添加到托管 SaaS 应用列表：

1. 使用工作或学校帐户或者个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧窗格中选择“Azure Active Directory”服务。 
1. 转到“企业应用程序”，并选择“所有应用程序”。 
1. 选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，输入“Lenses.io” 。
1. 从结果面板中选择“Lenses.io”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-lensesio"></a>配置并测试 Lenses.io 的 Azure AD SSO

你将创建名为 B.Simon 的测试用户，以配置并测试 Lenses.io 门户的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 Lenses.io 中的相关用户之间建立链接关系。

完成以下步骤：

1. [配置 Azure AD SSO](#configure-azure-ad-sso)，使用户能够使用此功能。
    1. [创建 Azure AD 测试用户和组](#create-an-azure-ad-test-user-and-group)以使用 B.Simon 用户测试 Azure AD SSO。
    1. [分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)，使 B.Simon 能够使用 Azure AD SSO。
1. [配置 Lenses.io SSO](#configure-lensesio-sso) 以在应用程序端配置 SSO 设置。
    1. [创建 Lenses.io 测试组权限](#create-lensesio-test-group-permissions)以控制 B.Simon 在 Lenses.io 中可访问的内容（授权）。
1. [测试 SSO](#test-sso)，验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO：

1. 在 [Azure 门户](https://portal.azure.com/)的“Lenses.io”应用程序集成页上，找到“管理”部分，然后选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“使用 SAML 设置单一登录”页上，选择“基本 SAML 配置”对应的编辑/笔图标以编辑设置 。

   ![显示用于编辑基本 SAML 配置的图标的屏幕截图。](common/edit-urls.png)

1. 在“基本 SAML 配置”部分中，在以下文本输入框中输入值：

    a. **登录 URL**：输入采用以下模式的 URL：`https://<CUSTOMER_LENSES_BASE_URL>`。 例如 `https://lenses.my.company.com`。

    b. **标识符(实体 ID)** ：输入采用以下模式的 URL：`https://<CUSTOMER_LENSES_BASE_URL>`。 例如 `https://lenses.my.company.com`。

    c. **回复 URL**：输入采用以下模式的 URL：`https://<CUSTOMER_LENSES_BASE_URL>/api/v2/auth/saml/callback?client_name=SAML2Client`。 例如 `https://lenses.my.company.com/api/v2/auth/saml/callback?client_name=SAML2Client`。

    > [!NOTE]
    > 这些不是实际值。 使用实际的登录 URL、回复 URL 和 Lenses 门户实例基 URL 的标识符更新这些值。 请参阅 [Lenses.io SSO 文档](https://docs.lenses.io/install_setup/configuration/security.html#single-sign-on-sso-saml-2-0)以了解详细信息。

1. 在“设置 SAML 单一登录”页上，转到“SAML 签名证书”部分 。 找到“联合元数据 XML”，然后选择“下载”以下载证书，并将其保存在计算机上 。

    ![显示证书下载链接的屏幕截图。](common/metadataxml.png)

1. 在“设置 Lenses.io”部分中，使用下载的 XML 文件针对 Azure SSO 配置 Lenses。

### <a name="create-an-azure-ad-test-user-and-group"></a>创建 Azure AD 测试用户和组

在 Azure 门户中，你将创建名为 B.Simon 的测试用户。 然后，你将创建一个用于控制 B.Simon 在 Lenses 中具有的访问权限的测试组。

可在 [Lenses SSO 文档](https://docs.lenses.io/install_setup/configuration/security.html#id3)中了解 Lenses 如何使用组成员身份映射进行授权。

创建测试用户：

1. 在 Azure 门户的左窗格中，依次选择“Azure Active Directory”、“用户”、“所有用户”  。
1. 在屏幕顶部选择“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”框中，输入 **B.Simon**。  
   1. 在“用户名”框中，输入 username@companydomain.extension。 例如，B.Simon@contoso.com。
   1. 选中“显示密码”复选框。 记下“密码”框中显示的密码。
   1. 选择“创建”。

**创建组：**

1. 转到“Azure Active Directory”，然后选择“组” 。
1. 在屏幕顶部选择“新建组”。
1. 在“组属性”中执行以下步骤：
   1. 在“组类型”框中，选择“安全性” 。
   1. 在“组名”框中，输入“LensesUsers” 。
   1. 选择“创建”。
1. 选择组“LensesUsers”并复制“对象 ID”（如 f8b5c1ec-45de-4abd-af5c-e874091fb5f7） 。 你将在 Lenses 中使用此 ID 来将该组的用户映射到[正确的权限](https://docs.lenses.io/install_setup/configuration/security.html#id3)。  

**将组分配给测试用户：**

1. 转到“Azure Active Directory”，然后选择“用户” 。
1. 选择测试用户“B.Simon”。
1. 选择“组”。
1. 在屏幕顶部选择“添加成员身份”。
1. 搜索并选择“LensesUsers”。
1. 单击“选择”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

本部分通过授予 B.Simon 访问 Lenses.io 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Lenses.io”。
1. 在应用概述页上的“管理”部分，选择“用户和组” 。

   ![显示“用户和组”链接的屏幕截图。](common/users-groups-blade.png)

1. 选择“添加用户”。

   ![显示“添加用户”链接的屏幕截图。](common/add-assign-user.png)

1. 在“添加分配”对话框中选择“用户和组”。
1. 在“用户和组”对话框的“用户”列表中，选择“B.Simon” 。 然后单击屏幕底部的“选择”按钮。****
1. 如果希望在 SAML 断言中使用任何角色值，请在“选择角色”对话框中，从列表中为用户选择相应的角色。 然后单击屏幕底部的“选择”按钮。****
1. 在“添加分配”对话框中，选择“分配”按钮。 

## <a name="configure-lensesio-sso"></a>配置 Lenses.io SSO

若要在 Lenses.io 门户上配置 SSO，请在 Lenses 实例上安装下载的“联合元数据 XML”，并[配置 Lenses 来启用 SSO](https://docs.lenses.io/install_setup/configuration/security.html#configure-lenses) 。

### <a name="create-lensesio-test-group-permissions"></a>创建 Lenses.io 测试组权限

1. 若要在 Lenses 中创建组，使用“LensesUsers”组的“对象 ID” 。 这是在用户[创建部分](#create-an-azure-ad-test-user-and-group)中复制的 ID。
1. 为 B.Simon 分配所需的权限。

有关详细信息，请参阅 [Azure - Lenses 组映射](https://docs.lenses.io/install_setup/configuration/security.html#azure-groups)。

## <a name="test-sso"></a>测试 SSO

在本部分，使用访问面板测试 Azure AD SSO 配置。

选择访问面板中的 Lenses.io 磁贴时，应会自动登录到 Lenses.io 门户。 有关详细信息，请参阅[访问面板简介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他资源

- [在 Lenses.io 实例中设置 SSO](https://docs.lenses.io/install_setup/configuration/security.html#single-sign-on-sso-saml-2-0)

- [有关如何将 SaaS 应用程序与 Azure AD 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure AD 的应用程序访问与 SSO 是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure AD 中的条件访问是什么？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [通过 Azure AD 试用 Lenses.io](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security 中的会话控制是什么？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [如何通过高级可见性和控制保护 Lenses.io](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
