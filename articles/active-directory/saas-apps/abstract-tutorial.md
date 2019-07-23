---
title: 教程：Azure Active Directory 与 Abstract 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Abstract 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2eb167ab-d769-4661-a8cb-ae371cb63d2a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81886fa9165269d89bde8306c5829be41952c190
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302588"
---
# <a name="tutorial-integrate-abstract-with-azure-active-directory"></a>教程：将 Abstract 与 Azure Active Directory 集成

本教程介绍如何将 Abstract 与 Azure Active Directory (Azure AD) 集成。 将 Abstract 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Abstract。
* 让用户使用其 Azure AD 帐户自动登录到 Abstract。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Abstract 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Abstract 支持 SP 和 IDP 发起的 SSO

## <a name="adding-abstract-from-the-gallery"></a>从库中添加 Abstract

要配置 Abstract 与 Azure AD 的集成，需要从库中将 Abstract 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务。
1. 导航到“企业应用程序”，选择“所有应用程序”。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Abstract”。
1. 从结果面板中选择“Abstract”，然后添加该应用。 在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

使用名为 B.Simon 的测试用户配置和测试 Abstract 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 Abstract 相关用户之间建立链接关系。

若要配置和测试 Abstract 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
2. **[配置 Abstract SSO](#configure-abstract-sso)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. [创建 Abstract 测试用户](#create-abstract-test-user) - 在 Abstract 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
6. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“Abstract”应用程序集成页上，找到“管理”部分，选择“单一登录”。
1. 在“选择单一登录方法”页上选择“SAML”。
1. 在“设置 SAML 单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在基本 SAML 配置部分，应用程序在 **IDP** 发起的模块中进行了预配置，且已通过 Azure 预填充了必要的 URL。 用户需要单击“保存”按钮来保存配置。

1. 如果要在 SP 发起的模式下配置应用程序，请单击“设置其他 URL”，并执行以下步骤：

    在“登录 URL”文本框中，键入 URL：`https://app.abstract.com/signin`

4. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分中，单击“复制”按钮，以复制“应用联合元数据 URL”，并将它保存在计算机上。

    ![证书下载链接](common/copy-metadataurl.png)

### <a name="configure-abstract-sso"></a>配置 Abstract SSO

确保从 Azure 门户检索 `App Federation Metadata Url` 和 `Azure AD Identifier`，因为你需要这些来配置 Abstract 上的 SSO。

可以在“使用 SAML 设置单一登录”页上找到这些信息：

* `App Federation Metadata Url` 位于“SAML 签名证书”部分。
* `Azure AD Identifier` 位于“设置 Abstract”部分。


现在可以在 Abstract 上配置 SSO 了：

>[!Note]
>需要使用组织的管理员帐户进行身份验证，然后才能访问 Abstract 上的 SSO 设置。

1. 打开 [Abstract Web 应用](https://app.abstract.com/)。
2. 转到左侧栏中的“权限”页。
3. 在“配置 SSO”部分，输入**元数据 URL** 和**实体 ID**。
4. 输入你可能有的任何手动例外。 列在手动例外部分的电子邮件会绕过 SSO，能够使用电子邮件和密码登录。 
5. 单击“保存更改”。

>[!Note] 
>需使用手动例外列表中的主要电子邮件地址。 如果列出的电子邮件是用户的次要电子邮件，则 SSO 激活会失败。 如果发生这种情况，则会看到一条错误消息，其中包含失败帐户的主要电子邮件。 在验证你知道该用户后，向手动例外添加该主要电子邮件。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。
   1. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 B.Simon 访问 Abstract 的权限，允许她使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。
1. 在应用程序列表中，选择“Abstract”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。
1. 在“添加分配”对话框中，单击“分配”按钮。

### <a name="create-abstract-test-user"></a>创建 Abstract 测试用户

若要在 Abstract 上测试 SSO，请执行以下操作：

1. 打开 [Abstract Web 应用](https://app.abstract.com/)。
2. 转到左侧栏中的“权限”页。
3. 单击“使用我的帐户测试”。 如果测试失败，请[联系我们的支持团队](https://www.abstract.com/help/contact/)。

>[!Note]
>需要使用组织的管理员帐户进行身份验证，然后才能访问 Abstract 上的 SSO 设置。
需在 Azure 门户中将此组织管理员帐户分配给 Abstract。

### <a name="test-sso"></a>测试 SSO 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Abstract 磁贴时，应会自动登录到为其设置了 SSO 的 Abstract。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

