---
title: 教程：Azure Active Directory 与 Replicon 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Replicon 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 02a62f15-917c-417c-8d80-fe685e3fd601
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b27615b0c76b5c23bbc79788431b0e909b8bf22a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67092764"
---
# <a name="tutorial-integrate-replicon-with-azure-active-directory"></a>教程：将 Replicon 与 Azure Active Directory 集成

本教程介绍如何将 Replicon 与 Azure Active Directory (Azure AD) 集成。 将 Replicon 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Replicon。
* 让用户使用其 Azure AD 帐户自动登录到 Replicon。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以从[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月免费试用版。
* 已启用 Replicon (SSO) 单一登录的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。 Replicon 支持 **SP** 发起的 SSO。

## <a name="adding-replicon-from-the-gallery"></a>从库中添加 Replicon

要配置 Replicon 与 Azure AD 的集成，需要从库中将 Replicon 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Replicon”   。
1. 从结果面板中选择“Replicon”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

使用名为 B.Simon 的测试用户配置和测试 Replicon 的 Azure AD SSO  。 若要运行 SSO，需要在 Azure AD 用户与 Replicon 相关用户之间建立链接关系。

若要配置和测试 Replicon 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
2. **[配置 Replicon SSO](#configure-replicon-sso)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B.Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B.Simon 能够使用 Azure AD 单一登录。
5. **[创建 Replicon 测试用户](#create-replicon-test-user)** - 在 Replicon 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
6. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“Replicon”应用程序集成页上，找到“管理”部分，选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”页上，输入以下字段的值  ：

    1. 在“登录 URL”  文本框中，使用以下模式键入 URL：`https://global.replicon.com/!/saml2/<client name>/sp-sso/post`。

    1. 在“标识符”框中，使用以下模式键入 URL：`https://global.replicon.com/!/saml2/<client name>` 

    1. 在“回复 URL”  文本框中，使用以下模式键入 URL：`https://global.replicon.com/!/saml2/<client name>/sso/post`

    > [!NOTE]
    > 这些不是实际值。 请使用实际登录 URL、标识符和回复 URL 更新这些值。 请联系 [Replicon 客户端支持团队](https://www.replicon.com/customerzone/contact-support)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. 单击“SAML 签名证书”  的编辑/笔形图标以编辑设置。

    ![签名算法](common/signing-algorithm.png)

    1. 选择“签名 SAML 断言”  作为“签名选项”  。

    1. 选择 **SHA-256** 作为“签名算法”  。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

   ![证书下载链接](common/metadataxml.png)

### <a name="configure-replicon-sso"></a>配置 Replicon SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录 Replicon 公司网站。

2. 若要配置 SAML 2.0，请执行以下步骤：

    ![启用 SAML 身份验证](./media/replicon-tutorial/ic777805.png "启用 SAML 身份验证")

    a. 要显示 **EnableSAML Authentication2** 对话框，请将以下内容追加到 URL 中的公司密钥后面：`/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

    * 下面显示完整 URL 的架构：`https://na2.replicon.com/\<YourCompanyKey\>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

   b. 单击 **+** 展开 **v20Configuration** 部分。

   c. 单击 **+** 展开 **metaDataConfiguration** 部分。

   d. 选择 **SHA256** 作为 xmlSignatureAlgorithm

   e. 单击“选择文件”  ，选择标识提供者元数据 XML 文件，并单击“提交”  。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分中，将在 Azure 门户中创建一个名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”    。
1. 选择屏幕顶部的“新建用户”  。
1. 在“用户”属性中执行以下步骤  ：
   1. 在“名称”  字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension  。 例如，`BrittaSimon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。  
   1. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 B.Simon 访问 Replicon 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Replicon”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”    。

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，选择“用户”列表中的“B.Simon”，然后单击屏幕底部的“选择”按钮    。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮   。
1. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-replicon-test-user"></a>创建一个 Replicon 测试用户

本部分要在 Replicon 中创建名为“B.Simon”的用户。

如果需要手动创建用户，请执行以下步骤： 

1. 在 Web 浏览器窗口中，以管理员身份登录 Replicon 公司站点。

2. 转到“管理”\>“用户”  。

    ![用户](./media/replicon-tutorial/ic777806.png "用户")

3. 单击“+添加用户”  。

    ![添加用户](./media/replicon-tutorial/ic777807.png "添加用户")

4. 在“用户配置文件”  部分中，执行以下步骤：

    ![用户配置文件](./media/replicon-tutorial/ic777808.png "用户配置文件")

    a. 在“登录名”文本框中，键入要预配的 Azure AD 用户的 Azure AD 电子邮件地址，如 `B.Simon@contoso.com`  。

    > [!NOTE]
    > 登录名需要与 Azure AD 中用户的电子邮件地址匹配

    b. 对于“身份验证类型”  ，选择“SSO”  。

    c. 将身份验证 ID 设置为与登录名（用户的 Azure AD 电子邮件地址）相同的值

    d. 在“部门”文本框中，键入用户的部门  。

    e. 对于“员工类型”  ，选择“管理员”  。

    f. 单击“保存用户配置文件”  。

> [!NOTE]
> 可以使用任何其他 Replicon 用户帐户创建工具或 Replicon 提供的 API 来预配 Azure AD 用户帐户。

### <a name="test-sso"></a>测试 SSO

选择访问面板中的 Replicon 磁贴时，应当会自动登录到设置了 SSO 的 Replicon。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)