---
title: 教程：Azure AD SSO 与 Smart Global Governance 集成
description: 了解如何在 Azure Active Directory 和 Smart Global Governance 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/04/2020
ms.author: jeedes
ms.openlocfilehash: d93cd2efe49f819c8adeb53674b6d4f465312e06
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88527411"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-smart-global-governance"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Smart Global Governance 的集成

本教程介绍如何将 Smart Global Governance 与 Azure Active Directory (Azure AD) 集成。 将 Smart Global Governance 与 Azure AD 集成后，可以：

* 使用 Azure AD 控制谁有权访问 Smart Global Governance。
* 允许用户使用其 Azure AD 帐户自动登录到 Smart Global Governance。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用程序与 Azure AD 集成的详细信息，请参阅[单一登录到 Azure Active Directory 中的应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录 (SSO) 的 Smart Global Governance 订阅。

## <a name="tutorial-description"></a>教程说明

本教程在测试环境中配置并测试 Azure AD SSO。

Smart Global Governance 支持 SP 和 IDP 发起的 SSO。

配置 Smart Global Governance 后，即可强制实施会话控制，从而实时保护组织的敏感数据以防外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="add-smart-global-governance-from-the-gallery"></a>从库中添加 Smart Global Governance

若要配置 Smart Global Governance 与 Azure AD 的集成，需要从库中将 Smart Global Governance 添加到托管的 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左窗格中选择“Azure Active Directory”。
1. 转到“企业应用程序”，选择“所有应用程序” 。
1. 若要添加应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，输入“Smart Global Governance”。
1. 在结果面板中选择“Smart Global Governance”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-smart-global-governance"></a>配置并测试 Smart Global Governance 的 Azure AD SSO

将使用名为“B.Simon”的测试用户配置并测试 Smart Global Governance 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 Smart Global Governance 中的对应用户之间建立链接关系。

若要配置并测试 Smart Global Governance 的 Azure AD SSO，请执行以下高级步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** ，使用户能够使用此功能。
    1. [创建 Azure AD 测试用户](#create-an-azure-ad-test-user)，以测试 Azure AD 单一登录。
    1. **[向测试用户授予访问权限](#grant-access-to-the-test-user)** ，使用户能够使用 Azure AD 单一登录。
1. 在应用程序端 **[配置 Smart Global Governance SSO](#configure-smart-global-governance-sso)** 。
    1. **[创建 Smart Global Governance 测试用户](#create-a-smart-global-governance-test-user)** ，对应于该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** ，验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO：

1. 在 [Azure 门户](https://portal.azure.com/)的“Smart Global Governance”应用程序集成页上的“管理”部分中，选择“单一登录”。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页上，选择“基本 SAML 配置”对应的铅笔按钮以编辑设置： 

   ![“基本 SAML 配置”对应的铅笔按钮](common/edit-urls.png)

1. 在“基本 SAML 配置”部分中，若要将应用程序配置为 IDP 发起的模式，请执行以下步骤。

    a. 在“标识符”框中，输入以下 URL 之一：

    - `https://eu-fr-south.console.smartglobalprivacy.com/platform/authentication-saml2/metadata`
    - `https://eu-fr-south.console.smartglobalprivacy.com/dpo/authentication-saml2/metadata`

    b. 在“回复 URL”框中，输入以下 URL 之一：

    - `https://eu-fr-south.console.smartglobalprivacy.com/platform/authentication-saml2/acs`
    - `https://eu-fr-south.console.smartglobalprivacy.com/dpo/authentication-saml2/acs`

1. 若要将应用程序配置为 SP 发起的模式，请选择“设置其他 URL”并完成以下步骤。

   - 在“登录 URL”框中，输入以下 URL 之一：

    - `https://eu-fr-south.console.smartglobalprivacy.com/dpo`
    - `https://eu-fr-south.console.smartglobalprivacy.com/platform`

1. 在“设置 SAML 单一登录”页上的“SAML 签名证书”部分，找到“证书(原始)”对应的“下载”链接，以下载证书并将其保存到计算机上：   

    ![证书下载链接](common/certificateraw.png)

1. 在“设置 Smart Global Governance”部分中，根据需要复制相应的 URL：

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分中，你将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中选择“Azure Active Directory”。 选择“用户”，然后选择“所有用户” 。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中，完成以下步骤：
   1. 在“名称”框中，输入 **B.Simon**。  
   1. 在“用户名”框中，输入 \<username>@\<companydomain>.\<extension>。 例如，`B.Simon@contoso.com`。
   1. 选择“显示密码”，然后记下“密码”框中显示的值。 
   1. 选择“创建”。

### <a name="grant-access-to-the-test-user"></a>向测试用户授予访问权限

在本部分中，将通过授予 B.Simon 访问 Smart Global Governance 的权限，允许该用户使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Smart Global Governance”。
1. 在应用概览页上，在“管理”部分中选择“用户和组”： 

   ![选择“用户和组”](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”：  

    ![选择“添加用户”](common/add-assign-user.png)

1. 在“用户和组”对话框中的“用户”列表内选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮 。
1. 在“添加分配”对话框中选择“分配”。 

## <a name="configure-smart-global-governance-sso"></a>配置 Smart Global Governance SSO

若要在 Smart Global Governance 端配置单一登录，需要将下载的原始证书以及从 Azure 门户复制的相应 URL 发送给 [Smart Global Governance 支持团队](mailto:support.tech@smartglobal.com)。 支持人员将在两端配置正确的 SAML SSO 连接。

### <a name="create-a-smart-global-governance-test-user"></a>创建 Smart Global Governance 测试用户

与  [Smart Global Governance 支持团队](mailto:support.tech@smartglobal.com)协作，将名为“B.Simon”的用户添加到 Smart Global Governance。 使用单一登录前，必须先创建并激活用户。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用访问面板测试 Azure AD SSO 配置。

在访问面板中选择“Smart Global Governance”磁贴时，应当会自动登录到设置了 SSO 的 Smart Global Governance 实例。 有关访问面板的详细信息，请参阅[访问面板简介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [在 Azure AD 中试用 Smart Global Governance](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security 中的会话控制是什么？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [如何通过高级可见性和控制保护 Smart Global Governance](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)