---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Single Sign-on for Skytap 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Single Sign-on for Skytap 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d6cb7ab2-da1a-4015-8e6f-c0c47bb6210f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/13/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 33a8035f16f531dbb17177d1c2f4d5cd344e5a28
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565751"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-single-sign-on-for-skytap"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Single Sign-on for Skytap 的集成

本教程介绍如何将 Single Sign-on for Skytap 与 Azure Active Directory (Azure AD) 集成。 将 Single Sign-on for Skytap 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Single Sign-on for Skytap。
* 让用户使用其 Azure AD 帐户自动登录到 Single Sign-on for Skytap。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解服务型软件 (SaaS) 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Single Sign-on for Skytap 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Single Sign-on for Skytap 支持 SP 和 IDP 发起的 SSO。
* 配置 Single Sign-on for Skytap 后，可以强制实施会话控制。 这可以实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="add-single-sign-on-for-skytap-from-the-gallery"></a>从库中添加 Single Sign-on for Skytap

若要配置 Single Sign-on for Skytap 与 Azure AD 的集成，需要从库中将 Single Sign-on for Skytap 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 转到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 **Single Sign-on for Skytap**。 
1. 在结果面板中选择“Single Sign-on for Skytap”，然后添加该应用。  在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-single-sign-on-for-skytap"></a>配置并测试 Single Sign-on for Skytap 的 Azure AD 单一登录

使用名为 **B.Simon** 的测试用户配置并测试 Single Sign-on for Skytap 的 Azure AD SSO。 若要正常使用 SSO，请在 Azure AD 用户与 Single Sign-on for Skytap 中的相关用户之间建立链接关系。

下面是配置和测试 Single Sign-on for Skytap 的 Azure AD SSO 的一般步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** ，使用户能够使用此功能。

    a. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** ，以使用 B.Simon 测试 Azure AD 单一登录。

    b. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** ，使 B.Simon 能够使用 Azure AD 单一登录。
1. **[配置 Single Sign-on for Skytap SSO](#configure-single-sign-on-for-skytap-sso)** ，在应用程序端配置单一登录设置。

    a. **[创建 Single Sign-on for Skytap 测试用户](#create-single-sign-on-for-skytap-test-user)** ，在 Single Sign-on for Skytap 中创建 B.Simon 的对应用户。 此对应用户将链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** ，验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中的“Single Sign-on for Skytap”应用程序集成页上，找到“管理”部分。   选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页上，选择“基本 SAML 配置”对应的笔形图标以编辑设置   。

   ![“设置 SAML 单一登录”页的屏幕截图，其中突出显示了铅笔图标](common/edit-urls.png)

1. 若要在“IDP”发起模式下配置应用程序，请在“基本 SAML 配置”部分输入以下字段的值：  

    a. 在“标识符”  文本框中，使用以下模式键入 URL：`http://pingone.com/<custom EntityID>`

    b. 在“回复 URL”文本框中，键入使用以下模式的 URL：`https://sso.connect.pingidentity.com/sso/sp/ACS.saml2` 

1. 若要在“SP”发起模式下配置应用程序，请选择“设置其他 URL”并执行以下步骤：  

    a. 在“登录 URL”  文本框中，使用以下模式键入 URL：`https://sso.connect.pingidentity.com/sso/sp/initsso?saasid=<saasid>&idpid=<idpid>`

    
    b. 在“中继状态”文本框中，键入使用以下模式的 URL：`https://pingone.com/1.0/<custom ID>` 

    > [!NOTE]
    > 这些不是实际值。 请使用“标识符”、“回复 URL”、“登录 URL”和“中继状态”更新这些值。 请联系 [Single Sign-on for Skytap 客户端支持团队](mailto:support@skytap.com)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分，找到“联合元数据 XML”    。 选择“下载”以下载元数据文件，并将其保存到计算机上。 

    ![证书下载链接的屏幕截图](common/metadataxml.png)

1. 在“设置 Single Sign-on for Skytap”部分，根据要求复制相应的一个或多个 URL。 

    ![复制配置 URL 的屏幕截图](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，你将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左窗格中，选择“Azure Active Directory” > “用户” > “所有用户”。   
1. 选择屏幕顶部的“新建用户”  。
1. 在“用户”属性中执行以下步骤  ：
   1. 在“名称”  字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension  。 例如，`B.Simon@contoso.com` 。
   1. 选中“显示密码”复选框，并记下“密码”字段中显示的值。  
   1. 选择“创建”  。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，你将通过授予 B.Simon 访问 Single Sign-on for Skytap 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序” > “所有应用程序”。  
1. 在“应用程序”列表中选择“Single Sign-on for Skytap”。 
1. 在应用的概述页中找到“管理”部分，然后选择“用户和组”   。

   ![“管理”部分的屏幕截图，其中突出显示了“用户和组”](common/users-groups-blade.png)

1. 选择“添加用户”。  在“添加分配”对话框中选择“用户和组”。  

    ![“用户和组”页的屏幕截图，其中已突出显示“添加用户”](common/add-assign-user.png)

1. 在“用户和组”对话框的“用户”列表中，选择“B.Simon”。   然后，选择屏幕底部的“选择”按钮。 
1. 如果希望在 SAML 断言中使用任何角色值，请在“选择角色”对话框中，从列表中为用户选择相应的角色。  然后，选择屏幕底部的“选择”按钮。 
1. 在“添加分配”对话框中选择“分配”。  

## <a name="configure-single-sign-on-for-skytap-sso"></a>配置 Single Sign-on for Skytap SSO

若要在 Single Sign-on for Skytap 端配置单一登录，需要将下载的“联合元数据 XML”以及从 Azure 门户复制的相应 URL 发送给 [Single Sign-on for Skytap 支持团队](mailto:support@skytap.com)。  他们会对此设置进行配置，以在两端正确设置 SAML SSO 连接。


### <a name="create-single-sign-on-for-skytap-test-user"></a>创建 Single Sign-on for Skytap 测试用户

在本部分，你将在 Single Sign-on for Skytap 中创建名为 B.Simon 的用户。 在 [Single Sign-on for Skytap 支持团队](mailto:support@skytap.com)的配合下，将用户添加到 Single Sign-on for Skytap 平台。 只有在创建并激活用户后，才能使用单一登录。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用访问面板测试 Azure AD 单一登录配置。

在访问面板中选择“Single Sign-on for Skytap”磁贴时，应会自动登录到设置了 SSO 的 Single Sign-on for Skytap。 有关详细信息，请参阅[访问面板简介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他资源

- [用于将 SaaS 应用程序与 Azure Active Directory 集成的教程](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [通过 Azure AD 试用 Slack](https://aad.portal.azure.com/)

