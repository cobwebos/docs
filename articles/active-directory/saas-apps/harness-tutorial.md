---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Harness 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Harness 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 82367f62-173e-4e14-bf84-d8f611706086
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21409eb056743d92db42e0787af24f8cec07db1b
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2019
ms.locfileid: "72026954"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-harness"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Harness 集成

本教程介绍如何将 Harness 与 Azure Active Directory (Azure AD) 集成。 将 Harness 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Harness。
* 让用户使用其 Azure AD 帐户自动登录到 Harness。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Harness 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Harness 支持 SP 和 IDP 发起的 SSO 

## <a name="adding-harness-from-the-gallery"></a>从库中添加 Harness

若要配置 Harness 与 Azure AD 的集成，需要从库中将 Harness 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Harness”   。
1. 从结果面板中选择“Harness”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-harness"></a>配置和测试 Harness 的 Azure AD 单一登录

使用名为 **B.Simon** 的测试用户配置和测试 Harness 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 Harness 相关用户之间建立链接关系。

若要配置和测试 Harness 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Harness SSO](#configure-harness-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Harness 测试用户](#create-harness-test-user)** - 在 Harness 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“Harness”应用程序集成页上，找到“管理”部分，选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在“IDP”发起的模式下配置应用程序，请在“基本 SAML 配置”部分中输入以下字段的值   ：

    在“回复 URL”文本框中，使用以下模式键入 URL：`https://app.harness.io/gateway/api/users/saml-login?accountId=<harness_account_id>` 

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”文本框中，键入 URL：`https://app.harness.io/` 

    > [!NOTE]
    > 答复 URL 值不是真实值。 你将从“配置 Harness SSO”  部分获得实际的答复 URL，本教程稍后将对此进行说明。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

1. 在“设置 Harness”部分中，根据要求复制相应的 URL  。

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

在本部分中，将通过授予 B.Simon 访问 Harness 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Harness”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-harness-sso"></a>配置 Harness SSO

1. 若要在 Harness 中自动执行配置，需要通过单击“安装扩展”  来安装**我的应用安全登录浏览器扩展**。

    ![我的应用扩展](common/install-myappssecure-extension.png)

2. 将扩展添加到浏览器后，单击“设置 Harness”  会将你定向到 Harness 应用程序。 在此处，提供管理员凭据以登录到 Harness。 浏览器扩展会自动配置该应用程序，并自动执行步骤 3-6。

    ![设置配置](common/setup-sso.png)

3. 若要手动设置 Harness，请打开新的 Web 浏览器窗口，以管理员身份登录 Harness 公司站点，并执行以下步骤：

4. 在页面右上角，单击“持续安全性”   > “访问管理”   > “身份验证设置”  。

    ![Harness 配置](./media/harness-tutorial/configure01.png)

5. 在“SSO 提供程序”  部分中，单击“+ 添加 SSO 提供程序”   > “SAML”  。

    ![Harness 配置](./media/harness-tutorial/configure03.png)

6. 在“SAML 提供程序”  弹出窗口中，执行以下步骤：

    ![Harness 配置](./media/harness-tutorial/configure02.png)

    a. 复制“在 SSO 提供程序中，请启用基于 SAML 的登录，然后输入以下 URL”  实例，并将其粘贴到 Azure 门户上“基本 SAML 配置”  部分的“答复 URL”文本框中。

    b. 在“显示名称”  文本框中，键入显示名称。

    c. 单击“选择文件”  以上传从 Azure AD 下载的“联合元数据 XML”文件。

    d. 单击“提交”  。

### <a name="create-harness-test-user"></a>创建 Harness 测试用户

要使 Azure AD 用户能够登录到 Harness，必须将其预配到 Harness 中。 在 Harness 中，预配是一项手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 Harness。

1. 在页面右上角，单击“持续安全性”   > “访问管理”   > “用户”  。

    ![Harness 配置](./media/harness-tutorial/configure04.png)

1. 在页面右侧，单击“+ 添加用户”。 

    ![Harness 配置](./media/harness-tutorial/configure05.png)

1. 在“添加用户”弹出窗口中，执行以下步骤  ：

    ![Harness 配置](./media/harness-tutorial/configure06.png)

    a. 在“电子邮件地址”文本框中，输入用户的电子邮件地址，例如 `B.simon@contoso.com`  。

    b. 选择用户组  。

    c. 单击“提交”  。

## <a name="test-sso"></a>测试 SSO 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Harness 磁贴时，应当会自动登录到设置了 SSO 的 Harness。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [通过 Azure AD 试用 Harness](https://aad.portal.azure.com/)

