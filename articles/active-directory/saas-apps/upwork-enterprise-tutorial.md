---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Upwork Enterprise 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Upwork Enterprise 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 254631e2-1a77-43e6-acb1-b26ba7e77efc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 12/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d5d9377ec88764f6f89bf486fe515a539c12077a
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83747461"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-upwork-enterprise"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Upwork Enterprise 集成

本教程介绍如何将 Upwork Enterprise 与 Azure Active Directory (Azure AD) 进行集成。 将 Upwork Enterprise 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Upwork Enterprise。
* 让用户使用其 Azure AD 帐户自动登录到 Upwork Enterprise。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了 Upwork Enterprise 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Upwork Enterprise 支持“SP 和 IDP”发起的 SSO 
* Upwork Enterprise 支持实时用户预配 

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="adding-upwork-enterprise-from-the-gallery"></a>从库中添加 Upwork Enterprise

要配置 Upwork Enterprise 与 Azure AD 的集成，需要从库中将 Upwork Enterprise 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 Upwork Enterprise   。
1. 从结果面板中选择 Upwork Enterprise，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-upwork-enterprise"></a>配置并测试 Upwork Enterprise 的 Azure AD 单一登录

使用名为 B.Simon 的测试用户配置和测试 Upwork Enterprise 的 Azure AD SSO  。 若要正常使用 SSO，需要在 Azure AD 用户与 Upwork Enterprise 相关用户之间建立链接关系。

若要配置和测试 Upwork Enterprise 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    * **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    * **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Upwork Enterprise SSO](#configure-upwork-enterprise-sso)** - 在应用程序端配置单一登录设置。
    * **[创建 Upwork Enterprise 测试用户](#create-upwork-enterprise-test-user)** - 在 Upwork Enterprise 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的 Upwork Enterprise 应用程序集成页上，找到“管理”部分，选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在基本 SAML 配置  部分，应用程序进行了预配置，且已通过 Azure 预填充了必要的 URL。 用户需要单击“保存”  按钮来保存配置。

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://www.upwork.com/ab/account-security/login`

1. 单击“ **保存**”。

1. Upwork Enterprise 应用程序需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。

    ![image](common/default-attributes.png)

1. 除了上述属性，Upwork Enterprise 应用程序还要求在 SAML 响应中传递回更多的属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。

    | 名称 |   源属性|
    | ------------ | --------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | 国家/地区 | user.country |
    | 电子邮件 | user.userprincipalname |

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

1. 在“设置 Upwork Enterprise”部分中，根据要求复制相应 URL  。

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

在本部分中，通过授予 B.Simon 访问 Upwork Enterprise 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择 Upwork Enterprise  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-upwork-enterprise-sso"></a>配置 Upwork Enterprise SSO

若要在 Upwork Enterprise 端配置单一登录，需要将下载的“联合元数据 XML”以及从 Azure 门户复制的相应 URL 发送给 [Upwork Enterprise 支持团队](https://support.upwork.com/hc/)   。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-upwork-enterprise-test-user"></a>创建 Upwork Enterprise 测试用户

本部分将在 Upwork Enterprise 中创建一个名为 B.Simon 的用户。 Upwork Enterprise 支持默认启用的实时用户预配。 此部分不存在任何操作项。 如果 Upwork Enterprise 中尚不存在用户，则会在身份验证后创建一个新用户。

## <a name="test-sso"></a>测试 SSO

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Upwork Enterprise 磁贴时，应会自动登录到已为其设置了 SSO 的 Upwork Enterprise。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [通过 Azure AD 试用 Upwork Enterprise](https://aad.portal.azure.com/)