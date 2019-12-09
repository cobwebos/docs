---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Claromentis 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Claromentis 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7980e0c5-e4d8-4678-afa2-7ec219432114
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c6eaf23950f83592a33709574be464f4499f0ab
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2019
ms.locfileid: "74823029"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-claromentis"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Claromentis 的集成

本教程介绍如何将 Claromentis 与 Azure Active Directory (Azure AD) 集成。 将 Claromentis 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Claromentis。
* 让用户使用其 Azure AD 帐户自动登录到 Claromentis。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Claromentis 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Claromentis 支持 **SP 和 IDP** 发起的 SSO
* Claromentis 支持**实时**用户预配

## <a name="adding-claromentis-from-the-gallery"></a>从库中添加 Claromentis

若要配置 Claromentis 与 Azure AD 的集成，需要从库中将 Claromentis 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 **Claromentis**。 
1. 在结果面板中选择“Claromentis”，然后添加该应用。  在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-claromentis"></a>配置并测试 Claromentis 的 Azure AD 单一登录

使用名为 **B.Simon** 的测试用户配置并测试 Claromentis 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Claromentis 中的相关用户之间建立链接关系。

若要配置并测试 Claromentis 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    * **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    * **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Claromentis SSO](#configure-claromentis-sso)** - 在应用程序端配置单一登录设置。
    * **[创建 Claromentis 测试用户](#create-claromentis-test-user)** - 在 Claromentis 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中的“Claromentis”应用程序集成页上，找到“管理”部分并选择“单一登录”。   
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在“IDP”发起的模式下配置应用程序，请在“基本 SAML 配置”部分中输入以下字段的值   ：

    a. 在“标识符”文本框中，根据组织的要求输入标识符值。 

    b. 在“回复 URL”  文本框中，使用以下模式键入 URL：`https://<customer_site_url>/custom/loginhandler/simplesaml/www/module.php/saml/sp/saml2-acs.php/claromentis`

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”文本框中，使用以下模式键入 URL： 

    | | |
    |-|-|
    | `https://<customer_site_url>/login`|
    | `https://<customer_site_url>/login?no_auto=0`|

    > [!NOTE]
    > 这些不是实际值。 本教程稍后将介绍如何使用实际的登录 URL 和标识符来更新这些值。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

1. 在“设置 Claromentis”部分，根据要求复制相应的 URL。 

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

在本部分，你将通过授予 B.Simon 访问 Claromentis 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在“应用程序”列表中选择“Claromentis”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-claromentis-sso"></a>配置 Claromentis SSO

1. 在另一个浏览器窗口中，以管理员身份登录到 Claromentis 网站。

1. 单击**应用程序图标**并选择“管理”。 

    ![Claromentis 配置](./media/claromentis-tutorial/config1.png)

1. 选择“自定义登录处理程序”选项卡。 

    ![Claromentis 配置](./media/claromentis-tutorial/config2.png)

1. 选择“SAML 配置”。 

    ![Claromentis 配置](./media/claromentis-tutorial/config3.png)

1. 在“SAML 配置”选项卡上，向下滚动到“配置”部分，然后执行以下步骤：  

    ![Claromentis 配置](./media/claromentis-tutorial/config4.png)

    a. 在“技术联系人姓名”文本框中，输入技术联系人的姓名。 

    b. 在“技术联系人电子邮件”文本框中，输入技术联系人的电子邮件地址。 

    c. 在“身份验证管理密码”文本框中提供密码。 

1. 向下滚动到“身份验证源”并执行以下步骤： 

    ![Claromentis 配置](./media/claromentis-tutorial/config5.png)

    a. 在“IDP”文本框中，输入从 Azure 门户复制的“Azure AD 标识符”值。  

    b. 在“实体 ID”文本框中输入实体 ID 值。 

    c. 上传从 Azure 门户下载的“联合身份验证元数据 XML”文件。 

    d. 单击“ **保存**”。

1. 现在你将发现，所有 URL 已填充到“SAML 配置”页的“标识提供者”部分。  

    ![Claromentis 配置](./media/claromentis-tutorial/config6.png)

    a. 复制“标识符(实体 ID)”值，并将其粘贴到 Azure 门户上“基本 SAML 配置”部分的“标识符”文本框中。   

    b. 复制“回复 URL”值，并将其粘贴到 Azure 门户上“基本 SAML 配置”部分的“回复 URL”文本框中。   

    c. 复制“登录 URL”值，并将其粘贴到 Azure 门户上“基本 SAML 配置”部分的“登录 URL”文本框中。   

### <a name="create-claromentis-test-user"></a>创建 Claromentis 测试用户

在本部分，我们将在 Claromentis 中创建名为 B.Simon 的用户。 Claromentis 支持默认已启用的实时用户预配。 此部分不存在任何操作项。 如果 Claromentis 中尚不存在用户，身份验证后会创建一个新用户。

## <a name="test-sso"></a>测试 SSO

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“Claromentis”磁贴时，应会自动登录到设置了 SSO 的 Claromentis。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [在 Azure AD 中试用 Claromentis](https://aad.portal.azure.com/)