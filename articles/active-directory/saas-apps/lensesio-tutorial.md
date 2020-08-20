---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Lenses.io 的集成 | Microsoft Docs
description: 了解如何配置 Azure Active Directory 和 Lenses.io 之间的单一登录。
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
ms.openlocfilehash: 0b8d736ab169ad07bd23a21d3a420bb6a044bf01
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88528596"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-the-lensesio-dataops-portal"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Lenses.io DataOps 门户集成。

本教程介绍如何将 [Lenses.io](https://lenses.io/) DataOps 门户与 Azure Active Directory (Azure AD) 集成。 将 Lenses.io 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Lenses.io 门户。
* 让用户使用其 Azure AD 帐户自动登录到 Lenses。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* Lenses 门户的实例。 可以通过[各种方式](https://lenses.io/product/deployment/)部署 Lenses 门户。
* 支持单一登录 (SSO) 的 Lenses.io [许可证](https://lenses.io/product/pricing/)。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Lenses.io 支持 SP 发起的 SSO

* 配置 Lenses.io 后，就可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-lensesio-from-the-gallery"></a>从库中添加 Lenses.io

若要配置 Lenses.io 与 Azure AD 的集成，需要从库中将 Lenses.io 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务。
1. 导航到“企业应用程序”，选择“所有应用程序” 。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Lenses.io” 。
1. 从结果面板中选择“Lenses.io”，然后添加该应用。 在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-sso-for-lensesio"></a>配置并测试 Lenses.io 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Lenses.io 门户的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 Lenses.io 中的相关用户之间建立链接关系。

若要配置并测试 Lenses.io 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户和组](#create-an-azure-ad-test-user-and-group)** - 使用 B.Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Lenses.io SSO](#configure-lensesio-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Lenses.io 测试组权限](#create-lensesio-test-group-permissions)** - 控制 B.Simon 在 Lenses.io 中应访问的内容（授权）。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“Lenses.io”应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值：

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<CUSTOMER_LENSES_BASE_URL>`，例如 `https://lenses.my.company.com`

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<CUSTOMER_LENSES_BASE_URL>`，例如 `https://lenses.my.company.com`

    c. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<CUSTOMER_LENSES_BASE_URL>/api/v2/auth/saml/callback?client_name=SAML2Client`
    例如，`https://lenses.my.company.com/api/v2/auth/saml/callback?client_name=SAML2Client`

    > [!NOTE]
    > 这些不是实际值。 根据 Lenses 门户实例的基 URL，使用实际的登录 URL、回复 URL 和标识符更新这些值。 可在 [Lenses.io SSO 文档](https://docs.lenses.io/install_setup/configuration/security.html#single-sign-on-sso-saml-2-0)查找详细信息。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上   。

    ![证书下载链接](common/metadataxml.png)

1. 在“设置 Lenses.io”部分中，使用上面的 XML 文件针对 Azure SSO 配置 Lenses。

### <a name="create-an-azure-ad-test-user-and-group"></a>创建 Azure AD 测试用户和组

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。 还将为 B.Simon 创建测试组，用于控制 B.Simon 在 Lenses 中的访问权限。
可在 [Lenses SSO 文档](https://docs.lenses.io/install_setup/configuration/security.html#id3)中了解 Lenses 如何使用组成员身份映射进行授权

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”  。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com`。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。
   1. 单击“创建”。

创建组：
1. 返回“Azure Active Directory”，选择“组” 
1. 选择屏幕顶部的“新建组”。
1. 在“组属性”中执行以下步骤：
   1. 在“组类型”字段中，选择 `Security`。
   1. 在“组名称”字段中，输入 `LensesUsers`
   1. 单击“创建”。
1. 选择组 `LensesUsers`，并记下“对象 ID”（例如 `f8b5c1ec-45de-4abd-af5c-e874091fb5f7`）。 此 ID 将在 Lenses 中用于将该组的用户映射到[正确的权限](https://docs.lenses.io/install_setup/configuration/security.html#id3)。  
   
将组分配给测试用户： 
1. 返回“Azure Active Directory”，选择“用户” 。
1. 选择测试用户 `B.Simon`。
1. 选择“组”。
1. 选择屏幕顶部的“添加成员身份”。
1. 搜索并选择 `LensesUsers`。
1. 单击“选择”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

本部分通过授予 B.Simon 访问 Lenses.io 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。
1. 在应用程序列表中，选择“Lenses.io”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。
1. 在“添加分配”对话框中，单击“分配”按钮。

## <a name="configure-lensesio-sso"></a>配置 Lenses.io SSO

若要在 Lenses.io 门户上配置单一登录，则在 Lenses 实例上安装下载的“联合元数据 XML”，并[配置 Lenses 来启用 SSO](https://docs.lenses.io/install_setup/configuration/security.html#configure-lenses) 。 

### <a name="create-lensesio-test-group-permissions"></a>创建 Lenses.io 测试组权限

本部分中，将使用用户[创建部分](#create-an-azure-ad-test-user-and-group)中记录的 `LensesUsers` 组的“对象 ID”在 Lenses 中创建组。
分配 `B.Simon` 在 Lenses 中应具有的所需权限。
可在 [Azure - Lenses 组映射](https://docs.lenses.io/install_setup/configuration/security.html#azure-groups)中查找详细信息。

## <a name="test-sso"></a>测试 SSO 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Lenses.io 磁贴时，应会自动登录到为其设置了 SSO 的 Lenses.io 门户。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [在 Lenses.io 实例中设置 SSO](https://docs.lenses.io/install_setup/configuration/security.html#single-sign-on-sso-saml-2-0)

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [通过 Azure AD 试用 Lenses.io](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security 中的会话控制是什么？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [如何通过高级可见性和控制保护 Lenses.io](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
