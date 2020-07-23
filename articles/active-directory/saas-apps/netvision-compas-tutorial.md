---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Netvision Compas 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Netvision Compas 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a56f44c5-dc08-4c7c-ad20-b6e7127deb2c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3015ea26d81505c4f058846dbcb3b7858f79267
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520105"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netvision-compas"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Netvision Compas 的集成

本教程介绍如何将 Netvision Compas 与 Azure Active Directory (Azure AD) 集成。 将 Netvision Compas 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Netvision Compas。
* 让用户使用其 Azure AD 帐户自动登录到 Netvision Compas。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Netvision Compas 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Netvision Compas 支持 **SP 和 IDP** 发起的 SSO
* 配置 Netvision Compas 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制扩展自条件访问。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)


## <a name="adding-netvision-compas-from-the-gallery"></a>从库中添加 Netvision Compas

若要配置 Netvision Compas 与 Azure AD 的集成，需要从库中将 Netvision Compas 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 **Netvision Compas**。 
1. 在结果面板中选择“Netvision Compas”，然后添加该应用。  在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-single-sign-on-for-netvision-compas"></a>配置并测试 Netvision Compas 的 Azure AD 单一登录

使用名为 **B.Simon** 的测试用户配置并测试 Netvision Compas 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Netvision Compas 中的相关用户之间建立链接关系。

若要配置并测试 Netvision Compas 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Netvision Compas SSO](#configure-netvision-compas-sso)** - 在应用程序端配置单一登录设置。
    1. **[配置 Netvision Compas 测试用户](#configure-netvision-compas-test-user)** - 在 Netvision Compas 中创建 B.Simon 的对应用户，并将其关联到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中的“Netvision Compas”应用程序集成页上，找到“管理”部分并选择“单一登录”。   
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在“IDP”发起的模式下配置应用程序，请在“基本 SAML 配置”部分中输入以下字段的值   ：

    a. 在“标识符”  文本框中，使用以下模式键入 URL：`https://<TENANT>.compas.cloud/Identity/Saml20`

    b. 在“回复 URL”  文本框中，使用以下模式键入 URL：`https://<TENANT>.compas.cloud/Identity/Auth/AssertionConsumerService`

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<TENANT>.compas.cloud/Identity/Auth/AssertionConsumerService`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 请联系 [Netvision Compas 客户端支持团队](mailto:contact@net.vision)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该元数据文件并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)



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

在本部分，你将通过授予 B.Simon 访问 Netvision Compas 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在“应用程序”列表中选择“Netvision Compas”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-netvision-compas-sso"></a>配置 Netvision Compas SSO

在本部分，你将在 Netvision Compas中启用 SAML SSO。 
1. 使用管理帐户登录到 Netvision Compas，并访问管理区域。 

    ![管理区域](media/netvision-compas-tutorial/admin.png)

1. 找到“系统”区域，并选择“标识提供者”。  

    ![管理 IDP](media/netvision-compas-tutorial/admin-idps.png)

1. 选择“添加”操作，将 Azure AD 注册为新的 IDP。 

    ![添加 IDP](media/netvision-compas-tutorial/idps-add.png)

1. 选择“SAML”作为“提供者类型”。  
1. 为“显示名称”和“说明”字段输入有意义的值。  
1. 将 Netvision Compas 用户分配到 IDP，方法是：在“可用用户”列表中选择用户，然后选择“添加所选项”按钮。    还可以遵循以下预配过程将用户分配到 IDP。
1. 对于“元数据”SAML 选项，请单击“选择文件”按钮，然后选择以前保存在计算机上的元数据文件。  
1. 单击“ **保存**”。

    ![编辑 IDP](media/netvision-compas-tutorial/idp-edit.png)


### <a name="configure-netvision-compas-test-user"></a>配置 Netvision Compas 测试用户

在本部分，你要将 Netvision Compas 中的某个现有用户配置为使用 Azure AD 来实现 SSO。 
1. 按公司定义的 Netvision Compas 用户预配过程操作，或编辑现有的用户帐户。 
1. 定义用户的个人资料时，请确保该用户的“电子邮件(个人)”地址与 Azure AD 用户名 username@companydomain.extension 相匹配。  例如，`B.Simon@contoso.com` 。

    ![编辑用户](media/netvision-compas-tutorial/user-config.png)

使用单一登录前，必须先创建并激活用户。

## <a name="test-sso"></a>测试 SSO 

在本部分中，测试 Azure AD 单一登录配置。

### <a name="using-the-access-panel-idp-initiated"></a>使用访问面板（IDP 发起模式）。

在访问面板中单击“Netvision Compas”磁贴时，应会自动登录到设置了 SSO 的 Netvision Compas。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

### <a name="directly-accessing-netvision-compas-sp-initiated"></a>直接访问 Netvision Compas（SP 发起模式）。

1. 访问 Netvision Compas URL。  例如，`https://tenant.compas.cloud` 。
1. 输入 Netvision Compas 用户名，然后选择“下一步”。  

    ![将用户登录](media/netvision-compas-tutorial/login-user.png)

1. （可选）如果在 Netvision Compas中为该用户分配了多个 IDP，则会显示可用 IDP 的列表。   选择以前在 Netvision Compas 中配置的 Azure AD IDP。 

    ![登录名选择](media/netvision-compas-tutorial/login-choose.png)

1. 你将重定向到 Azure AD 来执行身份验证。 成功通过身份验证后，你会自动登录到为其设置了 SSO 的 Netvision Compas。 

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [在 Azure AD 中试用 Netvision Compas](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security 中的会话控制是什么？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
