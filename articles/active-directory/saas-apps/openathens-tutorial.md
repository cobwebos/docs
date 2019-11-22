---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 OpenAthens 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 OpenAthens 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dd4adfc7-e238-41d5-8b25-1811f08078b6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43fc2272a81672ea613bdcbe17c5381e99cafbff
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053202"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-openathens"></a>教程：Azure Active Directory 单一登录 (SSO) 与 OpenAthens 的集成

在本教程介绍如何将 OpenAthens 与 Azure Active Directory (Azure AD) 集成。 若要将 OpenAthens 与 Azure AD 集成，可以：

* 在 Azure AD 中控制谁有权访问 OpenAthens。
* 让用户使用其 Azure AD 帐户自动登录到 OpenAthens。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用单一登录 (SSO) 的 OpenAthens 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* OpenAthens 支持 **IDP** 发起的 SSO
* OpenAthens 支持**实时**用户预配

## <a name="adding-openathens-from-the-gallery"></a>从库中添加 OpenAthens

若要配置 OpenAthens 与 Azure AD 的集成，需要从库中将 OpenAthens 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“OpenAthens”   。
1. 从结果面板中选择“OpenAthens”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-openathens"></a>配置并测试 OpenAthens 的 Azure AD 单一登录

使用名为 B.Simon 的测试用户来配置并测试 OpenAthens 的 Azure AD SSO  。 若要运行 SSO，需要在 Azure AD 用户与 OpenAthens 中相关用户之间建立链接关系。

若要配置和测试 OpenAthens 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    * **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    * **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 OpenAthens SSO](#configure-openathens-sso) - 在应用程序端配置单一登录设置  。
    * [创建 OpenAthens 测试用户](#create-openathens-test-user) - 在 OpenAthens 中创建 B Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式  。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“OpenAthens”应用程序集成页上，找到“管理”部分，选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”  部分中，上传**服务提供程序元数据文件**，本教程下文中介绍了相关的步骤。

    a. 单击“上传元数据文件”  。

    ![Openathens 上传元数据](common/upload-metadata.png)

    b. 单击“文件夹徽标”  来选择元数据文件并单击“上传”。 

    ![Openathens 浏览上传元数据](common/browse-upload-metadata.png)

    c. 成功上传元数据文件后，**标识符**值会自动填充在“基本 SAML 配置”  部分的文本框中：

    ![OpenAthens 域和 URL 单一登录信息](common/idp-identifier.png)

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

1. 在“设置 OpenAthens”部分，根据要求复制相应的 URL  。

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

在本部分，你将通过授予 B.Simon 访问 OpenAthens 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“OpenAthens”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-openathens-sso"></a>配置 OpenAthens SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录 OpenAthens 公司站点。

1. 在“管理”  选项卡下的列表中选择“连接”  。

    ![配置单一登录](./media/openathens-tutorial/tutorial_openathens_application1.png)

1. 选择“SAML 1.1/2.0”，再选择“配置”按钮。  

    ![配置单一登录](./media/openathens-tutorial/tutorial_openathens_application2.png)

1. 若要添加配置，请选择“浏览”按钮上传已从 Azure 门户下载的元数据 .xml 文件，再选择“添加”。  

    ![配置单一登录](./media/openathens-tutorial/tutorial_openathens_application3.png)

1. 在“详细信息”  选项卡下执行以下步骤。

    ![配置单一登录](./media/openathens-tutorial/tutorial_openathens_application4.png)

    a. 在“显示名称映射”  中，选择“使用属性”  。

    b. 在“显示名称属性”  文本框中，输入值 `http://schema.microsoft.com/identity/claims/displayname`。

    c. 在“唯一用户映射”  中，选择“使用属性”  。

    d. 在“唯一用户属性”  文本框中，输入值 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`。

    e. 在“状态”  中，选中所有三个复选框。

    f. 在“创建本地帐户”  中，选择“自动”  。

    g. 选择“保存更改”。 

    h. 在“信赖方”  选项卡上，复制**元数据 URL** 并在浏览器中将其打开以下载 **SP 元数据 XML** 文件。 在 Azure AD 中的“基本 SAML 配置”  部分中上传此 SP 元数据文件。

    ![配置单一登录](./media/openathens-tutorial/tutorial_openathens_application5.png)

### <a name="create-openathens-test-user"></a>创建 OpenAthens 测试用户

在本部分中，我们将在 OpenAthens 中创建一个名为 Britta Simon 的用户。 OpenAthens 支持默认情况下启用的**实时用户预配**。 此部分不存在任何操作项。 如果 OpenAthens 中尚不存在用户，则会在身份验证后创建一个新用户。

## <a name="test-sso"></a>测试 SSO

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 OpenAthens 磁贴时，应当会自动登录到你为其设置了 SSO 的 OpenAthens。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [使用 Azure AD 试用 OpenAthens](https://aad.portal.azure.com/)