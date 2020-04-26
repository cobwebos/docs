---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 MongoDB Cloud 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 MongoDB Cloud 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ef392044-235b-4d80-8a33-eeba9b142849
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/03/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef8094464b428f1f46017b0d4abaac5aeb1fa428
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024504"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mongodb-cloud"></a>教程：Azure Active Directory 单一登录 (SSO) 与 MongoDB Cloud 的集成

本教程介绍如何将 MongoDB Cloud 与 Azure Active Directory (Azure AD) 集成。 将 MongoDB Cloud 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 MongoDB Cloud、MongoDB Atlas、MongoDB Community、MongoDB University 和 MongoDB Support。
* 让用户使用其 Azure AD 帐户自动登录到 MongoDB Cloud。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解服务型软件 (SaaS) 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>先决条件

要开始，需要：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 一个允许进行单一登录 (SSO) 的 MongoDB Cloud 组织。你可以注册获取[免费群集](https://www.mongodb.com/cloud)

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* MongoDB Cloud 支持 SP 和 IDP 发起的 SSO。  
* MongoDB Cloud 支持实时用户预配。 
* 配置 MongoDB Cloud 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 有关详细信息，请参阅[了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="add-mongodb-cloud-from-the-gallery"></a>从库中添加 MongoDB Cloud

若要配置 MongoDB Cloud 与 Azure AD 的集成，需要从库中将 MongoDB Cloud 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧窗格中，选择“Azure Active Directory”  。
1. 转到“企业应用程序”，并选择“所有应用程序”。  
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 MongoDB Cloud。  
1. 在结果中选择“MongoDB Cloud”，然后添加该应用。  在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-single-sign-on-for-mongodb-cloud"></a>为 MongoDB Cloud 配置并测试 Azure AD 单一登录

使用名为 B.Simon 的测试用户配置并测试 MongoDB Cloud 的 Azure AD SSO。  若要使 SSO 正常工作，需要在 Azure AD 用户与 MongoDB Cloud 中的相关用户之间建立链接关系。

若要配置并测试 MongoDB Cloud 的 Azure AD SSO，请完成以下构建基块：

1. [配置 Azure AD SSO](#configure-azure-ad-sso)，使用户能够使用此功能。
    1. [创建 Azure AD 测试用户](#create-an-azure-ad-test-user)，以使用 B.Simon 测试 Azure AD 单一登录。
    1. [分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)，使 B.Simon 能够使用 Azure AD 单一登录。
1. [配置 MongoDB Cloud SSO](#configure-mongodb-cloud-sso) - 在应用程序端配置单一登录设置。
    1. [创建 MongoDB Cloud 测试用户](#create-a-mongodb-cloud-test-user) - 在 MongoDB Cloud 中创建 B.Simon 的对应用户，并将其关联到该用户的 Azure AD 表示形式。
1. [测试 SSO](#test-sso)，验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中，在“MongoDB Cloud”应用程序集成页上，找到“管理”部分   。 选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页上，选择“基本 SAML 配置”对应的铅笔图标以编辑设置   。

   ![“设置 SAML 单一登录”页的屏幕截图，其中突出显示了铅笔图标](common/edit-urls.png)

1. 若要在“IDP”发起模式下配置应用程序，请在“基本 SAML 配置”部分输入以下字段的值：  

    a. 在“标识符”  文本框中，使用以下模式键入 URL：`https://www.okta.com/saml2/service-provider/<Customer_Unique>`

    b. 在“回复 URL”文本框中，键入使用以下模式的 URL：`https://auth.mongodb.com/sso/saml2/<Customer_Unique>`

1. 若要在“SP”发起模式下配置应用程序，请选择“设置其他 URL”并执行以下步骤：  

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://cloud.mongodb.com/sso/<Customer_Unique>`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 若要获取这些值，请联系 [MongoDB Cloud 客户端支持团队](https://support.mongodb.com/)。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. MongoDB Cloud 应用程序需要特定格式的 SAML 断言，因此，需要在 SAML 令牌属性配置中添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。

    ![默认属性的屏幕截图](common/default-attributes.png)

1. 除上述属性以外，MongoDB Cloud 应用程序还要求在 SAML 响应中传回其他几个属性。 这些属性也是预先填充的，但可以根据要求查看它们。
    
    | 名称 | |  源属性|
    | ---------------| --------------- | --------- |
    | email | | user.userprincipalname |
    | firstName | | user.givenname |
    | lastName | | user.surname |

1. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分，找到“联合元数据 XML”    。 选择“下载”以下载证书，并将其保存在计算机上。 

    ![“SAML 签名证书”部分的屏幕截图，其中突出显示了“下载”链接](common/metadataxml.png)

1. 在“设置 MongoDB Cloud”部分中，根据你的需要复制相应的 URL。 

    ![“设置 MongoDB Cloud”部分的屏幕截图，其中突出显示了 URL](common/copy-configuration-urls.png)
### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，你将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左窗格中，选择“Azure Active Directory” > “用户” > “所有用户”。   
1. 选择屏幕顶部的“新建用户”  。
1. 在“用户”属性中执行以下步骤  ：
   1. 在“名称”  字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension  。 例如，`B.Simon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下密码  。
   1. 选择“创建”  。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，你将通过授予 B.Simon 访问 MongoDB Cloud 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序” > “所有应用程序”。  
1. 在应用程序列表中选择“MongoDB Cloud”。 
1. 在应用的概述页中找到“管理”部分，然后选择“用户和组”   。

   ![“管理”部分的屏幕截图，其中突出显示了“用户和组”](common/users-groups-blade.png)

1. 选择“添加用户”。  然后，在“添加分配”对话框中选择“用户和组”。  

    ![“用户和组”页的屏幕截图，其中已突出显示“添加用户”](common/add-assign-user.png)

1. 在“用户和组”对话框中，从用户列表中选择“B.Simon”。   然后选择屏幕底部的“选择”  。
1. 如果希望在 SAML 断言中使用任何角色值，请在“选择角色”对话框中，从列表中为用户选择相应的角色。  然后选择屏幕底部的“选择”  。
1. 在“添加分配”对话框中选择“分配”。  

## <a name="configure-mongodb-cloud-sso"></a>配置 MongoDB Cloud SSO

若要在 MongoDB Cloud 端配置单一登录，需要使用从 Azure 门户复制的相应 URL。 还需要为 MongoDB Cloud 组织配置联合身份验证应用程序。 请按照 [MongoDB Cloud 文档](https://docs.atlas.mongodb.com/security/federated-auth-azure-ad/)中的说明操作。 如有问题，请联系 [MongoDB Cloud 支持团队](https://support.mongodb.com/)。

### <a name="create-a-mongodb-cloud-test-user"></a>创建 MongoDB Cloud 测试用户

MongoDB Cloud 支持默认启用的实时用户预配。 无需执行其他操作。 如果 MongoDB Cloud 中尚不存在用户，身份验证后会创建一个新用户。

## <a name="test-sso"></a>测试 SSO 

在本部分，你将使用访问面板测试 Azure AD 单一登录配置。

在访问面板中选择 MongoDB Cloud 磁贴时，会自动登录到为其设置了 SSO 的 MongoDB Cloud。 有关详细信息，请参阅[从“我的应用”门户登录和启动应用](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他资源

- [用于将 SaaS 应用程序与 Azure Active Directory 集成的教程](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [在 Azure 上注册 MongoDB Atlas](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/mongodb.mongodb_atlas_azure_08082019?tab=Overview)

- [尝试通过 Azure AD 使用 MongoDB Cloud](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security 中的会话控制是什么？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [通过高级可见性和控制保护 MongoDB Cloud](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

