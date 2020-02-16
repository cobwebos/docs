---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Azure AD SAML Toolkit 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Azure AD SAML Toolkit 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3f4348e7-c34e-43c7-926e-f1b26ffacf6d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7902112c1694bacfeb45b5f20db80d5136642169
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2020
ms.locfileid: "77047952"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-azure-ad-saml-toolkit"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Azure AD SAML Toolkit 集成

本教程介绍如何将 Azure AD SAML Toolkit 与 Azure Active Directory (Azure AD) 集成。 将 Azure AD SAML Toolkit 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Azure AD SAML Toolkit。
* 可让用户使用其 Azure AD 帐户自动登录到 Azure AD SAML Toolkit。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Azure AD SAML Toolkit 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Azure AD SAML Toolkit 支持 **SP** 发起的 SSO
* 配置 Azure AD SAML Toolkit 后，就可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-azure-ad-saml-toolkit-from-the-gallery"></a>从库中添加 Azure AD SAML Toolkit

若要配置 Azure AD SAML Toolkit 与 Azure AD 的集成，需要从库中将 Azure AD SAML Toolkit 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 **Azure AD SAML Toolkit**  。
1. 从结果面板中选择“Azure AD SAML Toolkit”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-azure-ad-saml-toolkit"></a>配置和测试 Azure AD SAML Toolkit 的 Azure AD 单一登录

使用名为 **B. Simon** 的测试用户配置并测试 Azure AD SAML Toolkit 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Azure AD SAML Toolkit 中的相关用户之间建立链接关系。

若要配置并测试 Azure AD SAML Toolkit 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Azure AD SAML Toolkit SSO](#configure-azure-ad-saml-toolkit-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Azure AD SAML Toolkit 测试用户](#create-azure-ad-saml-toolkit-test-user)** - 在 Azure AD SAML Toolkit 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“Azure AD SAML Toolkit”应用程序集成页上，找到“管理”部分并选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”页上，输入以下字段的值  ：

    a. 在“登录 URL”文本框中，键入 URL：`https://samltoolkit.azurewebsites.net/` 

    b. 在“标识符(实体 ID)”文本框中，键入 URL：`https://samltoolkit.azurewebsites.net` 

    c. 在“回复 URL”文本框中键入 URL：`https://samltoolkit.azurewebsites.net/SAML/Consume` 

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(原始)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificateraw.png)

1. 在“设置 Azure AD SAML Toolkit”部分，根据要求复制相应的 URL。 

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

在本部分，我们通过授予 B.Simon 访问 Azure AD SAML Toolkit 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Azure AD SAML Toolkit”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-azure-ad-saml-toolkit-sso"></a>配置 Azure AD SAML Toolkit SSO

1. 打开新的 Web 浏览器窗口，如果你尚未在 Azure AD SAML Toolkit 网站中注册，请先单击“注册”  。 如果已注册，请使用注册的凭据登录到 Azure AD SAML Toolkit 公司站点。

    ![Azure AD SAML Toolkit 注册](./media/saml-toolkit-tutorial/register.png)

1. 单击“SAML 配置”  。

    ![Azure AD SAML Toolkit - SAML 配置](./media/saml-toolkit-tutorial/saml-configure.png)

1. 单击“创建”。 

    ![Azure AD SAML Toolkit - 创建 SSO](./media/saml-toolkit-tutorial/createsso.png)

1. 在“SAML SSO 配置”  页上，执行以下步骤：

    ![Azure AD SAML Toolkit - 创建 SSO](./media/saml-toolkit-tutorial/fill-details.png)

    1. 在“登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。

    1. 在“Azure AD 标识符”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值   。

    1. 在“注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值   。

    1. 单击“选择文件”  并上传从 Azure 门户下载的“证书(原始)”  文件。

    1. 单击“创建”。 

### <a name="create-azure-ad-saml-toolkit-test-user"></a>创建 Azure AD SAML Toolkit 测试用户

在本部分，我们将在 Azure AD SAML Toolkit 中创建名为 B.Simon 的用户。 Azure AD SAML Toolkit 支持默认已启用的实时用户预配。 此部分不存在任何操作项。 如果 Azure AD SAML Toolkit 中尚不存在用户，身份验证后会创建一个新用户。

## <a name="test-sso"></a>测试 SSO 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Azure AD SAML Toolkit 磁贴时，应会自动登录到设置了 SSO 的 Azure AD SAML Toolkit。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [通过 Azure AD 试用 Azure AD SAML Toolkit](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security 中的会话控制是什么？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [如何通过高级可见性和控制保护 Azure AD SAML Toolkit](https://docs.microsoft.com/cloud-app-security/protect-azure)