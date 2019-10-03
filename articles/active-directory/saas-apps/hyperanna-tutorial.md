---
title: 教程：Azure Active Directory 与 HyperAnna 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 HyperAnna 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3e47846e-eddd-4116-aa51-6b4bb772d2ac
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed5370fda9be2573f773bd2fe653feb74defeb7d
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2019
ms.locfileid: "68248866"
---
# <a name="tutorial-integrate-hyperanna-with-azure-active-directory"></a>教程：将 HyperAnna 与 Azure Active Directory 集成

本教程介绍如何将 HyperAnna 与 Azure Active Directory (Azure AD) 集成。 将 HyperAnna 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 HyperAnna。
* 让用户使用其 Azure AD 帐户自动登录到 HyperAnna。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 HyperAnna 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* HyperAnna 支持 SP 和 IDP 发起的 SSO

## <a name="adding-hyperanna-from-the-gallery"></a>从库中添加 HyperAnna

要配置 HyperAnna 与 Azure AD 的集成，需要从库中将 HyperAnna 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务。
1. 导航到“企业应用程序”，选择“所有应用程序”。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入 **HyperAnna**。
1. 在结果面板中选择“HyperAnna”，然后添加该应用。 在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

使用名为 **B.Simon** 的测试用户配置和测试 HyperAnna 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 HyperAnna 中的相关用户之间建立链接关系。

若要配置并测试 HyperAnna 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
2. **[配置 HyperAnna SSO](#configure-hyperanna-sso)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. [创建 HyperAnna 测试用户](#create-hyperanna-test-user) - 在 HyperAnna 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
6. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“HyperAnna”应用程序集成页上，找到“管理”部分，选择“单一登录”。
1. 在“选择单一登录方法”页上选择“SAML”。
1. 在“设置 SAML 单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在“IDP”发起的模式下配置应用程序，请在“基本 SAML 配置”部分中输入以下字段的值：

    在“回复 URL”文本框中，使用以下模式之一键入 URL：

    | |
    |--|
    | `https://microsoft.hyperanna.com/userservice/auth/saml` |
    | `https://anna.hyperanna.com/userservice/auth/saml` |

1. 如果要在 SP 发起的模式下配置应用程序，请单击“设置其他 URL”，并执行以下步骤：

    在“登录 URL”文本框中，使用以下模式键入一个 URL：

    | |
    |--|
    | `https://microsoft.hyperanna.com/` |
    | `https://anna.hyperanna.com/` |

4. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上。

    ![证书下载链接](common/certificatebase64.png)

6. 在“设置 HyperAnna”部分中，根据需要复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="configure-hyperanna-sso"></a>配置 HyperAnna SSO

若要在“HyperAnna”端配置单一登录，需要将下载的“证书 (Base64)”以及从 Azure 门户复制的相应 URL 发送给 [HyperAnna 支持团队](mailto:support@hyperanna.com)。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。
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

在本部分中，将通过授予 B.Simon 访问 HyperAnna 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。
1. 在应用程序列表中，选择“HyperAnna”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。
1. 在“添加分配”对话框中，单击“分配”按钮。

### <a name="create-hyperanna-test-user"></a>创建 HyperAnna 测试用户

本部分需在 HyperAnna 中创建名为“Britta Simon”的用户。 请与  [HyperAnna 支持团队](mailto:support@hyperanna.com)协作，将用户添加到 HyperAnna 平台中。 使用单一登录前，必须先创建并激活用户。

### <a name="test-sso"></a>测试 SSO 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的HyperAnna 磁贴时，应会自动登录到为其设置了 SSO 的 HyperAnna。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

