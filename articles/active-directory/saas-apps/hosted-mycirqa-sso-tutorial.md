---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Hosted MyCirqa SSO 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Hosted MyCirqa SSO 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4337e343-180d-475d-83ac-6f5b5f133dfe
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/26/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: adc6cd36190ca3847fdc9e3f6935aed5c78e0942
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2020
ms.locfileid: "75662437"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-hosted-mycirqa-sso"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Hosted MyCirqa SSO 集成

本教程介绍了如何将 Hosted MyCirqa SSO 与 Azure Active Directory (Azure AD) 进行集成。 将 Hosted MyCirqa SSO 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Hosted MyCirqa SSO。
* 让用户使用其 Azure AD 帐户自动登录到 Hosted MyCirqa SSO。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了 Hosted MyCirqa SSO 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Hosted MyCirqa SSO 支持 SP 发起的 SSO 

## <a name="adding-hosted-mycirqa-sso-from-the-gallery"></a>从库中添加 Hosted MyCirqa SSO

要配置 Hosted MyCirqa SSO 与 Azure AD 的集成，需要从库中将 Hosted MyCirqa SSO 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 Hosted MyCirqa SSO   。
1. 从结果面板中选择 Hosted MyCirqa SSO，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-hosted-mycirqa-sso"></a>配置和测试 Hosted MyCirqa SSO 的 Azure AD 单一登录

使用名为 B.Simon 的测试用户配置和测试 Hosted MyCirqa SSO 的 Azure AD SSO  。 若要运行 SSO，需要在 Azure AD 用户与 Hosted MyCirqa SSO 相关用户之间建立链接关系。

若要配置和测试 Hosted MyCirqa SSO 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    * **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    * **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Hosted MyCirqa SSO](#configure-hosted-mycirqa-sso)** - 在应用程序端配置单一登录设置。
    * **[创建 Hosted MyCirqa SSO 测试用户](#create-hosted-mycirqa-sso-test-user)** - 在 Hosted MyCirqa SSO 中创建 B.Simon 的对应用户，并将其链接到她的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的 Hosted MyCirqa SSO 应用程序集成页上，找到“管理”部分，选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值  ：

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.cirqahosting.com/CirqaIdentity/external?` 

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://isoxford.com/<CUSTOMID>/cirqaidentity/saml2` 

    > [!NOTE]
    > 这些不是实际值。 使用实际登录 URL 和标识符更新这些值。 请联系 [Hosted MyCirqa SSO 客户端支持团队](not sure)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. 在“使用 SAML 设置单一登录”  页的“SAML 签名证书”  部分中，单击“复制”按钮，以复制“应用联合元数据 URL”  ，并将它保存在计算机上。

    ![证书下载链接](common/copy-metadataurl.png)

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

在本部分中，通过授予 B.Simon 访问 Hosted MyCirqa SSO 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择 Hosted MyCirqa SSO  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-hosted-mycirqa-sso"></a>配置 Hosted MyCirqa SSO

若要在 Hosted MyCirqa SSO 端配置单一登录，需要将应用联合元数据 URL 发送给 [Hosted MyCirqa SSO 支持团队](mailto:support@isoxford.com)   。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

## <a name="create-hosted-mycirqa-sso-test-user"></a>创建 Hosted MyCirqa SSO 测试用户

在本部分中，你将在 Hosted MyCirqa SSO 中创建一个名为 Britta Simon 的用户。 与 [Hosted MyCirqa SSO 支持团队](mailto:support@isoxford.com)协作，在 Hosted MyCirqa SSO 平台中添加用户。 使用单一登录前，必须先创建并激活用户。

## <a name="test-sso"></a>测试 SSO

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Hosted MyCirqa SSO 磁贴时，应当会自动登录到为其设置了 SSO 的 Hosted MyCirqa SSO。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [通过 Azure AD 试用 Hosted MyCirqa SSO](https://aad.portal.azure.com/)