---
title: 教程：Azure Active Directory 与 Clarity 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Clarity 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/05/2020
ms.author: jeedes
ms.openlocfilehash: 969fb8c688fe26e1aa830cab2bbc35c5712b060e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88530692"
---
# <a name="tutorial-azure-active-directory-integration-with-clarity"></a>教程：Azure Active Directory 与 Clarity 集成

本教程介绍如何将 Clarity 与 Azure Active Directory (Azure AD) 集成。 将 Clarity 与 Azure AD 集成后，可以执行以下操作：

* 在 Azure AD 中控制谁有权访问 Clarity。
* 让用户使用其 Azure AD 帐户自动登录到 Clarity。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Clarity 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Clarity 支持 **IDP** 发起的 SSO
* 配置 Clarity 后，即可强制实施会话控制，从而实时保护组织的敏感数据以防外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-clarity-from-the-gallery"></a>从库中添加 Clarity

若要配置 Clarity 与 Azure AD 的集成，需要从库中将 Clarity 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务。
1. 导航到“企业应用程序”，选择“所有应用程序” 。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Clarity” 。
1. 从结果面板中选择“Clarity”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-clarity"></a>配置并测试 Clarity 的 Azure AD 单一登录

使用名为 **B.Simon** 的测试用户配置并测试 Clarity 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 Clarity 中的相关用户之间建立链接关系。

若要配置并测试 Clarity 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
2. **[配置 Clarity 单一登录](#configure-clarity-single-sign-on)** - 在应用程序端配置单一登录设置。
    1. **[创建 Clarity 测试用户](#create-clarity-test-user)** - 在 Clarity 中创建 B.Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“Clarity”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
2. 在“选择单一登录方法”对话框中，选择“SAML” 。
3.  在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置 。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“设置 SAML 单一登录”页上，执行以下步骤：

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://ca.ondemand.saml.20.post.<companyname>`

    b. 在“回复 URL”文本框中键入：`https://fedsso.ondemand.ca.com/affwebservices/public/saml2assertionconsumer`

    > [!NOTE]
    > 此值不是真实值。 请使用实际标识符更新此值。 请联系 [Clarity 客户端支持团队](mailto:catechnicalsupport@ca.com)获取此值。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

5. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上   。

    ![证书下载链接](common/certificatebase64.png)

6. 在“设置 Clarity”部分，根据要求复制相应 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”  。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com`。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。 
   1. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，将通过授予 B.Simon 访问 Clarity 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Clarity”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。  

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。  
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 
1. 在“添加分配”对话框中，单击“分配”按钮 

## <a name="configure-clarity-single-sign-on"></a>配置 Clarity 单一登录

若要在 **Clarity** 端配置单一登录，需要将下载的“证书(Base64)”以及从 Azure 门户复制的相应 URL 发送给 [Clarity 支持团队](mailto:catechnicalsupport@ca.com)。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-clarity-test-user"></a>创建 Clarity 测试用户

在本部分中，将在 Clarity 中创建名为 B.Simon 的用户。 请与  [Clarity 支持团队](mailto:catechnicalsupport@ca.com)协作，以将用户添加到 Clarity 平台。 使用单一登录前，必须先创建并激活用户。

## <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Clarity 磁贴时，应当会自动登录到为其设置了 SSO 的 Clarity。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Microsoft Cloud App Security 中的会话控制是什么？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [如何通过高级可见性和控制保护 Clarity](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
