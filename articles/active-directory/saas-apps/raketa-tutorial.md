---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Raketa 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Raketa 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8b29569a-2678-4015-96fc-388944969b72
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 07/28/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: df9c55971d05efcdbf1300a8023285bd0c8f3e4f
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/05/2020
ms.locfileid: "87796838"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-raketa"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Raketa 的集成

本教程介绍如何将 Raketa 与 Azure Active Directory (Azure AD) 集成。 将 Raketa 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Raketa。
* 让用户使用其 Azure AD 帐户自动登录 Raketa。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用单一登录 (SSO) 的 Raketa 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Raketa 支持 SP 发起的 SSO。
* 配置 Raketa 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-raketa-from-the-gallery"></a>从库中添加 Raketa

要配置 Raketa 与 Azure AD 的集成，需要从库中将 Raketa 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务 [1]。

    ![rkt_1](./media/raketa-tutorial/azure-active-directory.png)

1. 导航到“企业应用程序” [2]，选择“所有应用程序” [3]。

1. 若要添加新的应用程序，请选择“新建应用程序”[4]。 

    ![rkt_2](./media/raketa-tutorial/new-app.png)

1. 在“从库中添加”部分的搜索框 [6] 中，键入“Raketa”[5] 。

1. 在结果面板 [7] 中选择“Raketa”，然后单击“添加”按钮 [8]。 

    ![rkt_3](./media/raketa-tutorial/add-btn.png)


## <a name="configure-and-test-azure-ad-single-sign-on-for-raketa"></a>配置和测试 Raketa 的 Azure AD 单一登录

使用名为 B.Simon 的测试用户配置和测试 Raketa 的 Azure AD SSO。 要使 SSO 正常工作，需要在 Azure AD 用户与 Raketa 中的相关用户之间建立链接关系。

要配置和测试 Raketa 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Raketa SSO](#configure-raketa-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Raketa 测试用户](#create-raketa-test-user)** - 在 Raketa 中创建 B.Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“Raketa”应用程序集成页上，找到“管理”部分，选择“单一登录”[9]  。

    ![rkt_4](./media/raketa-tutorial/manage-sso.png)

1. 在“选择单一登录方法”页 [9] 上选择“SAML”[10]。 

    ![rkt_5](./media/raketa-tutorial/saml.png)

1. 在“设置 SAML 单一登录”页上，单击“基本 SAML 配置”[11] 旁边的编辑/铅笔图标以编辑设置。 

1. 在“基本 SAML 配置”部分，输入以下字段的值：

    1. 在“标识符(实体 ID)”[12] 和“登录 URL”[14] 文本框中，键入 URL：`https://raketa.travel/`。

    1. 在“回复 URL”文本框 [13] 中，使用以下模式键入 URL：`https://raketa.travel/sso/acs?clientId=<CLIENT_ID>`。  

    ![rkt_6](./media/raketa-tutorial/enter-urls.png)

    > [!NOTE]
    > 答复 URL 值不是真实值。 请使用实际回复 URL 更新此值。 请联系 [Raketa 客户端支持团队](mailto:help@raketa.travel)获取该值。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

1. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分，找到“证书(Base64)”，选择“下载”[15] 以下载该证书并将其保存到计算机上。   

1. 在“设置 Raketa”部分中，根据要求复制相应的 URL。

    1. 登录 URL [16] – 用于将用户重定向到身份验证系统的授权网页 URL。

    1. Azure AD 标识符 [17] – Azure AD 标识符。

    1. 注销 URL [18] – 用于在注销之后重定向用户的网页 URL。

    ![rkt_7](./media/raketa-tutorial/copy-urls.png)


### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”[1]、“用户”[19]和“所有用户”[20]  。

1. 选择屏幕顶部的“新建用户”[21]。

    ![rkt_8](./media/raketa-tutorial/new-user.png)

1. 在“用户”属性中执行以下步骤：

   1. 在“用户名”字段 [22] 中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com`。

   1. 在“名称”字段 [23] 中，输入 `B.Simon`。

   1. 选中“显示密码”复选框 [25]，然后记下“密码”框 [24] 中显示的值。 

   1. 单击“创建”[26]。 

    ![rkt_9](./media/raketa-tutorial/create-user.png)


### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，将通过授予 B.Simon 访问 Raketa 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”[2]、“所有应用程序”[3]。 

1. 在应用程序列表中，选择“Raketa”[27]。  

    ![rkt_10](./media/raketa-tutorial/add-raketa.png)

1. 在应用的概述页中找到“管理”部分，然后选择“用户和组”[28] 。 

    ![rkt_11](./media/raketa-tutorial/users-groups.png)

1. 选择“添加用户”[29]，然后在“添加分配”对话框中选择“用户和组”[30]。  

    ![rkt_12](./media/raketa-tutorial/add-user-raketa.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”[31]，然后单击屏幕底部的“选择”[32] 按钮。  

1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。

1. 在“添加分配”对话框中，单击“分配”按钮 [33] 。 

    ![rkt_13](./media/raketa-tutorial/assign-user.png)

## <a name="configure-raketa-sso"></a>配置 Raketa SSO

要在 Raketa 端配置单一登录，需要将下载的“证书(Base64)”以及从 Azure 门户复制的相应 URL 发送给 [Raketa 支持团队](mailto:help@raketa.travel) 。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-raketa-test-user"></a>创建 Raketa 测试用户

在本部分，将在 Raketa 中创建名为 B.Simon 的用户。 与 [Raketa 支持团队](mailto:help@raketa.travel)协作，将用户添加到 Raketa 平台中。 使用单一登录前，必须先创建并激活用户。

## <a name="test-sso"></a>测试 SSO

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Raketa 磁贴时，应会自动登录到为其设置了 SSO 的 Raketa。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [通过 Azure AD 试用 Raketa](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security 中的会话控制是什么？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [如何通过高级可见性和控制保护 Raketa](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)