---
title: 教程：Azure Active Directory 与 O.C. Tanner - AppreciateHub 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 O.C. Tanner - AppreciateHub 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dee8fbca-0b60-4a21-8917-1fb6919de5a0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/28/2020
ms.author: jeedes
ms.openlocfilehash: 768756e013a4301ea11f5f9478178bdad46f9d67
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2020
ms.locfileid: "76983990"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-oc-tanner---appreciatehub"></a>教程：Azure Active Directory 与 O.C. Tanner - AppreciateHub 的单一登录 (SSO) 集成

本教程介绍了如何将 O.C. Tanner - AppreciateHub 与 Azure Active Directory (Azure AD) 集成。 将 O.C. Tanner - AppreciateHub 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 O.C. Tanner - AppreciateHub。
* 让用户使用其 Azure AD 帐户自动登录到 O.C. Tanner - AppreciateHub。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅[什么是使用 Azure Active Directory 的应用程序访问和单一登录](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* O.C. 启用了单一登录 (SSO) 的 Tanner - AppreciateHub 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* O.C. Tanner - AppreciateHub 支持 **IDP** 发起的 SSO

* 配置 O.C. Tanner - AppreciateHub 后，就可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-oc-tanner---appreciatehub-from-the-gallery"></a>从库中添加 O.C. Tanner - AppreciateHub

若要配置 O.C. Tanner - AppreciateHub 与 Azure AD 的集成，需要从库中将 O.C. Tanner - AppreciateHub 添加到托管 SaaS 应用列表中。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”  部分中，在搜索框中键入“O.C.  Tanner - AppreciateHub”。
1. 从结果面板中选择“O.C.  Tanner - AppreciateHub”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-oc-tanner---appreciatehub"></a>配置并测试 O.C. Tanner - AppreciateHub 的 Azure AD 单一登录

使用名为 **B.Simon** 的测试用户配置并测试 O.C. Tanner - AppreciateHub 的 Azure AD SSO 若要正常使用 SSO，需要在 Azure AD 用户与 O.C. Tanner - AppreciateHub 中的相关用户之间建立链接关系。

若要配置并测试 O.C. Tanner - AppreciateHub 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    * **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    * **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 O.C.Tanner - AppreciateHub SSO](#configure-oc-tanner---appreciatehub-sso)** - 在应用程序端配置单一登录设置。
    * **[创建 O.C.Tanner - AppreciateHub 测试用户](#create-oc-tanner---appreciatehub-test-user)** - 在 O.C. Tanner - AppreciateHub 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中，在 **O.C.Tanner - AppreciateHub** 应用程序集成页上，找到“管理”部分并选择“单一登录”。  
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分中，用户不必执行任何步骤，因为该应用已经与 Azure 预先集成  。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

1. 在“设置 O.C.  Tanner - AppreciateHub”部分中，根据你的需要复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分中，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”    。
1. 选择屏幕顶部的“新建用户”  。
1. 在“用户”属性中执行以下步骤  ：
   1. 在“名称”  字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension  。 例如，`B.Simon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。  
   1. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，将通过授予 B.Simon 访问 O.C. Tanner - AppreciateHub 的权限， 允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“O.C.  Tanner - AppreciateHub”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-oc-tanner---appreciatehub-sso"></a>配置 O.C. Tanner - AppreciateHub SSO

要在 **O.C.Tanner - AppreciateHub** 端配置单一登录，需要将下载的**应用联合元数据 XML** 以及从 Azure 门户复制的相应 URL 发送给 [O.C.Tanner - AppreciateHub 支持团队](mailto:sso@octanner.com)。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-oc-tanner---appreciatehub-test-user"></a>创建 O.C. Tanner - AppreciateHub 测试用户

本部分的目的是在 O.C. Tanner - AppreciateHub 中创建名为“Britta Simon”的用户。

**若要在 O.C.Tanner - AppreciateHub 中创建名为“Britta Simon”的用户，请执行以下步骤：**

要求 [O.C. Tanner - AppreciateHub 支持团队](mailto:sso@octanner.com)创建一个用户，该用户的 nameID 属性值与 Azure AD 中 Britta Simon 的用户名相同。

## <a name="test-sso"></a>测试 SSO

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“O.C. Tanner - AppreciateHub”磁贴时，应会自动登录到设置了 SSO 的 O.C. Tanner - AppreciateHub。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [尝试通过 Azure AD 使用 O.C. Tanner - AppreciateHub](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security 中的会话控制是什么？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [如何通过高级可见性和控制保护 O.C. Tanner - AppreciateHub](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)