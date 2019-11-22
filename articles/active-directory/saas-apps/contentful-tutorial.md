---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Contentful 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Contentful 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f29e1015-d508-4698-a381-5d871c646161
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd218c61114c1e15009ace5a9a9bd7a536996e86
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2019
ms.locfileid: "72968670"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-contentful"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Contentful 集成

本教程介绍如何将 Contentful 与 Azure Active Directory (Azure AD) 集成。 将 Contentful 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Contentful。
* 让用户使用其 Azure AD 帐户自动登录到 Contentful。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了 Contentful 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Contentful 支持 **SP 和 IDP** 发起的 SSO
* Contentful 支持**恰时**用户预配

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值。 一个租户中只能配置一个实例。

## <a name="adding-contentful-from-the-gallery"></a>从库中添加 Contentful

要配置 Contentful 与 Azure AD 的集成，需要从库中将 Contentful 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Contentful”   。
1. 在结果中选择“Contentful”，然后添加应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-contentful"></a>为 Contentful 配置和测试 Azure AD 单一登录

使用名为 B.Simon 的测试用户配置和测试 Contentful 的 Azure AD SSO  。 若要运行 SSO，需要在 Azure AD 用户与 Contentful 相关用户之间建立链接关系。

若要配置和测试 Contentful 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    * **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    * **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Contentful SSO](#configure-contentful-sso)** - 在应用程序端配置单一登录设置。
    * **[创建 Contentful 测试用户](#create-contentful-test-user)** - 在 Contentful 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“Contentful”应用程序集成页上，找到“管理”部分，选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 若要在“IDP”发起模式下配置应用程序，请在“基本 SAML 配置”部分输入以下字段的值：  

    - 在“回复 URL”文本框中，从 Contentful 中的“SSO 设置”页复制 ACS（断言使用者服务）URL  。 它将如下所示：`https://be.contentful.com/sso/<organization_id>/consume`

1. 若要在“SP”发起模式下配置应用程序，请单击“设置其他 URL”并执行以下步骤：  

    - 在“登录 URL”文本框中，复制相同的 ACS（断言使用者服务）URL  。 它将如下所示：`https://be.contentful.com/sso/<organization_id>/login`

    > [!NOTE]
    > 这些不是实际值。 使用实际回复 URL 和登录 URL 更新这些值，方法是从 Contentful 的“SSO 设置”页复制 ACS（断言使用者服务）URL。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Contentful”部分，复制登录 URL 以配置 Contentful SSO  。

    ![复制配置 URL](media/contentful-tutorial/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左窗格中，依次选择“Azure Active Directory”、“用户”、“所有用户”。   
1. 选择屏幕顶部的“新建用户”  。
1. 在“用户”属性中执行以下步骤  ：
   1. 在“名称”  字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension  。 例如，`B.Simon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。  
   1. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，你将通过授予 B.Simon 访问 Contentful 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Contentful”  。
1. 在应用的概述页上找到“管理”部分，然后选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击页面底部的“选择”按钮    。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击页面底部的“选择”按钮   。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-contentful-sso"></a>配置 Contentful SSO

请按照以下步骤在“Contentful”端配置单一登录  。

1. 在 [Contentful](https://app.contentful.com) 中，导航到“组织设置”中的 SSO 设置页  。
1. 单击“设置 SSO”  。
1. 从 Azure AD 中的“设置 Contentful”部分复制并粘贴登录 URL  。
1. 从下载自 Azure AD 的 Base64 证书文件中复制并粘贴证书。
1. 为 SP 启动登录设置 SSO 名称。
1. 单击“启用 SSO”  。

如果这样不起作用，请联系 [Contentful 支持团队](mailto:support@contentful.com)。

### <a name="create-contentful-test-user"></a>创建 Contentful 测试用户

在本部分，我们将在 Contentful 中创建名为 B.Simon 的用户。 Contentful 支持默认启用的恰时用户预配。 此部分不存在任何操作项。 如果 Contentful 中不存在用户，则会在身份验证后创建一个新用户。

## <a name="test-sso"></a>测试 SSO

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Contentful 磁贴时，应会自动登录到为其设置了 SSO 的 Contentful。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [在 Azure AD 中试用 Contentful](https://aad.portal.azure.com/)
